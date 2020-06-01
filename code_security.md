## Sql injection

Query: select * from users where name='<input param>'
If input param is abc'; delete from users where '1'='1, it injects another delete query
Select * from users where name='abc'; delete from users where '1'='1';

Solution 2
Don't compose sql string with parameter, use preparedStatement.setParameter instead
```
String query = "select * from users where name=?"
PreparedStatement statement = con.prepareStatement(query);
statement.setString(1, inputParam);
```

## Cross-Site Scripting (XSS) - Stored Cross-Site Scripting
```
<td>${feedback.username}</td>
<td>${feedback.email} </td>
<td>${feedback.feedback}</td>
```
User feedback is displayed in the admin feedback report without sanitization, leaving the application vulnerable to Stored XSS (and, worst of all, potentially hacking into an admin account).
Fix in java controller
```
feedback.setUsername(ESAPI.encoder().encodeForHTML(feedback.getUsername()));
feedback.setEmail(ESAPI.encoder().encodeForHTML(feedback.getEmail()));
feedback.setFeedback(ESAPI.encoder().encodeForHTML(feedback.getFeedback()));
```

## Cross-Site Scripting (XSS) - DOM-Based Cross-Site Scripting
An application can be exposed to DOM-based XSS if the client-side code uses unsafe methods to generate HTML content. In this scenario, innerHTML is used with the query string parameter values received from the GET request URL which could lead to XSS attack due to injected client-side JavaScript
```
postToDeleteInfo.innerHTML = postTitle;
```

Usually the issue is in javascript or html file. Content was inject into html
The display of a URL parameter without proper escape leads to reflected XSS vulnerability.
```
<input class="form-control" name="username" value="${param.username}"  />
```
Shall not use param directly
Solution: If possible, don't display URL parameters. If doing so, always use an HTML encoding/escaping library (for example, ${fn:escapeXml(...) from JSTL functions).

Posts stored in the database are rendered directly in the browser without any sanitization/encoding which leaves the application prone to Cross Site Scripting(XSS) attacks. Because the data could be a runnable javascript snippet. 

It is recommended by OWASP to escape/sanitize data that can be considered unsafe as it is rendered to avoid any XSS vulnerabilities. The output filters c:out and fn:escapeXml are used in appropriate contexts to escape/encode the title and the author name. To maintain XML validity, fn:escapeXml is used when attributes are concerned (and are properly enclosed inside double quotes), while c:out tags are used when rendering in an HTML context. The content of a blog post contains some safe HTML elements (from a WYSIWYG editor) which need to be preserved. Therefore, OWASP Java HTML Sanitizer is used to allow a safe subset of HTML tags based on a filtering policy. This ensures that application functionality is preserved while preventing any XSS exploits.

using textContent can prevent XSS attacks.
```
var profileToDeleteInfo = document.getElementById("profileToDelete-info");
profileToDeleteInfo.innerHTML = /*[[${name}]]*/;
//  vs
profileToDeleteInfo.textContent = /*[[${name}]]*/;  //this can prevent XSS attacks
```
Or in java class 
```
ESAPI.encoder().encodeForHTML(the_data_display_on_html)
```

Insecure use of c:import leads to remote-file-inclusion vulnerability.
```
<c:import url="${param.view}" />
```
Avoid to use param 
```
<c:import url="${param.view}" />
// vs
<jsp:include page="${user.role}_heading.jsp" />
```

## File path or path traversal
By manipulating variables that reference files with “dot-dot-slash (../)”
```
private void save(MultipartFile file, String path) {
        String realPathToUploads = request.getServletContext().getRealPath(path);
       // save file to that path
```
It allows user input to point to some system path which you don't want the user to save file to.  For example, the path is ../../../systemPath. 
Fix
```
File file = new File(userDir + "/src/main/resources/log/" + logFile).getCanonicalFile();  
File containerDirectory = new File(userDir + "/src/main/resources");
if (isInDirectory(containerDirectory, file)) {.  //need verify this 
	InputStream fileStream = new FileInputStream(file);
	IOUtils.copy(fileStream, response.getOutputStream());
	response.flushBuffer();
}
```
Or 
```
File file = new File(analysisBaseDir, filename);
if (!file.exists() || !file.getCanonicalPath().startsWith(analysisBaseDir)) {
    throw new NoSuchFileException(file.getAbsolutePath());
}
```


