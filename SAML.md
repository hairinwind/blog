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
