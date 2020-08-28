## what is SAML
SAML (Security Assertion Markup Language) is a XML. 

## SAML has two types
- SAML request
    SAML request is simple. When the sp find the client request does not have SAML, it creates a SAML request in a from and return the from to browser and let browser to automatically submit the form to idp.```
    ```
      <samlp:AuthnRequest
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
        ID="aaf23196-1773-2113-474a-fe114412ab72"
        Version="2.0"
        IssueInstant="2004-12-05T09:21:59"
        AssertionConsumerServiceIndex="0"
        AttributeConsumingServiceIndex="0">
        <saml:Issuer>https://sp.example.com/SAML2</saml:Issuer>
        <samlp:NameIDPolicy
          AllowCreate="true"
          Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
      </samlp:AuthnRequest>
    ```
  
- SAML response
    idp provides SAML response after user successfully login. The SAML response contains assertion
    ```
    <saml:Assertion
       xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
       xmlns:xs="http://www.w3.org/2001/XMLSchema"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       ID="b07b804c-7c29-ea16-7300-4f3d6f7928ac"
       Version="2.0"
       IssueInstant="2004-12-05T09:22:05">
       <saml:Issuer>https://idp.example.org/SAML2</saml:Issuer>
       <ds:Signature
         xmlns:ds="http://www.w3.org/2000/09/xmldsig#">...</ds:Signature>
       <saml:Subject>
    ..........
       </saml:Subject>
       <saml:Conditions
    .........
       </saml:Conditions>
       <saml:AuthnStatement
         AuthnInstant="2004-12-05T09:22:00"
         SessionIndex="b07b804c-7c29-ea16-7300-4f3d6f7928ac">
         <saml:AuthnContext>
           <saml:AuthnContextClassRef>
             urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
          </saml:AuthnContextClassRef>
         </saml:AuthnContext>
       </saml:AuthnStatement>
       <saml:AttributeStatement>
         <saml:Attribute
           xmlns:x500="urn:oasis:names:tc:SAML:2.0:profiles:attribute:X500"
           x500:Encoding="LDAP"
           NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
           Name="urn:oid:1.3.6.1.4.1.5923.1.1.1.1"
           FriendlyName="eduPersonAffiliation">
           <saml:AttributeValue
             xsi:type="xs:string">member</saml:AttributeValue>
           <saml:AttributeValue
             xsi:type="xs:string">staff</saml:AttributeValue>
         </saml:Attribute>
       </saml:AttributeStatement>
     </saml:Assertion>
    ```

## understand SAML sso workflow
https://www.cnblogs.com/shuidao/p/3463947.html

## where is SAML in html
SAML is in a hidden field in form and the form is auto-submitted.
SAMLRequest example
```
<form method="post" action="https://idp.example.org/SAML2/SSO/POST" ...>
    <input type="hidden" name="SAMLRequest" value="<samlp:AuthnRequest>.......... </samlp:authnreques>" />
    ... other input parameter....
    <input type="submit" value="Submit" />

</form>

<javascript>
document.form[0].submit();// auto submit
</javascript>
```
SAMLResponse example
```
<form method="post" action="https://sp.example.com/SAML2/SSO/POST" ...>
    <input type="hidden" name="SAMLResponse" value="<samlp:Response>.........</samlp:respons>" />
    <input type="hidden" name="RelayState" value="''token''" />
    ...
    <input type="submit" value="Submit" />
</form>
<javascript>
document.form[0].submit();
</javascript>
```

## SAML signature  
To make sure the assertion integrity, the SAML assertion shall be signed.
This video explained the assertion signature. https://www.youtube.com/watch?v=UYQPkWDaHHM  
- Hash the <saml:assertion>...</saml:assertion> using SHA-1 to create the DigestValue (Base64 encoding step was omitted here)
- Insert <ds:Signature>...</ds:Signature> with the DigestValue created in the previous step into <saml:assertion>
- encrypted the whole <saml:assertion> and put the encrypted data into <ds:SignatureValue>

## java code to do the SAML signature
https://github.com/onelogin/java-saml  
https://santuario.apache.org/Java/api/org/apache/xml/security/signature/XMLSignature.html  

## Shall the assertion inside SAML be encrypted
The assertion contains privacy data and it is suggested to encrypt the assertion.  
https://stackoverflow.com/questions/8276233/is-it-recommended-to-sign-and-encrypt-saml-and-use-ssl  

## online tools
https://www.samltool.com/sign_response.php

## how is the big number be converted to the text in certificate
The certificate is the public key. We know the key is a big number. 
The text in the certificate is a base64 encoded text. It is encoded from DER, which is the binary serialization of ASN.1 format.  
https://www.shangyang.me/2017/05/24/encrypt-rsa-keyformat/  
Here is the online tool to convert the certifcate to the ASN.1
https://lapo.it/asn1js/  
After converting, you can see "INTEGER (1024 bit)" which is the big number. 
The number contains about 309 digits. 1024 is the binary bit, you can convert it to binary here https://codebeautify.org/decimal-binary-converter and the number after converting is 1024 bit.  
The java code to get publicKey from X509 certificate
```
    public static PublicKey getPublicKeyFromX509(InputStream x509InputStream)
			throws FileNotFoundException, CertificateException {
		X509Certificate x509 = X509Certificate.getInstance(x509InputStream);
		return x509.getPublicKey();
	}
