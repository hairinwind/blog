# angular

## async pipe
https://www.concretepage.com/angular-2/angular-2-async-pipe-example
```
<div>{{ observableTime | async }} </div> 
```
properties on aync object
```
<div [ngSwitch]="(promiseBook | async)?.id">

<div *ngIf="promiseBook | async as book; else loading">
```
add other pipes
```
<div>{{ observableTime | async | date : 'mediumTime' }} </div>
```

## how to debug the subscribe triggered from async pipe
The usual way to debug Observable is to add "do" before "subscribe"
```
observable.do(x=>console.log(x))
	.subscribe(...)
```
But if it is async pipe from the angular, the subscribe is triggered from angular code. I cannot add "do" before the "subscribe". Here I need share the observable. Then subscribe on data is shared. Only one service call will be triggered.
```
    data: Observable<any>;

    ngOnInit() {
        this.data = this.myService.getAll().share();
        this.data.subscribe(
            x => {
                console.log('return data', x);
        }
        );
    }
```
https://jaredforsyth.com/posts/visualizing-reactive-streams-hot-and-cold/

## observable.share to reduce the service call
I have html like this, which triggers two service call because there is 2 aysnc, although they are watching on the same object. 
```
<p>
    data size: {{(data|async)?.length}}
</p>
<div *ngFor='let item of data | async'>
    <p>{{item.title}}</p>
    <p>{{item.body}}</p>
    <hr/>
</div>
```
make the observable share can trigger only one service request.
```
    data: Observable<any>;

    ngOnInit() {
        this.data = this.myService.getAll().share();
    }
```


## 3 ways to communicate between angular components
1. Passing the reference of one component to another
2. Communication through parent component @Input/@Output
3. Communication through Service

https://medium.com/dailyjs/3-ways-to-communicate-between-angular-components-a1e3f3304ecb