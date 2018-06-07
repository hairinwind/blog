
## examples
https://gist.github.com/btroncone/d6cf141d6f2c00dc6b35

https://angularfirebase.com/lessons/rxjs-quickstart-with-20-examples/


## How to subscribe to RxJS event after it has been already emitted
```
let subject: ReplaySubject<string> = new ReplaySubject(1);
subject.next('test');
 
subject.subscribe((event) => {
  console.log(event);
});
```
It keeps in memory the last event emitted and will emit it again to every new subscriber. Actually, you can also change the constructor parameter from 1 to the number you need, in order to keep in memory more events and emit them again when you subscribe it: new ReplaySubject(5).
http://blog.danieleghidoli.it/2017/09/11/subscribe-rxjs-event-after-emitted-angular/

## test code
```
const myObservable1 = new Rx.Subject();

const myObservable2 = new Rx.Subject();

// const combine = Rx.Observable.withLatestFrom(myObservable1, myObservable2);
const combine = myObservable1.withLatestFrom(myObservable2);
combine.subscribe(([param1, param2])=> {
  console.log(param1,param2);
})

myObservable2.next('bar');
myObservable1.next('foo');
```

## Observable.from vs Observable.of
"from" iterates the emmitted value, but "of" does not iterate
```
const myObservable1 = Rx.Observable.from([1,2,3,4]);
myObservable1.subscribe(console.log);
//1
//2
//3
//4
```
```
const myObservable1 = Rx.Observable.of([1,2,3,4]);
myObservable1.subscribe(console.log);
//[1,2,3,4]
```
Actually, 
```
Rx.Observable.from([1,2,3,4])
```
equals 
```
Rx.Observable.of(1,2,3,4)
```

## map - convert the emmitted value
```
const myObservable1 = new Rx.Subject();

myObservable1.map(num => Math.log(num))
  .subscribe(console.log)

myObservable1.next(1000);
```
convert 1000 to Math.log(1000)

## filter
```
const myObservable1 = Rx.Observable.from([1,2,3,4]);

myObservable1.filter(num => num % 2 == 0)
  .subscribe(console.log)
```

## first emit value or first to pass condition
```
const source = Rx.Observable.from([1,2,3,4,5]);
source.first(num => num === 5).subscribe(console.log);
```
last is vice versa.

## Debounce and Throttle - Handle Stream Overload
Sometimes you might be dealing with a stream that that is emitting values crazy fast - such as mouse move events in the browser. You might only want to handle these events every so often.

1. Throttle - Give me the first value, then wait X time.
2. Debounce - Wait X time, then give me the last value.
```
mouseEvents
  .throttleTime(1000)
  .subscribe()
// MouseEvent<data>
// wait 1 second...


mouseEvents
  .debounceTime(1000)
  .subscribe()
// wait 1 second...
// MouseEvent<data>
```

## scan - keep a running total
Scan is like "reduce" for arrays in javascript. It keeps track of the accumulated total of emitted values. 
```
const source = new Rx.Subject();
source.scan((totalScore, current) => {
  return totalScore + current;
})
  .subscribe( val => console.log("subscribe...", val));

source.next(1); //subscribe... 1
source.next(2); //subscribe... 3
source.next(3); //subscribe... 6
```
It also works for object 
```
const source = new Rx.Subject();
source.scan((total, current) => {
  return Object.assign({}, total, current);
})
  .subscribe( val => console.log("subscribe...", val));

source.next({name:"john"});  // {name:"john"}
source.next({age:25}); //{age:25, name:"john"}
```

## switchMap - Get value from Observable A, then emit Observable B
switchMap is commonly required when dealing with async data from a database or API call. For example, you need to get a user ID from an observable, then use it to query the database.

