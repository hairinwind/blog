
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