```
Print the publicKey, the modulus is the big number in the certificate.


myJavaSaml
```
package my.javasaml;

import java.io.File;
import java.io.StringWriter;
import java.security.PrivateKey;
import java.security.cert.X509Certificate;

import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerException;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.TransformerFactoryConfigurationError;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
import javax.xml.xpath.XPathExpressionException;

import org.apache.commons.io.FileUtils;
import org.apache.commons.lang3.StringUtils;
import org.apache.xml.security.c14n.CanonicalizationException;
import org.apache.xml.security.c14n.Canonicalizer;
import org.apache.xml.security.c14n.InvalidCanonicalizerException;
import org.apache.xml.security.exceptions.XMLSecurityException;
import org.apache.xml.security.signature.XMLSignature;
import org.apache.xml.security.transforms.Transforms;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import com.onelogin.saml2.util.Constants;
import com.onelogin.saml2.util.Util;

public class myJavaSaml {

	public static void main(String[] args) throws Exception {
		String xmlPath = "saml_before_sign.xml";
		String samlXml = FileUtils.readFileToString(new File(xmlPath), "UTF-8");
		Document document = Util.loadXML(samlXml);

		String pubKeyBytes = "MIICRjCCAa+gAwIBAgIBADANBgkqhkiG9w0BAQ0FADBAMQswCQYDVQQGEwJjYTELMAkGA1UECAwCYmMxDzANBgNVBAoMBmJvbmJvbjETMBEGA1UEAwwKYm9uYm9tLmNvbTAeFw0yMDA4MjAwMDM1MzVaFw0yMTA4MjAwMDM1MzVaMEAxCzAJBgNVBAYTAmNhMQswCQYDVQQIDAJiYzEPMA0GA1UECgwGYm9uYm9uMRMwEQYDVQQDDApib25ib20uY29tMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDGi5pBQVB3hShfO6kdL2FFHoMCBGfgxEEiGrh+998LSlrpaEpAQLZTQ/E2JTQBQ5zCv5s+xztQKLMLfk1RbdJ+Jr+5+dMK8CEw9HO0MdntwBEl41IofUEHDyvWtLpsmYUpfVlQ8hH1zFfyza/e/FFuBCCgBM74jO2yXmV+UvGdiwIDAQABo1AwTjAdBgNVHQ4EFgQUmPw/1yl8PUrjZ+f+bbeeD/wupxcwHwYDVR0jBBgwFoAUmPw/1yl8PUrjZ+f+bbeeD/wupxcwDAYDVR0TBAUwAwEB/zANBgkqhkiG9w0BAQ0FAAOBgQBPnJCDQJtZCA0N3T1p1hqRK79r1vFfxzm0oMXQKQIlLMKc8X3L3bjLKnQTSc1BmZIhL7muF2A9gityYyyEfPoVUHe9jmHYtje5Txk+ZjqTTbdQhC/3BbeyHUV6OuTbp9NCVjcnTKIPoKwlGTz6R2diysVTjrBEgFCAxCT/j9E7OQ==";
		String fingerprint = "9b605c649708accb654e626c0dc6c78714c09225";
		String privKeyBytes = "MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAMaLmkFBUHeFKF87qR0vYUUegwIEZ+DEQSIauH733wtKWuloSkBAtlND8TYlNAFDnMK/mz7HO1Aoswt+TVFt0n4mv7n50wrwITD0c7Qx2e3AESXjUih9QQcPK9a0umyZhSl9WVDyEfXMV/LNr978UW4EIKAEzviM7bJeZX5S8Z2LAgMBAAECgYAqSG7IeOVvHr+TUHxah82aT74svs10k7cfUTt7ZsRsfiBQVvKBLoblwrFrC49Auap32yBvxTQ/hPtkdjh3IAFUllmz9ios8RlgsxBYbQ3QfivEWg7EuB6+7XVYtsiWu/joxoQmfxIE6Pjdd0NnSmgSxbyep2e3tbeUsMptTugaOQJBAOPpmevZn+upT20g/q/nWPpJNBCOIAw2+6TPCtmQ33XTQ4ln6swJnN38RGeO7kF1go0TjxdY/kxgLDSPtR7LPlUCQQDfA39q1mthFvvN/oKQfQkGVxzy3LUGIhvF2Py3iVekyvVHb/V81gkWms56EaIHFPqHAF3DEMhHyuhODvmCs4xfAkBnXl/tVHrvy462lCxvY6I7glAW4h8u6xEfIrhtDQQM0JDlFTis6f83v17XCUCOd9mKWsCfekF8KnJiNHuVR+ExAkEAqUymdhzA97vS/Naxl67UIxQOze/vHPDX1KKdd1e0SU4qVkkQO+zgiWCMTu8D6gxrBAGrLGKZKKdJI7kKgcZ8UQJBANoBQfqz4I/CVUJA6Cz1G7xPnQkW2BBNIr70s0FoU0LMdHZqyj0L7ZFm7WJo4qsUMVlUCtC10XHhySXO6IEvg4s=";
		// loads certificate and private key from string
		X509Certificate cert = Util.loadCert(pubKeyBytes);
		PrivateKey privateKey = Util.loadPrivateKey(privKeyBytes);

		// signs the response
		Node assertionNode = findFirstNode(document, "//saml:Assertion");
		
		String canonicalizedAssertion = canonicalize(assertionNode);
		System.out.println("\ncanonicalizedAssertion:\n" + canonicalizedAssertion + "\n");
		
		Document signedDocument = addSign(assertionNode, privateKey, cert, null);
		String signedDocumentText = Util.convertDocumentToString(signedDocument, true);
		System.out.println("signedResponse: " + signedDocumentText);
		
		//replace 
		String ecs4SamlPath = "/Users/45097077/projects/ecs/hbus-launchinggateway-external-config/src/main/resources/HBUSLaunchingGateway/DEV/eCS_FDR/ecs4_saml.xml";
		String ecs4Saml = FileUtils.readFileToString(new File(ecs4SamlPath));
		String signedSaml = StringUtils.replace(ecs4Saml, "ASSERTION_SECTION", signedDocumentText);
        System.out.println("\nafter sign: " + signedSaml);
        
		//load signed
		boolean validateResult = Util.validateSign(signedDocument, cert, fingerprint, Constants.RSA_SHA1, "//ds:Signature"); ///Response/ns2:Assertion/ds:Signature
		System.out.println("validate result: " + validateResult);
	}
	
