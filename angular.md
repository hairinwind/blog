# angular

## benefit to use angular
coding in structure, so easy to maintain and test
typescript
unit test ready

## angular concepts
- Modules: a collection of components, directives ... can be exported and used by externally. For example HttpClientModule or a lot of material modules
- Components - basic UI build block. It is a subset of directives, associated with template. 
- Services - 
- Directives - ngIf, ngFor
- Templates - html 
- Metadata - Annotations like @Component
- Pipes and filters - pipes is to transform data.
- Routing - navigate from one view to another 
- Forms
- Data binging
  - interpolation {{value}}
  - propery binding: [property] = "value"
  - Event binding: (onclick) = "function"
  - Two-way data bindiing: <input ...[(ngModel)]="value" 
- Dependency injection

## Pipe example

## filter example

## eager and lazy loading 
eager loading is loading all modules at application startup.  
lazy loading is loading module only when required. The compiled js files are separated

## lifecycle hooks
most used ones
- ngOninit  
- ngOnDestroy - unsubscribe Observables to avoid memory leak 

## What is observable
Observable provide support to passing messages between publishers and subscribers. It is asychronous.

## Observable vs Promise
Observable is from Rxjs. Promise is from javascript. TODO

## security in Angular 
TODO
JWT (JSON web token)

## Interceptors
Intercept all requests and responses, useful for caching, error handling and logging...

## model view data bind
### single direction: model to view
```
<input type="text" [ngModel]="myInput" />
```
if the myInput on the component is changed, the browser can see the change right away.  
Change the value in the browser won't change the property of the component.  

### single direction: view to model
```
<input type="text" (ngModelChange)="modelChange($event)" [ngModel]="input" />
```
ngModelChange is an event. modelChange is a function on the component.

### dual directions
```
<input type="text"  [(ngModel)]="myInput" />
```

### [] in angular template
https://angular.io/guide/template-syntax#property-binding-or-interpolation
[] tells angular it is expression.  
The items below are the same
```
<p><img src="{{heroImageUrl}}"> is the <i>interpolated</i> image.</p>
<p><img [src]="heroImageUrl"> is the <i>property bound</i> image.</p>

<p><span>{{title}}</span></p>
<p><span [innerHTML]="title"></span></p>
```

### content security
https://angular.io/guide/template-syntax#content-security
```
evilTitle = '<script>alert("evil never sleeps")</script>';
```
```
<p><span>{{title}}</span></p>
<p><span [innerHTML]="title"></span></p>
```
Here is the result
```
"<script>alert("evil never sleeps")</script>" //only displayed, the script was not executed

alert("evil never sleeps")  //<script> was filtered out
```

### []() between parent and child 
The code below is a child inside the parent html file
```
<hero-detail (deleteRequest)="deleteHero($event)" [hero]="currentHero"></hero-detail>
```
[hero] means pass "currentHero" from the parent component into the child component hero-detail. In the child component there is @Input hero to receive the passed in object/value.    

(deleteRequest)="deleteHero($event)" is passing an event from the child to the parent. deleteRequest is an EventEmitter on child component and it is annotated with @Output 
```
@Output
deleteRequest = new EventEmitter<Hero>();

delete() {
  this.deleteRequest.emit(this.hero);
}
```
When the deleteRequest emit a value, it is going to trigger "deleteHeo" function on the parent component. The event is passed from the child component to the parent component.

## safe navigation operator ?.
```
The current hero's name is {{currentHero?.name}}
```

## $any type cast
Sometimes a binding expression will be reported as a type error and it is not possible or difficult to fully specify the type. To silence the error, you can use the $any
```
<div>
  The hero's marker is {{$any(hero).marker}}
</div>
```

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

