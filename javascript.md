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

## !! in javascript 
it converts 0, false, undefined or null to false and any other value provided to true.
https://stackoverflow.com/questions/784929/what-is-the-not-not-operator-in-javascript

## javascript three dots ...
https://medium.com/@oprearocks/what-do-the-three-dots-mean-in-javascript-bc5749439c9a  
- Basically, ...a means clone all properties from a.  
```
const adrian = {
    fullName: 'Adrian Oprea',
    occupation: 'Software developer',
    age: 31,
    website: 'https://oprea.rocks'
};
const bill = {
    ...adrian,
    fullName: 'Bill Gates',
    website: 'https://microsoft.com'
};
console.log('bill', bill); // bill has the properties from adrian and overwrite fullname and website
bill.age=32
console.log('bill.age', bill.age); //32
console.log('adrian.age', adrian.age); //31
```
- If a is an array, it clones all items from a.   
```
const numbers1 = [1, 2, 3, 4, 5];
const numbers2 = [ ...numbers1, 1, 2, 6,7,8]; // this will be [1, 2, 3, 4, 5, 1, 2, 6, 7, 8]
```
- When used within the signature of a function, the three dots are also called the rest operator. The rest operator enables the developer to create functions that can take an indefinite number of arguments.
```
function sum(...numbers) {
    return numbers.reduce((accumulator, current) => {
        return accumulator += current
    });
};
sum(1, 2, 3, 4) // 10
```

https://dmitripavlutin.com/how-three-dots-changed-javascript/

## Destructuring
Destructuring is a convenient way of extracting multiple values from data stored in (possibly nested) objects and Arrays.
From array
```
const iterable = ['a', 'b'];
const [x, y] = iterable;
// x = 'a'; y = 'b'
```
From Object
```
const obj = { first: 'Jane', last: 'Doe' };
const {first: f, last: l} = obj;
// f = 'Jane'; l = 'Doe'
```
https://exploringjs.com/es6/ch_destructuring.html  

## multiple arrow functions in javascript?
https://stackoverflow.com/questions/32782922/what-do-multiple-arrow-functions-mean-in-javascript
```
const add = x => y => x + y
```
convert to es5, it is 
```
const add = function (x) {
  return function (y) {
    return x + y
  }
}
```
to call function a, it is like this 
```
a(x)(y)
```
For the es6 style javascript code, use the babel online website to covnert it to es5  
https://babeljs.io/en/repl

## diff between two array
Intersection, in both arr1 and arr2
```
let intersection = arr1.filter(x => arr2.includes(x));
```
not in intersection, the items are either in arr1 or arr2
```
let difference = arr1
                 .filter(x => !arr2.includes(x))
                 .concat(arr2.filter(x => !arr1.includes(x)));
```
from arr1 but not arr2
```
let difference = arr1.filter(x => !arr2.includes(x));
```
https://stackoverflow.com/questions/1187518/how-to-get-the-difference-between-two-arrays-in-javascript