	public static Node findFirstNode(Document document, String xpath) throws XPathExpressionException {
		NodeList nodes = Util.query(document, xpath);
		if (nodes.getLength() > 0) {
			return nodes.item(0);
		} else {
			throw new RuntimeException("node not found by " + xpath);
		}
	}
	
	public static Document addSign(Node node, PrivateKey key, X509Certificate certificate, String signAlgorithm) throws ParserConfigurationException, XPathExpressionException, XMLSecurityException, TransformerFactoryConfigurationError, TransformerException {
		// Check arguments.
		if (node == null) {
			throw new IllegalArgumentException("Provided node was null");
		}

		DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
	  	dbf.setNamespaceAware(true);
		Document doc = dbf.newDocumentBuilder().newDocument();
		Node newNode = doc.importNode(node, true);
		doc.appendChild(newNode);

		return addSign(doc, key, certificate, signAlgorithm);
	}
	
	public static Document addSign(Document document, PrivateKey key, X509Certificate certificate, String signAlgorithm) throws XMLSecurityException, XPathExpressionException, TransformerFactoryConfigurationError, TransformerException {
		org.apache.xml.security.Init.init();

		// Check arguments.
		if (document == null) {
			throw new IllegalArgumentException("Provided document was null");
		}

		if (document.getDocumentElement() == null) {
			throw new IllegalArgumentException("The Xml Document has no root element.");
		}

		if (key == null) {
			throw new IllegalArgumentException("Provided key was null");
		}
		
		if (certificate == null) {
			throw new IllegalArgumentException("Provided certificate was null");
		}

		if (signAlgorithm == null || signAlgorithm.isEmpty()) {
			signAlgorithm = Constants.RSA_SHA1;
		}

		// document.normalizeDocument();

		String c14nMethod = Constants.C14NEXC_WC;

		// Signature object
		XMLSignature sig = new XMLSignature(document, null, signAlgorithm, c14nMethod);

		// Including the signature into the document before sign, because
		// this is an envelop signature
		Element root = document.getDocumentElement();
		document.setXmlStandalone(false);		
		
		NodeList assertionNodes = root.getElementsByTagName("ns2:Assertion");
		Node assertionNode = root; 
		if (assertionNodes.getLength() > 0) {
			assertionNode = assertionNodes.item(0);
		}
		
		// If Issuer, locate Signature after Issuer, Otherwise as first child.
		NodeList subjectNodes = Util.query(document, "//saml:Subject", null);
		if (subjectNodes.getLength() > 0) {
			Node subject =  subjectNodes.item(0);
			
//						System.out.println("issue node: " + convertNodeToString(issuer));
			
			assertionNode.insertBefore(sig.getElement(), subject);
//						root.insertBefore(sig.getElement(), subject);
			
//						System.out.println("root after insert: " + convertNodeToString(root));
//						System.out.println("***");
		} else {
			root.insertBefore(sig.getElement(), root.getFirstChild());
		}

		String id = root.getAttribute("ID");

		String reference = id;
		if (!id.isEmpty()) {
			root.setIdAttributeNS(null, "ID", true);
			reference = "#" + id;
		}

		// Create the transform for the document
		Transforms transforms = new Transforms(document);
		transforms.addTransform(Constants.ENVSIG);
		//transforms.addTransform(Transforms.TRANSFORM_C14N_OMIT_COMMENTS);
		transforms.addTransform(c14nMethod);
		sig.addDocument(reference, transforms, Constants.SHA1);

		// Add the certification info
		sig.addKeyInfo(certificate);			

		// Sign the document
		sig.sign(key);

		// return Util.convertDocumentToString(document, true);
		return document;
	}
	
