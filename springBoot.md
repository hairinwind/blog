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

## be careful about the location of the SpringBootApplication class
I used to put the SpringBootApplication class into a package. All the controllers and services not under that package stop working. The SpringBootApplication class scans the controllers and services in its package or sub-packages. 

 ## @ConditionalOnProperty or @ConditionalOnExpression
conditional create the bean when the property value matches
 ```
 @Bean(name="processLines")
    @ConditionalOnProperty(
            value="inbound.filename.type",
            havingValue = "decm-customers-rules",
            matchIfMissing = false)
public Step processCustomerRuleLines(...) {
    ...
}
```
conditional create the bean when the expression is satisfied 
```
@Bean(name="processLines")
    @ConditionalOnExpression("'${inbound.filename.type}'.equals('AFT') || '${inbound.filename.type}'.equals('BILLBATCH')")
public Step processTransactionLines(...) {
    ...
}
```

## @ConfigurationProperties
This is an alternative of @Value on each properties. 
It can map the properties in a java class with the prefix.
https://www.baeldung.com/configuration-properties-in-spring-boot  
https://www.concretepage.com/spring-5/spring-value

properties file is like this 
```
#Simple properties
mail.hostname=host@mail.com
mail.port=9000
mail.from=mailer@mail.com
```
The java class is like this
```
@Configuration
@PropertySource("classpath:configprops.properties")
@ConfigurationProperties(prefix = "mail")
public class ConfigProperties {
     
    private String hostName; //this will take the property mail.hostname
    private int port;
    private String from;
 
    // standard getters and setters
}
```
https://reflectoring.io/spring-boot-conditionals/

## @Value
https://www.baeldung.com/spring-value-annotation
```
@Value("${value.from.file}")
private String valueFromFile;
```
With default value
```
@Value("${unknown.param:some default}")
private String someDefault;
```
With empty default value, someDefault will be empty string if unknown.param cannot be found.
```
@Value("${unknown.param:}")
private String someDefault;
```
split
```
@Value("#{'${listOfValues}'.split(',')}")
private List<String> valuesList;
```
split with default empty value 
```
@Value("#{'${some.key:}'.split(',')}")
Set<String> someKeySet;
```
@Value with Maps
```
# properties file 
valuesMap={key1: '1', key2: '2', key3: '3'}

// Java 
@Value("#{${valuesMap}}")
private Map<String, Integer> valuesMap;
```

## Spring Boot - Generating Random Properties
to generate a random property in property file
```
#random int with max
app.user-age=${random.int(100)}

#random int range
app.max-users=${random.int[1,10000]}

#random long with max
app.refresh-rate-milli=${random.long(1000000)}

#random long range
app.initial-delay-milli=${random.long[100,90000000000000000]}
```
https://www.logicbig.com/tutorials/spring-framework/spring-boot/random-properties.html

## Circular Dependencies
https://www.baeldung.com/circular-dependencies-in-spring

## @Autowired on property vs @Autowired on set method
We can put @Autowired on property
```
@Autowired
private ServiceA serviceA;
```
This actually adds serviceA as an argument of the constructor.

Or 
```
private ServiceA serviceA;
@Autowired
public void setServiceA(...) {
    //set ...
}
```
This actually injects the bean when it needed. The previous one may have the issue that when the bean is initialized the injected bean is not ready yet.   
https://www.baeldung.com/circular-dependencies-in-spring

## spring unit test dirtyContext
DirtiesContext.classMode  
https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/annotation/DirtiesContext.ClassMode.html

## spring boot display sql with argument
put this in pom
```
<dependency>
    <groupId>com.integralblue</groupId>
    <artifactId>log4jdbc-spring-boot-starter</artifactId>
    <version>1.0.2</version>
</dependency>	
```
https://github.com/candrews/log4jdbc-spring-boot-starter  
To disable some logging, put this in application.properties
```
logging.level.jdbc.sqlonly=INFO
logging.level.jdbc.sqltiming=WARN
logging.level.jdbc.audit=WARN
logging.level.jdbc.resultset=WARN
logging.level.jdbc.resultsettable=WARN
logging.level.jdbc.connection=WARN
```
I hope it could filter the sql by table name. For example I don't want to see the sql to spring batch tables.  There is one option "log4jdbc.debug.stack.prefix", but seems not working for me.

