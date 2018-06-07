# Spring Boot 

## Override default Spring-Boot application.properties settings in Junit Test
@TestPropertySource(locations="classpath:test.properties")
```
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = ExampleApplication.class)
@TestPropertySource(locations="classpath:test.properties")
public class ExampleApplicationTests {

}
```
https://stackoverflow.com/questions/29669393/override-default-spring-boot-application-properties-settings-in-junit-test

## spring boot junit test
http://www.baeldung.com/spring-boot-testing
https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html
http://site.mockito.org/

## spring boot junit test mock Spring Security Context 
```
	ApplicationUser applicationUser = mock(ApplicationUser.class);
    Authentication authentication = mock(Authentication.class);
    SecurityContext securityContext = mock(SecurityContext.class);
    when(securityContext.getAuthentication()).thenReturn(authentication);
    SecurityContextHolder.setContext(securityContext);
    when(SecurityContextHolder.getContext().getAuthentication().getPrincipal()).thenReturn(applicationUser);
```
https://aggarwalarpit.wordpress.com/2017/05/17/mocking-spring-security-context-for-unit-testing/

## spring boot junit test returning 401

The @WebMvcTest by default auto configure spring security if spring-security-test is present in the class path (which in my case is).

https://stackoverflow.com/questions/39554285/spring-test-returning-401-for-unsecured-urls

To avoid that, set secure=false
```
@WebMvcTest(controllers = DataController.class, secure=false)
public class DataControllerTest {
	...	
```

## spring boot and logback.xml
https://www.mkyong.com/spring-boot/spring-boot-slf4j-logging-example/


## spring boot Property in logback.xml
https://stackoverflow.com/questions/29322709/unable-to-use-spring-property-placeholders-in-logback-xml
```
<springProperty name="destination" source="my.loggger.extradest"/>
```

## spring cloud config
Here is the test URL
- /{application}/{profile}[/{label}]
- /{application}-{profile}.yml
- /{label}/{application}-{profile}.yml
- /{application}-{profile}.properties
- /{label}/{application}-{profile}.properties

Label is actually the git branch, referring to **spring.cloud.config.label** in application.properties  
My git repository for configuration is https://github.com/hairinwind/springCloud/tree/master/cloudConfig  
My properties file name is config-client-development.properties  
{application} is config-client  
{profile} is development  
so the visit URL will be  
```
http://localhost:8888/config-client/development/master
```
My git repository has a branch test, the URL below can get the properties from the test branch, no need to change the setting of the config server.
```
http://localhost:8888/config-client/development/test
```
- /{application}-{profile}.properties is 
```
http://localhost:8888/master/config-client-development.properties
```
The sample code git is here https://github.com/hairinwind/springCloud  

## configure two datasources
https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-two-datasources
mark one of the DataSource instances as @Primary
If it complains the bean cannot be found, you may need explicitly declare it, for example 
```
@Bean(name="ahaSettingDataSource")
...
@Qualifier("ahaSettingDataSource")
```


