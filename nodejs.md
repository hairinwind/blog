
## Express error handling
https://expressjs.com/en/guide/error-handling.html
golden retriever
Errors that occur in synchronous code inside route handlers and middleware require no extra work.
Errors returned from asynchronous functions that are invoked by route handlers and middleware must be passed to the next() function where they will be caught by Express and processed accordingly.

## await 
Making async function call looks like a synchronous function call. 

https://blog.risingstack.com/mastering-async-await-in-nodejs/


## avoid singleton 
https://medium.com/@iaincollins/how-not-to-create-a-singleton-in-node-js-bd7fde5361f5
```
var helloWorldA = require(‘./hello-world’)(),
    helloWorldB = require(‘./hello-world’)();
```
