
## examples
https://gist.github.com/btroncone/d6cf141d6f2c00dc6b35

https://angularfirebase.com/lessons/rxjs-quickstart-with-20-examples/

## online test
the code example can be input here to do a quick test
http://jsbin.com/qoxisuguke/edit?js,console,output

## cold vs hot | share
... to be filled...

## Subject 
An RxJS Subject is just an Observable with the ability to call next() on itself to emit new values.
```
const myObservable1 = new Rx.Subject();

myObservable1.map(num => Math.log(num))
  .subscribe(console.log)

myObservable1.next(1000);
```
- Observable is a function that sets up observation.
- Observers is a list of objects which subscribe to Observable and will receive notifications.
- Subject is Observables and also implements Observer interface. 
https://medium.com/@benlesh/on-the-subject-of-subjects-in-rxjs-2b08b7198b93

## Observable.from vs Observable.of
"from" iterates the emitted value, but "of" does not iterate
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

## map - convert the emitted value
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
2. Debounce - Wait X time after the last value emitted, if no more value emitted, then give me the last value.
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

Here is another good example of debounce. Autocomplete, you don't want to send query to the server on every keystroke.
```
this.searchSubscription = this.searchFormControl.valueChanges
    .debounceTime(500)
    .subscribe(inputValue => {
        // console.log('inputValue...', inputValue);
        this.doSearch(inputValue);
    });
```

When does it start debounce time? The debounce is started every time there is a value emitted.  
Here is the code to help me understand it. 
```
const source = new Rx.Subject();
source
  .debounceTime(3000)
  .subscribe(val => {
    console.log(val, new Date().toLocaleString());
    console.log('======================')
  });

setTimeout(()=>{
  source.next(1);
}, 500);
source.next(2);
setTimeout(()=>{
  console.log('next(3)', new Date().toLocaleString())
  source.next(3);
}, 600);

setTimeout(()=>{
  console.log('next(4)', new Date().toLocaleString())
  source.next(4);
}, 5000);
```
The console output is like this
```
next(3) 8/1/2018, 2:54:02 PM
3 "8/1/2018, 2:54:05 PM"
======================
next(4) 8/1/2018, 2:54:06 PM
4 "8/1/2018, 2:54:09 PM"
======================
```
Here is what it happens. 
- 2 is the first emitted value
- debounce 3 seconds 
- 1 is emitted after 500 ms
- debounce 3 seconds after 1 is emitted
- 3 is emitted after 600 ms
- debounce 3 seconds after 3 is emitted
- as no other values are emitted in these 3 seconds, the subscribe function is triggered with the latest value emmited, which is 3. From the console output, we can see the subscribe function is executed 3 seconds after the value is emitted. And the 3 seconds is the debounce time. 
- 4 is emiited after 5 seconds and the subscription function is executed 3 seconds later. 

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
```
observable1.switchMap(observer1 => {
	return observable2
	}).subscribe(observer2 => {...})
```
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

## exhaustMap

## mergeMap/flatmap
Here is my code. the click triggers the innerObservable. I am using the Math.random to create an instance id.
The internalObservable emits 'foo' value right away and emits 'bar' value after 3 seconds.  
```
const clicks$ = Rx.Observable.fromEvent(document, 'click');
const innerObservable$ = Rx.Observable.create(
  observer => {
    let id = Math.floor((Math.random() * 1000000) + 1);
    let counter = 0 ;
    observer.next({id:id, counter:counter++, value:'foo'});
    setTimeout(() => observer.next({
      id:id, counter:counter++, value:'bar'
    }), 3000);
  });

clicks$.mergeMap(event => innerObservable$)
                    .subscribe(val => console.log(val));
//Click twice
//Output
//first mouse click
[object Object] {
  counter: 0,
  id: 671767,
  value: "foo"
}
//second mouse click, the second innerObservable is triggered, the id is different. 
[object Object] {
  counter: 0,
  id: 95199,
  value: "foo"
}
//the first innerObservable is still emitting values. 
[object Object] {
  counter: 1,
  id: 671767,
  value: "bar"
}
[object Object] {
  counter: 1,
  id: 95199,
  value: "bar"
}

```

Let's compare it with "switchMap". The code is the same, the only change is using "switchMap" to replace "mergeMap".
```
const clicks$ = Rx.Observable.fromEvent(document, 'click');
const innerObservable$ = Rx.Observable.create(
  observer => {
    let id = Math.floor((Math.random() * 1000000) + 1);
    let counter = 0 ;
    observer.next({id:id, counter:counter++, value:'foo'});
    setTimeout(() => observer.next({
      id:id, counter:counter++, value:'bar'
    }), 3000);
  });

clicks$.switchMap(event => innerObservable$)
                    .subscribe(val => console.log(val));
//Click twice
//Output
//first mouse click
[object Object] {
  counter: 0,
  id: 763458,
  value: "foo"
}
//second mouse click, we can see the first subscrption is cancelled.
[object Object] {
  counter: 0,
  id: 734597,
  value: "foo"
}
[object Object] {
  counter: 1,
  id: 734597,
  value: "bar"
}
```
Here are some comparisons:
- flatMap/mergeMap - creates an Observable immediately for any source item, all previous Observables are kept alive
- concatMap - waits for the previous Observable to complete before creating the next one
- switchMap - for any source item, completes the previous Observable and immediately creates the next one
- exhaustMap - source items are ignored while the previous Observable is not completed