	public static String convertNodeToString(Node node) throws TransformerFactoryConfigurationError, TransformerException {
		StringWriter writer = new StringWriter();
		Transformer transformer = TransformerFactory.newInstance().newTransformer();
		transformer.transform(new DOMSource(node), new StreamResult(writer));
		String xml = writer.toString();
		return xml;
	}
	
	public static String canonicalize(Node xmlNode) throws InvalidCanonicalizerException, CanonicalizationException {
		org.apache.xml.security.Init.init(); // 
		Canonicalizer canon = Canonicalizer.getInstance(Canonicalizer.ALGO_ID_C14N_OMIT_COMMENTS); //Canonicalizer.ALGO_ID_C14N_OMIT_COMMENTS
		byte canonXmlBytes[] = canon.canonicalizeSubtree(xmlNode);
		String canonXmlString = new String(canonXmlBytes);
		return canonXmlString;
	}

}

```

digestUtils
```
package my.javasaml;

import java.io.File;
import java.io.IOException;
import java.io.StringWriter;
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

import javax.xml.parsers.ParserConfigurationException;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerConfigurationException;
import javax.xml.transform.TransformerException;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.TransformerFactoryConfigurationError;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

import org.apache.commons.codec.binary.Base64;
import org.apache.commons.io.FileUtils;
import org.apache.xml.security.c14n.CanonicalizationException;
import org.apache.xml.security.c14n.Canonicalizer;
import org.apache.xml.security.c14n.InvalidCanonicalizerException;
import org.w3c.dom.Document;
import org.w3c.dom.Node;
import org.xml.sax.SAXException;

import com.onelogin.saml2.util.Util;

public class DigestUtils {

	public static void main(String[] args) throws Exception {
		String xmlPath = "saml_before_sign.xml";
		String samlXml = FileUtils.readFileToString(new File(xmlPath), "UTF-8");
		Document document = Util.loadXML(samlXml);
		Node assertionNode = myJavaSaml.findFirstNode(document, "//saml:Assertion");
		
		System.out.println("\nDigest\n" + createDigestValue(assertionNode));
	}
	
