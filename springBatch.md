## spring batch sql server dead lock
I have mutliple spring batch instances which are sharing the same mssql database. When they start creating jobExcecution at the same moment. They are competing on the id of the table. I saw "deadlock" error and "Could not increment identity".  
The reason is that spring is not using SQL server IDENTITY as they are not supported before SQL server 2012.  
Eventually the solution is factory.setIsolationLevelForCreate(ISOLATION_REPEATABLE_READ)  
https://github.com/spring-projects/spring-framework/issues/21425
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

## share data between steps 
use either stepExecutionContext or jobExecutionContext  
https://stackoverflow.com/questions/2292667/how-can-we-share-data-between-the-different-steps-of-a-job-in-spring-batch

## spring batch JobExecutionDecider
If there are multiple steps in spring batch job, let's say the second step may not run depending on the result of the first step. JobExecutionDecider shall be used to do that work.
This job contains two steps. The first one is doing the validation. if there is validation error, the error is put into stepExecutionContext. (Actually, this job can be done in one step. Let the processor do validation.)
```
Job job = jobBuilderFactory.get("mappingImportJob")
                .listener(jobCompletionNotificationListener)
                .flow(validateMappingLines)
                .next(writerDecide).on("ERROR").fail()
                .on("NOERROR").to(saveMappingLines)
                .end()
                .build();
```

The JobExecutionDecider returns "YES" status if there is error in stepExecutionContext.
```
@Bean
    public JobExecutionDecider writerDecide() {
        return new JobExecutionDecider() {
            @Override
            public FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution) {
                if (CollectionUtils.isEmpty((Collection<?>) stepExecution.getExecutionContext().get(ERRORS_IN_CONTEXT))) {
                    log.info("...No validation errors");
                    return new FlowExecutionStatus("NOERROR");
                }
                log.info("...validation errors found");
                return new FlowExecutionStatus("ERROR");
            }
        };
    }
```

https://stackoverflow.com/questions/41746719/using-jobexecutiondecider-with-spring-boot-batch  
https://www.programcreek.com/java-api-examples/index.php?api=org.springframework.batch.core.job.flow.JobExecutionDecider  
https://www.programcreek.com/java-api-examples/?code=mintster/nixmash-blog/nixmash-blog-master/batch/src/main/java/com/nixmash/blog/batch/demo/DemoJobConfiguration.java  
https://docs.spring.io/spring-batch/trunk/reference/html/configureStep.html

## spring batch load files with header and footer
the requirement is to validate header and footer with the content. For example, the header may contain total line number shall be expect in the file and the footer contains the total amount.

If the file is small, we can read all data in by setting chunk size to a big number. There are reader, processor and writer in the step. We can do the validation in the processor. 

If the file is big, we cannot read it into memroy. It may need two steps. The first step read read chunk by chunk and do the count and total amount caculation. Save the calculation result into stepExectionContext and pass them to the next step. The second step validate the header and the footer has the right data. The third step shall only be triggered when it passes the second one. The third step read data chunk by chunk and write them. 

## spring faultTolerant skip and retry
Spring Batch will first process the whole chunk at once if that fails it will fallback to proces each item individually so it is able to determine which items where faulty and skipped. Then some records are saved into database and some are not. The one cannot be saved triggers the listener. If you want to have "all or nothing", you shall not use "faultTolerant", then spring rollback the transaction. 

## multiple processor
CompositeResultBean can be used. 
```
CompositeResultBean Processor0.process(Item item) {
  final CompositeResultBean r = new CompositeResultBean();
  r.setResult1(processor1.process(item));
  r.setResult2(processor2.process(item));
  return r;
}
```
https://stackoverflow.com/questions/18999724/spring-batch-one-reader-multiple-processors-and-writers

## get StepExecution in Listener
I need get the StepExecution in my listener, then I can save some states into the context.  
I have a SkipListener which need reference of StepExecution. I made my SkipListener implements both SkipListener and StepExecutionListener. But spring does not inject the StepExecutionListener to my class. Later I found I have to register the listener twice. One is to regiester it as SkipListener and the other one is to regiester it as a StepExecutionListener. 
```
@Bean
public Step processCustomerLines(ItemWriter<Customer> customerWriter,
                                        ItemReader<Customer> customerReader,
                                        SkipListener<Customer, Customer> customerThrowableSkipListener,
                                        @Value("${batch.chunk.size}") int chunkSize) {
    return stepBuilderFactory.get("processCustomerRuleLines")
            .<Customer, Customer> chunk(chunkSize)
            .faultTolerant()
            .skip(Throwable.class)
            .skipLimit(Integer.MAX_VALUE)
            .listener(customerThrowableSkipListener)
            .reader(customerReader)
            .writer(customerWriter)
            .listener((StepExecutionListener) customerThrowableSkipListener)
            .build();
}
```
Above is the sample code to build the step. The same customerThrowableSkipListener was regiestered twice. The first listener(customerThrowableSkipListener) is to regiester it as skipListener and the second listener((StepExecutionListener) customerThrowableSkipListener) is to regiester it as StepExecutionListener. Now Spring knows it needs StepExecution being injected.  
https://stackoverflow.com/questions/43987089/issue-retrieving-a-executioncontext-from-a-skiplistener



