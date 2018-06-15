
## examples
https://gist.github.com/btroncone/d6cf141d6f2c00dc6b35

https://angularfirebase.com/lessons/rxjs-quickstart-with-20-examples/

## online test
the code example can be input here to do a quick test
http://jsbin.com/qoxisuguke/edit?js,console,output

## cold vs hot
... to be filled...

## Subject 
An RxJS Subject is just an Observable with the ability to call next() on itself to emit new values.
```
const myObservable1 = new Rx.Subject();

myObservable1.map(num => Math.log(num))
  .subscribe(console.log)

myObservable1.next(1000);d
```

## ReplaySubject - How to subscribe to RxJS event after it has been already emitted
```
let subject: ReplaySubject<string> = new ReplaySubject(1);
subject.next('test');
 
subject.subscribe((event) => {
  console.log(event);
});
```
It keeps in memory the last event emitted and will emit it again to every new subscriber. Actually, you can also change the constructor parameter from 1 to the number you need, in order to keep in memory more events and emit them again when you subscribe it: new ReplaySubject(5).
http://blog.danieleghidoli.it/2017/09/11/subscribe-rxjs-event-after-emitted-angular/

## withLatestFrom
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
switchMap likes mergeMap but when the source Observable emits, cancel any previous subscriptions of the inner Observable and a new one will start.
```
const source = new Rx.Subject();
const thisServiceFindUserById = function(userId) {
  return Rx.Observable.create( (observer) => {
    observer.next({id:userId, name:"john"});
  });
};

source.switchMap(params => {
  console.log("switchMap params...", params);
  return thisServiceFindUserById(params['id']);
})
  .subscribe(val => console.log("result...", val));

source.next({id:12345});

//Output
"switchMap params..."
[object Object] {
  id: 12345
}
"result..."
[object Object] {
  id: 12345,
  name: "john"
}
```
The switchMap can take a second parameter which is a function to combine the two emitted value
```
source.switchMap(params => {
  console.log("switchMap params...", params);
  return thisServiceFindUserById(params['id']);
}, (params, internalObservableResult) => [params, internalObservableResult])
  .subscribe(val => console.log("result...", val));
//Output 
"result..."
[[object Object] {
  id: 12345
}, [object Object] {
  id: 12345,
  name: "john"
}]
```
this is a real example from angular project
```
this.course$ = this.route.params. //params is an observable
	.switchMap(params => this.coursesServie.findCourseByUrl(params['id']))  //service to call remote http
	.first()
	.publishLast().refCount();
```
https://blog.angular-university.io/rxjs-switchmap-operator/

## concat
The subscription won't start until the previous completes. It is like a subscription queue.
```
const source1 = new Rx.Subject();
const source2 = new Rx.Subject();

source1.concat(source2)
  .subscribe(res=>console.log(res));

source2.next(1);
source1.next(2);
source1.next(4);
source1.complete();  //when source1 never completes, the subsequent observables never run
source2.next(3);
//output 2,4,3
```

## concatMap

##forkjoin
forkJoin is the Promise.all() of Rx. Basically it means that don’t let me know until all the Observables are complete, then give me all the values at once. ( Array )
```
const source1 = new Rx.Subject();
const source2 = new Rx.Subject();
Rx.Observable.forkJoin(source1, source2).subscribe(res => console.log(res));

source1.next({id:1});
source1.complete();
console.log("source1 completed...");
source2.next({id:2});
console.log("before source2 completed...");
source2.complete();
console.log("after source2 completed...");

//Output
"source1 completed..."
"before source2 completed..."
[[object Object] {
  id: 1
}, [object Object] {
  id: 2
}]
"after source2 completed..."
```

## flatMap

## exhaustMap

## mergeMap
- flatMap/mergeMap - creates an Observable immediately for any source item, all previous Observables are kept alive
- concatMap - waits for the previous Observable to complete before creating the next one
- switchMap - for any source item, completes the previous Observable and immediately creates the next one
- exhaustMap - source items are ignored while the previous Observable is not completed

## TakeUntil - Get values until you’re told not to

## TakeWhile - Get values while the conditions are right

## Buffer - Collect values, then emit as Array

## withLatestfrom vs combineLatest

## Errors Handling
```
const source = new Rx.Subject();
source.map(val => {
  console.log("map val..." + val);
  if (val % 2 == 0) {
    throw 'error, catch me!';
  }
  return val;
})
  .subscribe(val => console.log("subscribe...", val), 
            err => console.log("err", err));

source.next(1);
source.next(2);
```
catch? to be filled...





