## Object Property Descriptors
Javascript object property descriptors can help on these:

- How do I create a read-only property?
- How do I make an unenumerable property?

```
var cat = {
    name: 'foo',
    age: 9
};
Object.defineProperty(cat, 'name', { writable: false });
console.log(cat.name); // foo 
cat.name = "koo"; // JavaScript will ignore it as writable is set to false 
console.log(cat.name); // foo
```
https://dzone.com/articles/easy-javascript-part-7-object-property-descriptors?edition=334864&utm_source=Daily%20Digest&utm_medium=email&utm_campaign=Daily%20Digest%202017-11-10

## promise vs observable
https://yuyang041060120.github.io/2016/05/16/observable-vs-promise/

promise does not have delay, cancel and retry
while observalbe has these features

## import vs require
There are two module system you can choose in Node.js:

- Importing modules using require, and exporting using CommonJS module.exports and exports.foo
- Importing modules using ES6 import, and exporting using ES6 export.

```
var fs = require('fs');
```
equals
```
import * as fs from 'fs';
```

http://researchhubs.com/post/computing/javascript/nodejs-require-vs-es6-import-export.html

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import

https://blog.jdriven.com/2017/06/typescript-and-es6-import-syntax/

article about commonJS require https://blog.risingstack.com/node-js-at-scale-module-system-commonjs-require/

## template
```
var test = 'abc';
var s = `this is ${test} ...`;
console.log(s);
//this is abc ...
```
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals

## promise resolve
//TODO

## dynamic import 
import the module at runtime.
```
export module PowerBiService {
	export function getToken() {
		...
	}
	...
}
```
To dynamic import it and call the function
```
const myPowerBiService = (await import('./powerbi.service')).PowerBiService; //.PowerBiService is the moduel name, here is used as namespace
const token = myPowerBiService.getToken();
```

## JSON parse error 
Got error when run JSON.parse(x)
the error is like this, “Unexpected token o in JSON at position 1”
**It means your data is already an object.** No need to parse it. The passed in argument x shall be a JSON string. 

## await 
The await operator is used to wait for a Promise. It can only be used inside an async function.  
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await  

Observable and Promise are both asynchronous. But await only works for Promise. I used to make a mistake to put await in front of Observable and nothing was triggered. 
To use before Observable, call the toPromise function on observable
```
await observable.toPromise();
```

The benefit of await
- Async/await improves code readability
- Async/await gives us synchronous-like syntax for asynchronous behavior
- Async/await can be used with and in place of promises
- Async/await enables try...catch error handling for asynchronous operations
- Async/await is supported by Node.js and all major browser vendors
- Async/await officially arrives in the ES2018 language spec
https://appendto.com/2017/06/asyncawait-come-use/

## Throw Errors From Async Functions
https://www.valentinog.com/blog/throw-errors-async-functions-javascript/  
if you see this error, it means an error was thrown in async functions and it is not caught.
```
UnhandledPromiseRejectionWarning: Unhandled promise rejection...
```
Async functions and async methods always return a Promise, either resolved or rejected.  
When exception is thrown, a rejected Promise will propagate up in the stack unless you catch it.  

throwing exception is same in sync and async functions
```
async getData(url) {
    if (typeof url !== "string") {
      throw TypeError("url must be a string");
    }
    // const response = await fetch(url)
    // do stuff
}
```  
To catch the excpetion from async method 
use .catch() method
```
getData(url).then(res => ...).catch(err => console.error(err));
```
Or use try/catch with await
```
try{
	await getData(url);
}catch(error) {
	console.error(error);
}
```

## slice() vs splice()
- The slice( ) method copies a given part of an array and returns that copied part as a new array. It doesn’t change the original array.
- The splice( ) method changes an array, by adding or removing elements from it.
https://medium.freecodecamp.org/lets-clear-up-the-confusion-around-the-slice-splice-split-methods-in-javascript-8ba3266c29ae

## javascript sleep function
```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function demo() {
  console.log('Taking a break...');
  await sleep(2000);
  console.log('Two seconds later');
}
```
https://stackoverflow.com/questions/951021/what-is-the-javascript-version-of-sleep  