## UI keystroke 
Here is a good solution for ui keystroke. One scenario is used for autocomplete
```
this.term.valueChanges
   .debounceTime(400)
   .distinctUntilChanged()
   .subscribe(...)
```
The distinctUntilChanged can avoid push the same value to the service. 
For example, the user input 'foo'. 'foo' is sent to the service. 
Then the user append 'bar' after 'foo' and contiuously press backspace 3 times. The value is still 'foo' which shall not be sent to the service. 

Here is the angular code
```
@Component({
  selector: 'my-app',
  template: `
    <div>
      <h2>Wikipedia Search</h2>
      <input type="text" [formControl]="term"/>
      <ul>
        <li *ngFor="let item of items | async">{{item}}</li>
      </ul>
    </div>
  `
})
export class App {

  items: Observable<Array<string>>;
  term = new FormControl();

  constructor(private wikipediaService: WikipediaService) {
    this.items = this.term.valueChanges
                 .debounceTime(400)
                 .distinctUntilChanged()
                 .switchMap(term => this.wikipediaService.search(term));
  }
}
```
service code
```
import { Injectable } from '@angular/core';
import { URLSearchParams, Jsonp } from '@angular/http';

@Injectable()
export class WikipediaService {
  constructor(private jsonp: Jsonp) {}

  search (term: string) {
    var search = new URLSearchParams()
    search.set('action', 'opensearch');
    search.set('search', term);
    search.set('format', 'json');
    return this.jsonp
                .get('http://en.wikipedia.org/w/api.php?callback=JSONP_CALLBACK', { search })
                .toPromise()
                .then((response) => response.json()[1]);
  }
}
```
https://blog.thoughtram.io/angular/2016/01/06/taking-advantage-of-observables-in-angular2.html  

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

## Pass Async Data to Child Components
1. ngIf
2. ngOnchanges
3. Subject

https://scotch.io/tutorials/3-ways-to-pass-async-data-to-angular-2-child-components  

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

## angular meterial: cursor cannot see when the background is dark
If the background is dark, the cursor in the matInput cannot be seen. To change that, in the css 
```
.mat-input-element {
    caret-color: auto;
}
```
If auto does not work, explicitly set it to be "white".
https://developer.mozilla.org/en-US/docs/Web/CSS/caret-color

## How can I watch for changes to localStorage in Angular2?
https://stackoverflow.com/questions/35397198/how-can-i-watch-for-changes-to-localstorage-in-angular2  

## angular proxy the backend service
In dev mode, we may want to proxy the backend service. For example, inject userId in http header.
here is the command to start the ng server with proxy
```
ng serve --proxy-config proxy-backend.conf.json
```
Here is the prxoy-backend.conf.json
```
{
    "/api": {
        "target": "http://localhost:3000",  //backend service to be proxy
        "secure": false,
        "logLevel": "debug",
        "headers": {
            "ifs_organizationid": "123456789",
            "ifs_subject": "123456" //injected values...
        }
    }
}
```

## angualr material menu not overlap the trigger icon
By default, the menu overlaps the trigger menu icon. To avoid that, using [overlapTrigger]="false" attribute.
https://material.angular.io/components/menu/overview  


## view real error for unit test 
I used to get this error when running unit test. 
```
Error: Failed to execute 'send' on 'XMLHttpRequest': Failed to load 'ng:///DynamicTestModule/DatasetCategoryComponent.ngfactory.js'
```
to view the real error, try this 
```
ng test -sm=false
```
or --source-map=false

## How to Modularize an Angular Application
Here is the article about how to create modules in angular project.  
https://dzone.com/articles/howto-modularize-an-angular-application-by-example?edition=435195&utm_source=Daily%20Digest&utm_medium=email&utm_campaign=Daily%20Digest%202019-01-07

## unit test observable, fakeAsync and flush
the code to be tested is like this. It does not change anything on the UI.
```
saveReport() {
    this.abcService.postReport(report.id, report.dataSetId, reportName)
      .subscribe(async() => {
          await this.removeAutoSavedReport(this).toPromise();
          this.navigateToViewMode();
      });
}

```
The function call inside the subscribe is async and you don't know when it is executed.  
To test it, angular provids fakeAsync and flush.
https://alligator.io/angular/testing-async-fakeasync/
flush basically simulates the passage of time until the macrotask queue is empty. 
```
it('test saveReport', fakeAsync(() => {
    spyOn...
    component.saveReport();
    flush();
    expect...;
  }));
```

