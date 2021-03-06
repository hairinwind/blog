## Basic Types
- boolean
- number
- string
- array
```
	let a: number[] = [1, 2, 3];
	let a: Array<number> = [1, 2, 3];
```
- tuple
```
	let x: [string, number];
	x = ["hello", 10]; 
```
	When accessing an element outside the set of known indices, a union type is used instead:
```
x[3] = "world"; // OK, 'string' can be assigned to 'string | number'
x[4] = true; // error, 'boolean' isn't 'string | number'
```	
- enum
```
	enum Color {Red, Green, Blue}
	let c: Color = Color.Green;
```
- any
```
	let notSure: any = 4;
```

- void, commonly see this as the return type of functions
```
	function warnUser(): void {
	    alert("This is my warning message");
	}
```

- type assertions
```
	let someValue: any = "this is a string";
	let strLength: number = (<string>someValue).length;
```
	OR 
```
	let someValue: any = "this is a string";
	let strLength: number = (someValue as string).length;
```




For more detail 

https://www.typescriptlang.org/docs/handbook/basic-types.html

## Get and Set
```
class foo {
    private _bar:boolean = false;
    get bar():boolean {
        return this._bar;
    }
    set bar(theBar:boolean) {
        this._bar = theBar;
    }
}
```
So to use it,
```
var myFoo = new foo();
if(myFoo.bar) {         // calls the getter
    myFoo.bar = false;  // calls the setter and passes false
}
// myFoo.getBar(); // this is wrong    
// myFoo.get('bar');  // this is wrong
```

## print instance class name
```
const instance = new MyClass();
console.log(instance.constructor.name); // MyClass
```
https://stackoverflow.com/questions/13613524/get-an-objects-class-name-at-runtime-in-typescript

## enum
example 
```
enum Colors {
    Red = "RED",
    Green = "GREEN",
    Blue = "BLUE",
}
```
you can’t write Colors["RED"] to get the string "Red"


## optional argument
add ? after the argument
```
class X {
    error(message: string, title?: string, autoHideAfter?: number) {
        console.log(message, title, autoHideAfter);
    }
}

new X().error("hi there", undefined, 1000);
```
also a property in interface can be optional
```
export interface IErrorParams {
  message: string;
  title?: string;
  autoHideAfter?: number;
}
```


