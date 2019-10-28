
## design principles
SOLID

## design pattern

## get rid of if/else
use strategy https://juejin.im/post/5c5172d15188256a2334a15d  
use handler http://www.ciphermagic.cn/spring-boot-without-if-else.html 

use java 8 filter? https://www.baeldung.com/java-predicate-chain  
https://www.baeldung.com/java-replace-if-statements
the basic idea is to use hashMap to bypass if/else
```
public class OperatorFactory {
    static Map<String, Operation> operationMap = new HashMap<>();
    static {
        operationMap.put("add", new Addition());
        operationMap.put("divide", new Division());
        // more operators
    }
 
    public static Optional<Operation> getOperation(String operator) {
        return Optional.ofNullable(operationMap.get(operator));
    }
}
```

