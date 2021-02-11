## mockito mock annotations
https://www.baeldung.com/mockito-annotations

## powermock test private method 
AccountMappingProcessor is the class to be tested. "validate" is the private method to be tested. The rest parameters are the arguments of method "validate". 
```
AccountMappingProcessor accountMappingProcessor = new AccountMappingProcessor();
Whitebox.invokeMethod(accountMappingProcessor, "validate",
                validationPassed, validationMessage, targetList, accountMapping);
```
This article also introduces how to mock and veiry private method.  
https://www.baeldung.com/powermock-private-method  
https://github.com/powermock/powermock/wiki/MockPrivate

## mockito mock static method
The function to be tested shall call fileUtils twice with different arguments.   
Here is an example to use powermock to mock static and verify.
```
@RunWith(PowerMockRunner.class)
@PrepareForTest(FileUtils.class)
public class BatchResultFileExportorTest {

    @Test
    public void testFileGenerated() throws IOException {
        PowerMockito.mockStatic(FileUtils.class);
        BatchResultFileExportor exporter = new BatchResultFileExportor();
        exporter.setShallExportWarning(true);

        List<BatchError> batchErrors = new ArrayList<>();
        final String error_msg_1 = "error_msg_1";
        final String item_1 = "item_1";
        batchErrors.add(new BatchError(item_1, error_msg_1));
        final String error_msg_2 = "error_msg_2";
        final String item_2 = "item_2";
        batchErrors.add(new BatchError(item_2, error_msg_2));

        String batchFileName = "sample_batch_file_name";
        File targetDir = new File("/tmp");
        exporter.exportWarningToFiles(batchErrors, batchFileName, targetDir);

        PowerMockito.verifyStatic(FileUtils.class, Mockito.times(2));
        ArgumentCaptor<File> fileArgument1 = ArgumentCaptor.forClass(File.class);
        ArgumentCaptor<List> listArgument1 = ArgumentCaptor.forClass(List.class);
        FileUtils.writeLines(fileArgument1.capture(), listArgument1.capture());

        assertEquals(new File("/tmp", "sample_batch_file_name_warn.csv"), fileArgument1.getAllValues().get(0));
        List<String> expectedItems = Arrays.asList(new String[]{item_1, item_2});
        assertEquals(expectedItems, listArgument1.getAllValues().get(0));

        assertEquals(new File("/tmp", "sample_batch_file_name_warn.txt"), fileArgument1.getAllValues().get(1));
        List<String> expectedWarningMsgs = Arrays.asList(new String[]{error_msg_1, error_msg_2});
        assertEquals(expectedWarningMsgs, listArgument1.getAllValues().get(1));
    }

}
```
This example also verifies the arguments by ArgumentCaptor.

## mockito agument matchers
https://www.baeldung.com/mockito-argument-matchers  
I have the code like 
```
myService.call(new Date(), "abc");
```
When mock its return, how to match the new Date() argument?  
I implemented my own DateMatcher  
```
class DateMatcher implements ArgumentMatcher<Date> {

    private Date left;

    public DateMatcher(Date left) {
        this.left = left;
    }

    @Override
    public boolean matches(Date right) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        String leftText = sdf.format(left);
        String rightText = sdf.format(right);
        long dateDiff = Math.abs(right.getTime() - left.getTime());
        return leftText.equals(rightText) || dateDiff < 10000; 
    }
}
``` 
The "when" code is like this 
```
when(myService.call(argThat(new DateMatcher(calendarDate)), eq("abc"))).thenReturn(...);
```
Note: In case of a method has more than one argument, it isn't possible to use ArgumentMatchers for only some of the arguments. Mockito requires you to provide all arguments either by matchers or by exact values.

Here is another way to overwrite System.currentTimeMillis(), it needs Powermock and PowerMockito
```
whenNew(Date.class).withNoArguments().thenReturn(NOW);
```
https://stackoverflow.com/questions/11887799/how-to-mock-new-date-in-java-using-mockito


## use PowerMockRunner with SpringJUnit4ClassRunner
https://blog.jayway.com/2014/11/29/using-another-junit-runner-with-powermock/
```
@RunWith(PowerMockRunner.class)
@PowerMockRunnerDelegate(SpringJUnit4ClassRunner.class)
```

## Mockito.mock() vs @Mock vs @MockBean
https://www.baeldung.com/java-spring-mockito-mock-mockbean

Mockito.mock() and @Mock are the same.  
@MockBean adds the mock objects to spring application context.

## overwrite spring properties in test
https://www.baeldung.com/spring-tests-override-properties
```
@SpringBootTest(properties = { "example.firstProperty=annotation" })
```

## mockito deep stubs
Sometimes, need create deep object, it might be easy to use Mockito RETURNS_DEEP_STUBS
```
Target target = Mockito.mock(target.class, Mockito.RETURNS_DEEP_STUBS);
when(target.getA().getB().getC()).thenReturn(expect_return);
```

## junit 5 @ParameterizedTest
https://www.baeldung.com/parameterized-tests-junit-5  
@ValueSource or @EnumSource only support one argument. When multiple arguments are needed, use @MethodSource to refer a method to prepare the arguments. 
```
@ParameterizedTest
@MethodSource("provideStringsForIsBlank")
void isBlank_ShouldReturnTrueForNullOrBlankStrings(String input, boolean expected) {
    assertEquals(expected, Strings.isBlank(input));
}
...
private static Stream<Arguments> provideStringsForIsBlank() {
    return Stream.of(
      Arguments.of(null, true),
      Arguments.of("", true),
      Arguments.of("  ", true),
      Arguments.of("not blank", false)
    );
}

```
