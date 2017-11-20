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