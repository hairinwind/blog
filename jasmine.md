
## only run specific spec
```
npm run test -- src/adhoc-datasets/api/adhoc-datasets.api.controller.spec.ts
```

## createSpy vs createSpyObj
These two methods are helpful to create mock on function and objects.
https://stackoverflow.com/questions/24321307/what-is-the-difference-between-createspy-and-createspyobj

use "createSpy" when the implementation does not exist.
use "createSpyObj" from the implmentation (object/function) when it exists.

## spyOn cheat sheet
https://daveceddia.com/jasmine-2-spy-cheat-sheet/

## spy a function without object
```
test = jasmine.createSpy();
or 
test = createSpy().and.callFake(test); 
```

## unhandledRejection
If there is an error thrown from async functions, it is only an unhandledRejection warning and jasmine does not think the test spec is failed. 
To correct it, create the file 'unhandled-rejection.spec.ts', then the warning will be treated as error.
```
describe('Global Unhandled Rejection Processing', () => {
  const UNHANDLED_REJECTION_EVENT = 'unhandledRejection';

  // Handle this global event to force the "jasmine" unit test engine to
  // fail any unit test which raises error in the callback of a "promise" operation.
  // Otherwise, "jasmine" will suppress the error by only raising "UnhandledPromiseRejectionWarning".
  process.on(UNHANDLED_REJECTION_EVENT, function (err, promise) {
    console.log(`\r\nOne or more tests failed due to '${UNHANDLED_REJECTION_EVENT}'
      event occurred in the callback of a 'promise' operation.\r\nSee following for details.`);
    throw err;
  });

});

```

## error Expected a spy, but got Function.
It means the method shall be spyOn but not. 

## jasmine test setTimeout
when the testing target code contains 'setTimeout', there is two ways to test it. 
- spyOn setTimeout
- wait for the timeout
If the target code is js running in browser, we can spyOn 'window, setTimeout' and make the func to be executed without waiting.
```
spyOn(window, 'setTimeout').and.callFake((func, time) => {
	func();
});
``` 
If the code is nodejs code, there is not reference to window. The nodejs has 'setTimeout' in global and in timers module.
I don't know how to mock the 'setTimeout' in global. spyON(global, 'setTimeout') does not work. 
If the target code is using the timers module, here is the typescript example 
```
import * as timers from 'timers';
...
timers.setTimeout(func, 5000)
```
we can spyOn it
```
spyOn(timers, 'setTimeout').and.callFake((func, time) => {
	func();
});
```

The second way is to wait for the timeout. 
By default jasmine will wait for 5 seconds for an asynchronous spec to finish before causing a timeout failure.
If need more time, this can be adjusted by setting jasmine.DEFAULT_TIMEOUT_INTERVAL.
Let's say targetService.targetFunction() is an async function which returns a promise. In that method, it wait for 6 seconds, longer than jasmine default. 
We need set a new jasmine timeout in beforeEach and recover it in afterEach. 
```
describe("long asynchronous specs", function() {
    let originalTimeout;
    beforeEach(function() {
      originalTimeout = jasmine.DEFAULT_TIMEOUT_INTERVAL;
      jasmine.DEFAULT_TIMEOUT_INTERVAL = 10000;
    });

    it("takes a long time", function(done) {
      targetService.targetFunction().then(() => {
      		expect(...);
      		//when everything is done, call done() to notify jasmine context
      		done();
      	})
    });

    afterEach(function() {
      jasmine.DEFAULT_TIMEOUT_INTERVAL = originalTimeout;
    });
  });

```
The done() shall be called to notify the jasmine context when all work is done. Otherwise, jasmine throws out error when timeout happens. The timeout error is like this.
```
Error: Timeout - Async callback was not invoked within timeout specified by jasmine.DEFAULT_TIMEOUT_INTERVAL.
```
In conclusion: Because we’re testing an async call, in beforeEach or it block, don’t forget to call done. The test runner will wait until the done() function is called before moving to the next test.