## Injection Flaws - XPath Injection
Search is user input parameter
```
String exprStr = "//"+domain+"/user[lastName/text()='"+search+"']";
```
Here is the solution
```
String sanitized = search.replace("'","");
String exprStr = String.format("//%s/user[lastName/text()='%s']",domain,sanitized);
XPathExpression expr = xpath.compile(exprStr);
```

## Injection Flaws - Deserialization of Untrusted Data
fix
The solution uses its own strong ObjectInputStream, which securely deserializes the untrusted data. The inputStream also validates deserialized object type. This is a good solution if the list of expected classes are small and can easily be identified.
```
@Override
    protected Class<?> resolveClass(ObjectStreamClass desc) throws IOException,
            ClassNotFoundException {
        if (!desc.getName().equals(EmployeeRequestVO.class.getName())) {
            throw new InvalidClassException(
                    "Unauthorized deserialization attempt",
                    desc.getName());
        }
        return super.resolveClass(desc);
    }
```
Vs
```
@Override
    protected Class<?> resolveClass(ObjectStreamClass desc) throws IOException,
            ClassNotFoundException {
        return super.resolveClass(desc);
    }

```
What is the differece?
The first one is the solution. The EmployeeObjectInputStream class is extended from the ObjectInputStream as it checks if the className is identical to the required className. If not, it throws an unauthorized exception, making this the recommended solution for preventing deserialization attacks.

## XML External Entities (XXE) - XML External Entities (XXE)
An XXE attack works by taking advantage of a feature in XML, namely XML eXternal Entities (XXE) that allows external XML resources to be loaded within an XML document.
documentBuilderFactory need set feature disallow-doctype-decl
```
DocumentBuilder safebuilder = documentBuilderFactory.newDocumentBuilder();
documentBuilderFactory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
OR
documentBuilderFactory.setFeature("http://xml.org/sax/features/external-general-entities", false);
documentBuilderFactory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
documentBuilderFactory.setFeature("http://apache.org/xml/features/nonvalidating/load-external-dtd", false);
documentBuilderFactory.setXIncludeAware(false);
documentBuilderFactory.setExpandEntityReferences(false);
```

The XML parser XMLInputFactory is used to parse XML. This doesn't provide the correct configuration option(s), which can leave the system open to a variety of attacks, for example, system file exposure, remote code execution, etc.
The XML parser options javax.xml.stream.isSupportingExternalEntities and XMLInputFactory.SUPPORT_DTD are set to false. These options disable inline DTD (Data Type Definition) processing. This prevents XXE (XML External Entity) attacks. The option javax.xml.stream.isSupportingExternalEntities is also implemented to prevent external parsed entities from being resolved. Lastly, XMLInputFactory.SUPPORT_DTD configures the parser in such a way that it doesn't support DTDs.
```
xmlInputFactory.setProperty(XMLInputFactory.SUPPORT_DTD, false);
xmlInputFactory.setProperty("javax.xml.stream.isSupportingExternalEntities", false);
```

## inproper authentication
The user who does not have admin access is allowed to visit admin pages. 

## Authentication - Insufficient Anti-Automation
Insufficient Anti-automation occurs when a web application permits an attacker to automate a process that was originally designed to be performed only in a manual fashion.
A simple example of Insufficient Anti-automation, is an application that allows users to view their account details, by directly accessing a URL similar to the following: 
http://www.some.site/app/accountDetails.aspx?UserID=XYZ
Where XYZ denotes an Account ID number.
If the application issues predictable (or enumerable) Account ID numbers, and also does not employ anti-automation mechanisms, an attacker could write an automated script, which would submit massive amounts of HTTP requests, each with a different Account ID number, and then harvest user account information from the response page.

Another example is "reset password". On the page to reset password, it shall add a step to check it is a real person, e.g. CAPTCHA

