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

## 3 ways to communicate between angular components
1. Passing the reference of one component to another
2. Communication through parent component @Input/@Output
3. Communication through Service

https://medium.com/dailyjs/3-ways-to-communicate-between-angular-components-a1e3f3304ecb