To filter some messages, add janino dependency in pom.xml
```
<dependency>
    <groupId>org.codehaus.janino</groupId>
    <artifactId>janino</artifactId>
    <version>3.0.15</version>
</dependency>
```
logback.xml, I don't want to see two polling sql statements.
```
<appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
    <filter class="ch.qos.logback.core.filter.EvaluatorFilter">
        <evaluator>
            <matcher>
                <Name>DECM_INIT_polling_query</Name>
                <!-- filter out odd numbered statements -->
                <regex>SELECT \* FROM DECM_INIT WHERE ATTR_DOMAIN='DATE_PROC'</regex>
            </matcher>
            <expression>DECM_INIT_polling_query.matches(formattedMessage)</expression>
        </evaluator>
        <OnMismatch>NEUTRAL</OnMismatch>
        <OnMatch>DENY</OnMatch>
    </filter>
    <filter class="ch.qos.logback.core.filter.EvaluatorFilter">
        <evaluator>
            <matcher>
                <Name>TXNS_polling_query</Name>
                <!-- filter out odd numbered statements -->
                <regex>SELECT TOP(100) \* FROM TXNS WHERE PROCESSED_TS IS NULL and STATUS IN ('PROC','STOP','DIRTY','REJ') order by TSEQ</regex>
            </matcher>
            <expression>TXNS_polling_query.matches(formattedMessage)</expression>
        </evaluator>
        <OnMismatch>NEUTRAL</OnMismatch>
        <OnMatch>DENY</OnMatch>
    </filter>
    <encoder>
        <pattern>%-4relative [%thread] %-5level %logger - %msg%n</pattern>
    </encoder>
</appender>
```
Here is one filter to only show log with specific text, e.g. accountNumber, mutliple threads may access the same account, outputting the actions only related to that account is helpful to detect the issue.
```
<filter class="ch.qos.logback.core.filter.EvaluatorFilter">
    <evaluator>
        <matcher>
            <Name>redis_log</Name>
            <regex>82838482</regex>
        </matcher>
        <expression>redis_log.matches(formattedMessage)</expression>
    </evaluator>
    <OnMismatch>DENY</OnMismatch>
    <OnMatch>ACCEPT</OnMatch>
</filter>
```

## spring override property at runtime
In one of my test, I need start the activmq. To avoid the port competition on the build server (multiple builds from different branches could be triggered at the same moement), I want the activemq broker is started on different port.

Here is the code in BeforeClass
```
    public static String BROKER_ADDRESS = "tcp://localhost:" + (new Random().nextInt(89999)+10000);
    
    @BeforeClass
    public static void startupActiveMq() throws Exception {
        //set the dynamic port URL into system property and jmsTemplate can get that
        System.setProperty("spring.activemq.broker-url", BROKER_ADDRESS);

        BROKER = new BrokerService();
        BROKER.addConnector(new URI(BROKER_ADDRESS));
        BROKER.setPersistent(false);
        BROKER.setUseJmx(false);

        BROKER.start();
    }
``` 
The BeforeClass is a static method. So, it cannot get the spring property value. I let the BROKER_ADDRESS start on a random port. Then use System.setProperty "spring.activemq.broker-url" to let the jmsTemplate listen on the port randomly generated. 

https://stackoverflow.com/questions/17353327/populating-spring-value-during-unit-test

## actuator
https://www.callicoder.com/spring-boot-actuator/  
https://www.callicoder.com/spring-boot-actuator-metrics-monitoring-dashboard-prometheus-grafana/  
https://docs.spring.io/spring-boot/docs/2.0.x/actuator-api/html/  
```
management.endpoints.web.exposure.include=* 
management.endpoints.web.exposure.exclude=
management.endpoint.health.show-details=always
```

## disable error page 
```
server.error.whitelabel.enabled=false
```