## Insecure Password Reset
	- Does reset email sent to the right email address? 
	- Can a password reset link be reused?
	- Does it last for more than 24 hours?
	- Can the link or token be predicted?
	- Is the previous password checked?
	- During password change, can the username be changed?
	- Are secret question answers guessable based on limited answers or through social media research?
	- Are passwords sent in cleartext to the user

## Insufficient Session Expiration
Web.xml session-timeout. 
session-timeout in web.xml was set to 0, leaving the session open indefinitely

The session isn't properly invalidated after logout. Only the client's cookie gets removed, which would still allow for a longshot of session hijacking.

## Session Handling - Weak Session Token Generation
The session token is generated by a synchronized sequential number generator (AtomicLong) making the session tokens not only predictable, but vulnerable to a sequential massive stealth attack through session hijacking.  
Base64 is not enought as well.  
Here is one implementation
```
private String generateSessionId() {
	try {
		byte[] bytes = new byte[64];
		SecureRandom.getInstance("SHA1PRNG").nextBytes(bytes);
		return new String(Base64.encode(bytes));
	}
	catch (NoSuchAlgorithmException nsae) {
		throw new RuntimeException("Error trying to generate session key.", nsae);
	}
}
```

---
Session Id shall not be exposed to URL.  
The solution allows the framework or the app server to manage access to the session. Sometimes no action is necessary.

## Authentication - Forceful Browsing
An attacker can use Brute Force techniques to search for unlinked contents in the domain directory, such as temporary directories and files, and old backup and configuration files.

## Insecure Cryptography - Weak Algorithm Use
Using the AES/GCM algorithm will protect sensitive data from theft.

## Insufficient Transport Layer Protection - Unprotected Transport of Sensitive Information
Using a database connection without encryption is a really bad practice. An attacker can use an attack like MITM to obtain sensitive data.
```
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST}:${DB_PORT}/${DB_NAME}
```
Solution
```
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST}:${DB_PORT}/${DB_NAME}?ssl=true&sslfactory=org.postgresql.ssl.DefaultJavaSSLFactory&sslmode=verify-full
```

## Sensitive Data Storage - Plaintext Storage of Sensitive Information
All sensitive data, such as national insurance numbers or salary information, must be stored in an encrypted form. AES/GCM/NoPadding is a secure encryption algorithm with the symmetric key being stored in a Java KeyStore. The password for this keystore is then kept in a separate file. It isn't recommended that the password is stored as a constant in the source code, because it might accidentally get pushed to a git repository and become present in the server's memory for a longer time.

## Access Control - Insecure Direct Object Reference
This vulnerability usually manifests when an input parameter is used to directly reference an object, file or other content but insufficient checks are performed to determine if the user is allowed to access the requested resource. For example, an ID value of an object may be accepted by the application and used to look up and display the data to the user. If the provided ID is not checked appropriately, it may be possible to guess valid IDs and access other users' data.

Review the input parameters used by the application and try to identify an instance that allows resources to be accessed without appropriate checks.

For example 
```
<a th:href="'/user-documents/document?id='+ ${document.key}">Link to document</a>
```
But server side does not check if the current user has the access to that document.

---
avoid writing files to diskes  
Here is an example to write file content directly to response instead of writing files on disks
```
response.setHeader("Content-Disposition", "attachment; filename="+reportFile);
try (Writer writer = response.getWriter()) {
	for (User user : users) {
		writer.write("\""+user.getUsername()+"\",");
		writer.write("\""+user.getFirstName()+"\",");
		writer.write("\""+user.getLastName()+"\"\n");
	}
}
```


## Unvalidated Redirects and Forwards - Unvalidated Redirects and Forwards
Find instances where a redirection or forward is performed by the application. This will usually manifest when redirecting a user after certain actions such as login, or when forwarding to a given view handler that could change dynamically based on the user role. Identify any external inputs used in the redirection or forward and check whether sufficient validation or sanitisation was performed to ensure it cannot be abused to redirect users to another site without warning, or to forward to unintended internal handlers.
For example, redirect URL shall not be a input parameter

