## what is SAML
SAML (Security Assertion Markup Language) is a XML. 

## SAML has two types
- SAML request
- SAML response

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
https://github.com/onelogin/java-saml  
https://santuario.apache.org/Java/api/org/apache/xml/security/signature/XMLSignature.html  

## Shall the assertion inside SAML be encrypted
The assertion contains privacy data and it is suggested to encrypt the assertion.  
https://stackoverflow.com/questions/8276233/is-it-recommended-to-sign-and-encrypt-saml-and-use-ssl  

## online tools
https://www.samltool.com/sign_response.php
