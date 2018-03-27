
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