	public static String createDigestValue(Node document) throws TransformerFactoryConfigurationError, TransformerException, InvalidCanonicalizerException, CanonicalizationException, ParserConfigurationException, IOException, SAXException, NoSuchAlgorithmException {
		// create the transformer in order to transform the document from
        // DOM Source as a JAVA document class, into a character stream (StreamResult) of
        // type String writer, in order to be converted to a string later on
//        TransformerFactory tf = new TransformerFactoryImpl(); //net.sf.saxon.
        Transformer transformer = TransformerFactory.newInstance().newTransformer();
        transformer.setOutputProperty(OutputKeys.OMIT_XML_DECLARATION, "yes");
        transformer.setOutputProperty(OutputKeys.METHOD, "xml");
        transformer.setOutputProperty(OutputKeys.ENCODING, "UTF-8");

        // create the string writer and transform the document to a character stream
        StringWriter sw = new StringWriter();
        transformer.transform(new DOMSource(document), new StreamResult(sw));

        String documentAsString = sw.toString();

        // initialize the XML security object, which is necessary to run the apache canonicalization
//        com.sun.org.apache.xml.internal.security.Init.init();
        org.apache.xml.security.Init.init();

        // canonicalize the document to a byte array and convert it to string
        Canonicalizer canon = Canonicalizer.getInstance(Canonicalizer.ALGO_ID_C14N_EXCL_WITH_COMMENTS);
        byte canonXmlBytes[] = canon.canonicalize(documentAsString.getBytes());
        String canonXmlString = new String(canonXmlBytes);
        
        System.out.println("canonXmlString\n" + canonXmlString);

        // get instance of the message digest based on the SHA-1 hashing algorithm
        MessageDigest digest = MessageDigest.getInstance("SHA-1");

        // call the digest method passing the byte stream on the text, this directly updates the message
        // being digested and perform the hashing
        byte[] hash = digest.digest(canonXmlString.getBytes(StandardCharsets.UTF_8));

        // encode the endresult byte hash
        byte[] encodedBytes = Base64.encodeBase64(hash);

        return new String(encodedBytes);
	}
}
```

mySamlValidator
```
package my.javasaml;

import java.io.File;
import java.security.PrivateKey;
import java.security.cert.X509Certificate;

import org.apache.commons.io.FileUtils;
import org.w3c.dom.Document;

import com.onelogin.saml2.util.Constants;
import com.onelogin.saml2.util.Util;

public class MySamlValiator {
	