## Access Control - Missing Function Level Access Control

## Security Misconfiguration - Information Exposure
```
<form role="form" name="form" action="/user/addPost" method="get">
```
Use "get" on form is a potential issue, all content will be added to URL parameters. Https cannot protect the body now.

## Security Misconfiguration - Disabled Security Features
The code below does not protect admin pages.
```
 http
                .authorizeRequests()
                .antMatchers("/**").permitAll()
                .antMatchers("/admin-page").hasRole("USER")
```
Shall be fixed like this
```
http
                .authorizeRequests()
                .antMatchers(
                        "/registration**",
                        "/forgot-password**",
                        "/reset-password**").permitAll()
                .antMatchers(
                        "/js/**",
                        "/css/**",
                        "/img/**",
                        "/webjars/**").permitAll()
                .anyRequest().authenticated()

```

JSESSIONID shall not be injected to URL
```
servletContext.setSessionTrackingModes(
                EnumSet.of(SessionTrackingMode.URL));
```
Shall be fixed like this
```
servletContext.setSessionTrackingModes(
                EnumSet.of(SessionTrackingMode.COOKIE));
```

## Security Misconfiguration - Debug Features Enabled
```
server.error.whitelabel.enabled=false
``` 
The application implements exception handling by means of outputting a page with the fields of the error name, status, and short message. The parameter server.error.whitelabel.enabled = true prevents a user from displaying a stack trace when an exception is received. All this implementation hides the internal structure of the application and does not allow an attacker to know what the application works with.

## Vulnerable Components - Using Components From Untrusted Source
```
Html 
<iframe id="info"…
Javascript 
$.ajax({
	url : "http://www.knowit.com/what-happens-when-i-give-my-credit-card-details-online/",
	success : function(data) {
		$("#info").attr("srcdoc", data);
	},

```
The ajax return is injected into iframe. It may contain malicious scripts. 
Fix 
```
<iframe id="info" sandbox seamless>
```
The use of the sandbox attribute in an iframe prevents srcdoc from executing scripts and submitting forms. Therefore, only static HTML content from the external source is rendered into the iframe in order to convey the required information to end users.

---
version 1.4.2 of the jQuery library is susceptible to XSS attacks.
```
<script type="text/javascript"
        src="https://code.jquery.com/jquery-1.4.2.min.js"
        integrity="sha256-4joqTi18K0Hrzdj/wGed9xQOt/UuHuur+CeogYJkPFk="
        crossorigin="anonymous"></script>
```
The solution is to use the latest verison of jquery, at that time it is 3.4.1.

---
Using the library from an untrusted source might make an application vulnerable to the various attacks. For example, a potential attacker can inject a spyware into the system.
Like this 
```
<script type="text/javascript"
        src="http:/js-library/bootstrap/3.3.7/bootstrap.min.js"></script>
```
Fix is 
```
<script type="text/javascript"
        th:src="@{/webjars/bootstrap/3.3.7/js/bootstrap.min.js}"></script>
```

## Insufficient Logging and Monitoring - Insufficient Logging and Monitoring
appenders=console
Console only is not enough 

## Cross Site Request Forgery
Cross-Site Request Forgery (CSRF) is an attack that forces an end user to execute unwanted actions on a web application. It has another name "fishing".  
Cookies aren't a safe repository for CSRF tokens.  
One fix is to compare the token in the session. 
code to create token in session
```
SecureRandom rand = new SecureRandom();
byte[] bytes = new byte{32};
rand.nextBytes(bytes);
final String token = tokenEncoder.encode(bytes.toString());
session.setAttribute(CSRF_TOKEN_SESSION_KEY, token);
```
Here is checking the token from request
```
final String found = request.getParameter(CSRF_TOKEN_REQUEST_KEY);
final String expected = (String) session.getAttribute(CSRF_TOKEN_SESSION_KEY);

if (found==null || !found.equals(expected)) {
    if (found==null) throw new SecurityException("No CSRF token was found in request parameter on CSRF protected resource.");
    throw new SecurityException("Possible CSRF attack identified. [found "+found+" , expected "+expected+"]" );
}
```
