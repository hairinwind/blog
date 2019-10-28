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