	public static void main(String[] args) throws Exception {
		String xmlPath = "samlSignedByOneTool.xml";
		String samlXml = FileUtils.readFileToString(new File(xmlPath), "UTF-8");
		Document document = Util.loadXML(samlXml);

		String pubKeyBytes = "MIICRjCCAa+gAwIBAgIBADANBgkqhkiG9w0BAQ0FADBAMQswCQYDVQQGEwJjYTELMAkGA1UECAwCYmMxDzANBgNVBAoMBmJvbmJvbjETMBEGA1UEAwwKYm9uYm9tLmNvbTAeFw0yMDA4MjAwMDM1MzVaFw0yMTA4MjAwMDM1MzVaMEAxCzAJBgNVBAYTAmNhMQswCQYDVQQIDAJiYzEPMA0GA1UECgwGYm9uYm9uMRMwEQYDVQQDDApib25ib20uY29tMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDGi5pBQVB3hShfO6kdL2FFHoMCBGfgxEEiGrh+998LSlrpaEpAQLZTQ/E2JTQBQ5zCv5s+xztQKLMLfk1RbdJ+Jr+5+dMK8CEw9HO0MdntwBEl41IofUEHDyvWtLpsmYUpfVlQ8hH1zFfyza/e/FFuBCCgBM74jO2yXmV+UvGdiwIDAQABo1AwTjAdBgNVHQ4EFgQUmPw/1yl8PUrjZ+f+bbeeD/wupxcwHwYDVR0jBBgwFoAUmPw/1yl8PUrjZ+f+bbeeD/wupxcwDAYDVR0TBAUwAwEB/zANBgkqhkiG9w0BAQ0FAAOBgQBPnJCDQJtZCA0N3T1p1hqRK79r1vFfxzm0oMXQKQIlLMKc8X3L3bjLKnQTSc1BmZIhL7muF2A9gityYyyEfPoVUHe9jmHYtje5Txk+ZjqTTbdQhC/3BbeyHUV6OuTbp9NCVjcnTKIPoKwlGTz6R2diysVTjrBEgFCAxCT/j9E7OQ==";
		String fingerprint = "9b605c649708accb654e626c0dc6c78714c09225";
		String privKeyBytes = "MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAMaLmkFBUHeFKF87qR0vYUUegwIEZ+DEQSIauH733wtKWuloSkBAtlND8TYlNAFDnMK/mz7HO1Aoswt+TVFt0n4mv7n50wrwITD0c7Qx2e3AESXjUih9QQcPK9a0umyZhSl9WVDyEfXMV/LNr978UW4EIKAEzviM7bJeZX5S8Z2LAgMBAAECgYAqSG7IeOVvHr+TUHxah82aT74svs10k7cfUTt7ZsRsfiBQVvKBLoblwrFrC49Auap32yBvxTQ/hPtkdjh3IAFUllmz9ios8RlgsxBYbQ3QfivEWg7EuB6+7XVYtsiWu/joxoQmfxIE6Pjdd0NnSmgSxbyep2e3tbeUsMptTugaOQJBAOPpmevZn+upT20g/q/nWPpJNBCOIAw2+6TPCtmQ33XTQ4ln6swJnN38RGeO7kF1go0TjxdY/kxgLDSPtR7LPlUCQQDfA39q1mthFvvN/oKQfQkGVxzy3LUGIhvF2Py3iVekyvVHb/V81gkWms56EaIHFPqHAF3DEMhHyuhODvmCs4xfAkBnXl/tVHrvy462lCxvY6I7glAW4h8u6xEfIrhtDQQM0JDlFTis6f83v17XCUCOd9mKWsCfekF8KnJiNHuVR+ExAkEAqUymdhzA97vS/Naxl67UIxQOze/vHPDX1KKdd1e0SU4qVkkQO+zgiWCMTu8D6gxrBAGrLGKZKKdJI7kKgcZ8UQJBANoBQfqz4I/CVUJA6Cz1G7xPnQkW2BBNIr70s0FoU0LMdHZqyj0L7ZFm7WJo4qsUMVlUCtC10XHhySXO6IEvg4s=";
		// loads certificate and private key from string
		X509Certificate cert = Util.loadCert(pubKeyBytes);
		PrivateKey privateKey = Util.loadPrivateKey(privKeyBytes);
		
		boolean validateResult = Util.validateSign(document, cert, fingerprint, Constants.RSA_SHA1, "//ds:Signature"); ///Response/ns2:Assertion/ds:Signature
		System.out.println("validate result: " + validateResult);
	}

}
```

RSAUtil
```
package my.javasaml;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStream;
import java.lang.reflect.InvocationTargetException;
import java.security.InvalidKeyException;
import java.security.KeyFactory;
import java.security.NoSuchAlgorithmException;
import java.security.PrivateKey;
import java.security.Provider;
import java.security.PublicKey;
import java.security.Security;
import java.security.cert.CertificateFactory;
import java.security.cert.CertificateFactorySpi;
import java.security.spec.InvalidKeySpecException;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;

import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import javax.security.cert.CertificateException;
import javax.security.cert.X509Certificate;

import org.apache.commons.io.FileUtils;

public class RSAUtil {

	private static String publicKey = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCgFGVfrY4jQSoZQWWygZ83roKXWD4YeT2x2p41dGkPixe73rT2IW04glagN2vgoZoHuOPqa5and6kAmK2ujmCHu6D1auJhE2tXP+yLkpSiYMQucDKmCsWMnW9XlC5K7OSL77TXXcfvTvyZcjObEz6LIBRzs6+FqpFbUO9SJEfh6wIDAQAB";
	private static String privateKey = "MIICdQIBADANBgkqhkiG9w0BAQEFAASCAl8wggJbAgEAAoGBAKAUZV+tjiNBKhlBZbKBnzeugpdYPhh5PbHanjV0aQ+LF7vetPYhbTiCVqA3a+Chmge44+prlqd3qQCYra6OYIe7oPVq4mETa1c/7IuSlKJgxC5wMqYKxYydb1eULkrs5IvvtNddx+9O/JlyM5sTPosgFHOzr4WqkVtQ71IkR+HrAgMBAAECgYAkQLo8kteP0GAyXAcmCAkA2Tql/8wASuTX9ITD4lsws/VqDKO64hMUKyBnJGX/91kkypCDNF5oCsdxZSJgV8owViYWZPnbvEcNqLtqgs7nj1UHuX9S5yYIPGN/mHL6OJJ7sosOd6rqdpg6JRRkAKUV+tmN/7Gh0+GFXM+ug6mgwQJBAO9/+CWpCAVoGxCA+YsTMb82fTOmGYMkZOAfQsvIV2v6DC8eJrSa+c0yCOTa3tirlCkhBfB08f8U2iEPS+Gu3bECQQCrG7O0gYmFL2RX1O+37ovyyHTbst4s4xbLW4jLzbSoimL235lCdIC+fllEEP96wPAiqo6dzmdH8KsGmVozsVRbAkB0ME8AZjp/9Pt8TDXD5LHzo8mlruUdnCBcIo5TMoRG2+3hRe1dHPonNCjgbdZCoyqjsWOiPfnQ2Brigvs7J4xhAkBGRiZUKC92x7QKbqXVgN9xYuq7oIanIM0nz/wq190uq0dh5Qtow7hshC/dSK3kmIEHe8z++tpoLWvQVgM538apAkBoSNfaTkDZhFavuiVl6L8cWCoDcJBItip8wKQhXwHp0O3HLg10OEd14M58ooNfpgt+8D8/8/2OOFaR0HzA+2Dm";