Here is another scenario to use the flatmap.  
let's say we have this code. The internal service call 'this.wikipediaService.search(term)' also returns an Observable. 
```
this.term.valueChanges
   .debounceTime(400)
   .distinctUntilChanged()
   .subscribe(term => this.wikipediaService.search(term).subscribe(items => this.items = items))
```
If you have a subscribe inside another subscribe, this shall be avoided. It can be refactored by flatMap or switchMap.
The above code shall be refactored to this
```
this.term.valueChanges
	.debounceTime(400)
	.distinctUntilChanged()
	.flatMap(term => this.wikipediaService.search(term))
	.subscribe(items => this.items = items);
```

## TakeUntil - Get values until you’re told not to
Basically, it stops the subscription from the outer observable once the inner observable emit a value. 
Example: mouse drag and drop 
html
```
<body style="height: 500px;">
  <div id="widget" style="position: absolute;">AABBCC...</div>
</body>
```
javascript
```
var widget = document.getElementById("widget");
var parent = document.getElementsByTagName("BODY")[0];

var mouseDowns = Rx.Observable.fromEvent(widget, "mousedown");
var parentMouseMoves = Rx.Observable.fromEvent(parent, "mousemove");
var parentMouseUps = Rx.Observable.fromEvent(parent, "mouseup");
mouseDowns.switchMap( e => parentMouseMoves.takeUntil(parentMouseUps))
	.subscribe(e => {
		widget.style.left = e.clientX + "px";
		widget.style.top = e.clientY + "px";
	});
```
Once the parentMouseUps event happens, it completes the subscription to the parentMouseMoves, so the target div is not moving any more. 

## TakeWhile - Get values while the conditions are right

## Buffer - Collect values, then emit as Array

## withLatestfrom vs combineLatest

## pairwise - Let me know when the Observable emits, but also give me the previous value. ( Array )
```
var source = new Rx.Subject();

var subscription = source.pairwise().subscribe(
    function (x) {
        console.log('Next: ' + JSON.stringify(x));
    },
    function (err) {
        console.log('Error: ' + err);
    },
    function () {
        console.log('Completed');
    });

source.next('test1');
source.next('test2');
source.next('test3');

//Output
// it won't trigger until there are two values, the array contains the previous and the current value.
"Next: [\"test1\",\"test2\"]"
"Next: [\"test2\",\"test3\"]"
```

## pipe

## Errors Handling
Observables does not trap errors. for example, the code below won't hit the error function. 
```
var source = Rx.Observable.create( (observer) => {
    observer.next(1);
    throw new Error('test');
});

source.subscribe({
  next: (val) => {console.log('next...', val);}, 
  error: (e) => {
    console.log('error function is executed...');
    console.log('error...\n', e.message);
  }, 
  complete: ()=> {console.log('complete...');}
  }
);
```
You have to catch the error in the observable function and call .error() method explicitly like this. 
```
var source = Rx.Observable.create( (observer) => {
    observer.next(1);
    try {
        // ... some code may throw out error
        throw new Error('test___error');
    } catch (e) {
      observer.error(e);
    }
});

source.subscribe({
  next: (val) => {console.log('next...', val);}, 
  error: (e) => {
    console.log('error function is executed...');
    console.log('error...\n', e.message);
  }, 
  complete: ()=> {console.log('complete...');}
  }
);
```
https://medium.com/@benlesh/on-the-subject-of-subjects-in-rxjs-2b08b7198b93

Here is an example, if the error is thrown from the operator chain, like map/filter, it kills the Observable. 
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
source.next(3);
```
The result is  
"map val...1"  
"subscribe..." 1    
"map val...2"  
"err"  // it hits the err function in subscribe  
"error, catch me!"  

It hits the err function in the subscribe but it kills the subject. The  source.next(3) was not sent to the observer. To solve this, create a disposable stream, If an error occurs only the disposable stream dies, and the main stream lives on. https://iamturns.com/continue-rxjs-streams-when-errors-occur/
```
const source = new Rx.Subject();

const source2 = source.switchMap(x =>
	Rx.Observable.of(x)
		.map(val => {
          if (val % 2 == 0) {
            throw 'error, catch me!';
          }
          return val;
        })
		.catch(error => Rx.Observable.of('even number'))
);

source2.subscribe(
	(x => console.log('Success:', x)),
	(x => console.log('Error:', x)),
	(() => console.log('Complete'))
);

source.next(1);
source.next(2);
source.next(3);
```
The result is  
"Success:" 1  
"Success:" "even number"  
"Success:" 3  
The pros is that it does not kill the subject. The cons is that it never hits the error function in subscribe.

## subscribe vs forEach
the code below compares forEach vs subscribe. They actually do the same work
```
const clicks$ = Rx.Observable.fromEvent(document, 'click');

clicks$.map(e => {
  return {x:e.clientX, y:e.clientY}
}).forEach(point => {
  alert(JSON.stringify(point));
});

clicks$.subscribe( e => alert(e.clientX+"|"+e.clientY));
```
The difference is the return type, forEach returns Promise while subscribe returns ISubscription.


## observer is an array VS osbserver emitted multiple times
The subscribe triggers a function which may return an array, e.g. a service call to fetch data from database usually returns an array.  
Then the array is the observer and wrapped in the Observable. 
```
observableInstance.subscribe((val) => {
		//the val is an array
	})  
``` 
We cannot apply operator like groupBy, filter, map on the observable. Those are the operators applied on the values form multiple values emitted. It is confusing that these values feel like an array. Actually, they are not. They are values in an stream and arriving in different time.  

if you need to do filter on the array returned from one value emit. do this
```
observableInstance
	.map(val => val.filter(...)) // the val here is just an array
	.subscribe((val) => {
		//the val is an array
	})
``` 



