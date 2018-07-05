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

## pass in String to angular components

String literals can be passed in different ways:
```
<component inputField="string"></component>
<component [inputField]="'string'"></component>
<component inputField="{{'string'}}"></component>
```

## Route event 
https://angular.io/guide/router#router-events

https://toddmotto.com/dynamic-page-titles-angular-2-router-events

I used to face the code like this
```
this.router.navigate([id, "childPage"], {queryParams: this.route.snapshot.queryParams});
```
Another place, 
```
let routeParamsObserver = Observable.combineLatest(this.route.params, this.route.queryParams,
            (params, queryParams) => {
                return {params, queryParams};
        });
routeParamsObserver.subscribe(functionA)
```
I guess angular change the parameters first. Then the subscriber functionA is triggered. At this moment the id was not applied to the route yet. 
I have to use the route event to make sure both id and params changes were applied before trigger functionA
```
this.router.events.subscribe((event) => {
            if (event instanceof NavigationEnd) {
                routeParamsObserver.first().subscribe(functionA);
                // first() make sure it won't trigger next time, until the NavigationEnd event happens
            }
        });
```

## unit test HttpClient
using HttpClientTestingModule, here is a good example 
https://alligator.io/angular/testing-httpclient/

## unit test mock router example
https://github.com/angular/vladivostok/blob/master/test/router.spec.ts

## unit test error: no component factory ... found,did you add it to @NgModule.entryComponents
creating a testModule can help it.
In my case, it complains no component factory SaveReportSnackbarComponent found,did you add it to @NgModule.entryComponents
```
const TEST_DIRECTIVES = [
  SaveReportSnackbarComponent,
];

@NgModule({
  imports: [
    MatFormFieldModule,
    ...],
  exports: TEST_DIRECTIVES,
  declarations: TEST_DIRECTIVES,
  entryComponents: [
    SaveReportSnackbarComponent
  ],
})
class TestModule { }
```
In the test configuration
```
beforeEach(async(() => {
    TestBed.configureTestingModule({
      imports: [
        ...
        TestModule
      ],
      providers: [
        ...
```

## TestBed useValue: new MockNavigationService() vs useClass: MockNavigationService
//TODO 