## What is the difference between declarations, providers, and import in NgModule?
https://stackoverflow.com/questions/39062930/what-is-the-difference-between-declarations-providers-and-import-in-ngmodule  
Components are declared, Modules are imported, and Services are provided.  
- imports makes the exported declarations of other modules available in the current module
- declarations are to make directives (including components and pipes) from the current module available to other directives in the current module. Selectors of directives, components or pipes are only matched against the HTML if they are declared or imported.
- providers are to make services and values known to DI (dependency injection). They are added to the root scope and they are injected to other services or directives that have them as dependency.

## run lodash function from console
Sometimes I need run lodash functions from console when debugging angular project. The code is like this
```
_.filter(customers, {'id':636});
```

## ngrx 
- action: In the most common scenario, everything starts in the component view. Some interaction made by a user may cause the component to dispatch an action.
- states: readonly. The state of your whole application is stored in an object tree within a single store.
- store: A store is an object (an instance of the ngrx Store class) that brings the things we mentioned before (Actions, Reducers, Selectors) together. For example, when an action is dispatched (using the store object dispatch function), the store is the one finding and executing the appropriate reducer. It is also the one holding the application state.
- selectors: obtain slices of our store.
- effects: The Effects listen if any action is dispatched, then, similar to what reducers do, it checks if the action is one of the actions type it has a case for.
Then is going to perform a side-effect, usually getting or sending data to an API. Finally is going to emit another action, usually, an action referring to the result-state of the side effect (success, error, etc), then a reducer is going to enter in the scene as we already mention in the ngrx flow.
- reducers: pure function. The operation triggered by dispatching an action is called reducers. These reducers receive an action and the state, depending on the action dispatched (usually with a switch statement) they execute an operation and return a new state object.

Benefit
- Since we have a single source of truth and you can’t directly change the state, applications are going to behave more consistently.
- Using the redux pattern gives us a lot of cool features to make debugging easier.
- Applications become easier to test since we are introducing pure functions to handle changes in the state and also because both, ngrx and rxjs, have a lot’s great features for testing.
- As soon as you feel comfortable with using ngrx, understanding the flow of data in your applications becomes incredibly easy and predictable.

https://medium.com/frontend-fun/angular-ngrx-a-clean-and-clear-introduction-4ed61c89c1fc

## ngrx adapter




## angular support IE11
polyfills.ts, add this
```
if (!Object.values) { Object.values = o => Object.keys(o).map(k => o[k]); }
```
angular.json  
add this under "budgets"
```
"es5": {
          "tsConfig": "./tsconfig-es5.app.json"
        }
```
add this under "serve"."configurations"
```
"es5": {
          "browserTarget": "decm:build:es5"
        }
```
browserslist
```
not IE 9-10 # For IE 9-10 support, remove 'not'.
IE 11
```
package.json
```
"start": "ng serve --configuration es5",
...
"build:prod": "ng build --prod --extractCss --statsJson",
```
tsconfig-es5.app.json
```
{
    "extends": "./tsconfig.app.json",
    "compilerOptions": {
        "target": "es5" 
    }
}
```

## angular bundle size anglyzing
https://alligator.io/angular/angular-webpack-bundle-analyzer/
```
npm i webpack-bundle-analyzer -D
```
Add this to package.json
```
"scripts": {
  ...
  "build:stats": "ng build --stats-json",
  "analyze": "webpack-bundle-analyzer dist/www/stats.json"
}
```
Then 
```
npm run build:stats
npm run analyze
```
Webpack Bundle Analyzer is started at http://127.0.0.1:8888

