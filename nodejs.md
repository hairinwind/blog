
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

## let request-promise return full response
By default, the request-promise only return the json object in the body, it does not have the status code. In my case, I need propagate the status. I need set this option
```
const requestOptions = {
    ...
    resolveWithFullResponse: true, // to propagate the status, the full response shall be returned
    json: true, 
};

const results = await request.post(url, requestOptions);
const parsedResults = results.body;
```
the json:true option would return json obejct in results.body, so you don't need parse it. 