	public static PublicKey getPublicKey(String base64PublicKey) {
		PublicKey publicKey = null;
		try {
			X509EncodedKeySpec keySpec = new X509EncodedKeySpec(Base64.getDecoder().decode(base64PublicKey.getBytes()));
			KeyFactory keyFactory = KeyFactory.getInstance("RSA");
			publicKey = keyFactory.generatePublic(keySpec);
			return publicKey;
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
		} catch (InvalidKeySpecException e) {
			e.printStackTrace();
		}
		return publicKey;
	}

	public static PrivateKey getPrivateKey(String base64PrivateKey) {
		PrivateKey privateKey = null;
		PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(Base64.getDecoder().decode(base64PrivateKey.getBytes()));
		KeyFactory keyFactory = null;
		try {
			keyFactory = KeyFactory.getInstance("RSA");
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
		}
		try {
			privateKey = keyFactory.generatePrivate(keySpec);
		} catch (InvalidKeySpecException e) {
			e.printStackTrace();
		}
		return privateKey;
	}

	public static byte[] encrypt(String data, String publicKey) throws BadPaddingException, IllegalBlockSizeException,
			InvalidKeyException, NoSuchPaddingException, NoSuchAlgorithmException {
		Cipher cipher = Cipher.getInstance("RSA/ECB/PKCS1Padding");
		cipher.init(Cipher.ENCRYPT_MODE, getPublicKey(publicKey));
		return cipher.doFinal(data.getBytes());
	}
	
	public static byte[] encrypt(byte[] data, PublicKey publicKey) throws BadPaddingException, IllegalBlockSizeException,
		InvalidKeyException, NoSuchPaddingException, NoSuchAlgorithmException {
		Cipher cipher = Cipher.getInstance("RSA/ECB/PKCS1Padding");
		cipher.init(Cipher.ENCRYPT_MODE, publicKey);
		return cipher.doFinal(data);
	}

	public static byte[] encrypt(String data, PublicKey publicKey) throws BadPaddingException,
			IllegalBlockSizeException, InvalidKeyException, NoSuchPaddingException, NoSuchAlgorithmException {
		Cipher cipher = Cipher.getInstance("RSA/ECB/PKCS1Padding");
		cipher.init(Cipher.ENCRYPT_MODE, publicKey);
		return cipher.doFinal(data.getBytes());
	}

	public static String decrypt(byte[] data, PrivateKey privateKey) throws NoSuchPaddingException,
			NoSuchAlgorithmException, InvalidKeyException, BadPaddingException, IllegalBlockSizeException {
		Cipher cipher = Cipher.getInstance("RSA/ECB/PKCS1Padding");  //RSA/ECB/PKCS1Padding
		cipher.init(Cipher.DECRYPT_MODE, privateKey);
		return new String(cipher.doFinal(data));
	}

	public static String decrypt(String data, String base64PrivateKey) throws IllegalBlockSizeException,
			InvalidKeyException, BadPaddingException, NoSuchAlgorithmException, NoSuchPaddingException {
		return decrypt(Base64.getDecoder().decode(data.getBytes()), getPrivateKey(base64PrivateKey));
	}

	public static String decrypt1(String data, String base64PrivateKey) throws IllegalBlockSizeException,
			InvalidKeyException, BadPaddingException, NoSuchAlgorithmException, NoSuchPaddingException {
		return decrypt(data.getBytes(), getPrivateKey(base64PrivateKey));
	}