## activemq trust all package
http://activemq.apache.org/objectmessage.html
The error is like 
```
Caused by: java.lang.ClassNotFoundException: Forbidden class com.central1.psa.core.paymentmessage.shared.dto.impl.PaymentMessageTraceEventImpl! This class is not trusted to be serialized as ObjectMessage payload. Please take a look at http://activemq.apache.org/objectmessage.html for more information on how to configure trusted classes.
```
It needs set the activeMq factory trust packages. 
```
factory.setTrustedPackages(new ArrayList(Arrays.asList("org.apache.activemq.test,org.apache.camel.test".split(","))));
or 
factory.setTrustAllPackages(true);
```
In spring boot, we can do it through properties
```
spring.activemq.packages.trusted=<package1>,<package2>,<package3>
or 
spring.activemq.packages.trust-all=true
```

## activemq fail over url configuration
```
broker1=tcp://Vahclq01pss123:61616
broker2=tcp://Vahclq01pss:61616
spring.activemq.broker-url=failover:(${broker1},${broker2})?randomize=false&priorityBackup=true
```

## spring boot custom error page with images
Here is the file structure.

![markdown image](/images/springbootErrorPageImage.PNG "spring boot file structure")

The "static" is tricky here. In the 404.html, we shall only have 
```
<img src="/images/logo.png" alt="logo">
``` 
Any files/folders under static folder are in the root of the web context. If you run the spring boot project, you can find the image by the url http://your_springboot_url/images/logo.png

## spring externalization of property file 
put in this command line argument
```
java -jar ... --spring.config.name=<the property file name, with out properties suffix>
```
or set up the env variable
```
export SPRING_CONFIG_NAME = ...
```

## Mockito.mock() vs @Mock vs @MockBean
https://www.baeldung.com/java-spring-mockito-mock-mockbean

Mockito.mock() and @Mock are the same.  
@MockBean adds the mock objects to spring application context.

## spring batch sql server dead lock
I have mutliple spring batch instances which are sharing the same mssql database. When they start creating jobExcecution at the same moment. They are competing on the id of the table. I saw "deadlock" error and "Could not increment identity".  
The reason is that spring is not using SQL server IDENTITY as they are not supported before SQL server 2012.  
Eventually the solution is factory.setIsolationLevelForCreate(ISOLATION_REPEATABLE_READ);  
```
    private static final String ISOLATION_REPEATABLE_READ = "ISOLATION_REPEATABLE_READ";

    @Autowired
    private DataSource dataSource;

    @Autowired
    private PlatformTransactionManager platformTransactionManager;

    @Bean
    public JobRepository jobRepository() throws Exception {
        JobRepositoryFactoryBean factory = new JobRepositoryFactoryBean();
        factory.setDataSource(dataSource);
        factory.setTransactionManager(platformTransactionManager);
        factory.setValidateTransactionState(true);
        factory.setIsolationLevelForCreate(ISOLATION_REPEATABLE_READ);
        factory.afterPropertiesSet();
        return factory.getObject();
    }
```
That helps a bit. But it does not get rid of dead lock. I have to add AOP around advice to do the retry if deadlock happens. 
```
@Aspect
@Component
public class MyAspect {

    private static final Logger log = LoggerFactory.getLogger(MyAspect.class);

    @Around(value = "execution(* org.springframework.batch.core.repository.JobRepository.createJobExecution(..)) and args(jobName, jobParameters)")
    public Object aroundCreateJob(ProceedingJoinPoint joinPoint, String jobName, JobParameters jobParameters) throws Throwable {
        int maxRetry = 5;
        int retryCount = 0;
        while (retryCount < maxRetry) {
            try {
                Object retVal = joinPoint.proceed(new Object[]{jobName, jobParameters});
                return retVal;
            } catch (Throwable e) {
                retryCount ++;
                if (retryCount > maxRetry) {
                    throw e;
                } else {
                    log.warn("...error when creating jobExecution \n{}\n{}\n{}", jobName, jobParameters, e.getMessage());
                }
            }
            try {
                Thread.sleep(1000 * retryCount);
                log.warn("...sleep {} seconds before recreate jobExecution", retryCount);
            } catch (Exception e) {}
        }
        return null;
    }
}
``` 

