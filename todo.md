
## sevlet code in spring boot 
https://www.codejava.net/java-ee/servlet/webfilter-annotation-examples  
https://www.baeldung.com/spring-servletcomponentscan

## filters regiested by registrationBean or not
It seems you don't have to regiest the bean by registrationBean. 
registrationBean pros can set urlPattern. 
cons: it seems it is after spring security authentication. Sometimes, I want some filters be executed before authentication.
https://www.baeldung.com/spring-boot-add-filter  
https://www.baeldung.com/spring-security-custom-filter

