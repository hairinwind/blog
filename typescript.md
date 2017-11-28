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