	public static PublicKey getPublicKeyFromX509(InputStream x509InputStream)
			throws FileNotFoundException, CertificateException {
		X509Certificate x509 = X509Certificate.getInstance(x509InputStream);
		return x509.getPublicKey();
	}

	/**
	 * @param args
	 * @throws Exception
	 */
	public static void main(String[] args) throws Exception {
//		try {
//			String encryptedString = Base64.getEncoder().encodeToString(encrypt("abc", publicKey));
//			System.out.println(encryptedString);
//			String decryptedString = RSAUtil.decrypt(encryptedString, privateKey);
//			System.out.println(decryptedString);
//		} catch (NoSuchAlgorithmException e) {
//			System.err.println(e.getMessage());
//		}

		PublicKey publicKey = getPublicKeyFromX509(new FileInputStream("/Users/45097077/Downloads/mycert/mycert.crt"));
//		String privateKey = "MIICdwIBADANBgkqhkiG9w0BAQEFAASCAmEwggJdAgEAAoGBAMaLmkFBUHeFKF87qR0vYUUegwIEZ+DEQSIauH733wtKWuloSkBAtlND8TYlNAFDnMK/mz7HO1Aoswt+TVFt0n4mv7n50wrwITD0c7Qx2e3AESXjUih9QQcPK9a0umyZhSl9WVDyEfXMV/LNr978UW4EIKAEzviM7bJeZX5S8Z2LAgMBAAECgYAqSG7IeOVvHr+TUHxah82aT74svs10k7cfUTt7ZsRsfiBQVvKBLoblwrFrC49Auap32yBvxTQ/hPtkdjh3IAFUllmz9ios8RlgsxBYbQ3QfivEWg7EuB6+7XVYtsiWu/joxoQmfxIE6Pjdd0NnSmgSxbyep2e3tbeUsMptTugaOQJBAOPpmevZn+upT20g/q/nWPpJNBCOIAw2+6TPCtmQ33XTQ4ln6swJnN38RGeO7kF1go0TjxdY/kxgLDSPtR7LPlUCQQDfA39q1mthFvvN/oKQfQkGVxzy3LUGIhvF2Py3iVekyvVHb/V81gkWms56EaIHFPqHAF3DEMhHyuhODvmCs4xfAkBnXl/tVHrvy462lCxvY6I7glAW4h8u6xEfIrhtDQQM0JDlFTis6f83v17XCUCOd9mKWsCfekF8KnJiNHuVR+ExAkEAqUymdhzA97vS/Naxl67UIxQOze/vHPDX1KKdd1e0SU4qVkkQO+zgiWCMTu8D6gxrBAGrLGKZKKdJI7kKgcZ8UQJBANoBQfqz4I/CVUJA6Cz1G7xPnQkW2BBNIr70s0FoU0LMdHZqyj0L7ZFm7WJo4qsUMVlUCtC10XHhySXO6IEvg4s=";
//		System.out.println(publicKey);
//		
//		String data = "abc";
//		System.out.println("\nThe text to encrypt: " + data);
//		String encryptedString = Base64.getEncoder().encodeToString(encrypt(data, publicKey));
//		System.out.println("The encrypted text: " + encryptedString);
//		String decryptedString = RSAUtil.decrypt(encryptedString, privateKey);
//		System.out.println("The text decrypted: " + decryptedString);
//
//		String encryptedString1 = "wyUUOhp/+fa+MZSoAuYZbcMHQzHPDnR9wFq2isph6EBQ323enAzz6bBFu/O5MrV5qZmQfibM3Rh/eAoWX5MgUpGFSDcyG5oajmEflxy0+fIt1YdEUmetf7KigKW/zQunnCkBtlkTtUmc1oc4CFdN8HR49kH6rd8kGRtrd1EpKpE=";
//		String decryptedString1 = RSAUtil.decrypt(encryptedString1, privateKey);
//		System.out.println(decryptedString1);
		
		//encrypt binary files
		byte[] fileByteArray = FileUtils.readFileToByteArray(new File("/Users/45097077/projects/ecs/myjavasaml/javasaml.zip"));
		System.out.println(fileByteArray.length);
		
		String encryptedString = Base64.getEncoder().encodeToString(encrypt(fileByteArray, publicKey));
		System.out.println(encryptedString);
	}
}
```
