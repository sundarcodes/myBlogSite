---
title: "State Management in Angular 2+ apps - Part 2"
date: 2017-12-11
tags: ["State Management", "Services", "Change Detection", "Angular 2+"]
draft: true
---

In [part-1][part-1] of this series, we got to know what state is and the need to have a state management solution especially in the case of a large web app. In this post, we will be seeing howe we can solve our shared state problem using plain Angular services. This

In the last post we identified that the good reads collection is the data that is being shared by the home component and the navbar component. So we know that when we need to share data between components, we just need to introduce a service, place the data in the service and inject the service in components that needs it. This is precisely what we will be doing here.

```javascript
@Injectable()
export class BackendService {
  baseAPIRURL = 'http://localhost:3000/api';
  reads: GoodRead[];
  subs: Subscription;
  constructor(private http: HttpClient) {
    this.reads = [];
  }
```

We have a `BackendService` which is going to hold our collection in the reads variable. `GoodRead` is our model class which is going to have all attributes of blog/book like title, description, catergory etc.

```javascript
export class GoodRead {
    constructor(public title: string, public description: string, public category: string,
        public url: string, public isRead: boolean, public id?: number) {

    }
}
```

The service is going to talk with the backend and get the data and store it back in the `reads` variable.

```javascript
 fetchAllReads() {
    const url = `${this.baseAPIRURL}/index`;
    this.subs = this.http.get<GoodRead[]>(url)
      .subscribe(data => this.reads = data);
  }
```

And we would be exposing this `reads` array using a getter.

```javascript
get allReads(): GoodRead[] {
    return this.reads;
  }
```

Now in our home.component.ts, we would inject this service and in the home.component.html, we would be using this the service in our html to render the items in the DOM.
```
    <div class="col-sm-4"
      *ngFor="let readItem of backendService.allReads">
      <div class="card">
        <div class="card-body">
```

Now to display the number of items read, we expose a getter in the `BackendService`. I will come to the console.log statement in just a while.

```
  get allReadsCount(): number {
    console.log(`Reading counter at ${Date.now()}`);
    return this.reads.filter(read => read.isRead).length;
  }
```

We would using this method in the navbar component to display the number of reads.
```
  <ul class="nav">
    <li class="nav-item">
       Read
      <span class="badge badge-primary badge-pill">{{backendService.allReadsCount}}</span>
    </li>
  </ul>
```

Please look [here][service-mgmt-code-base] for the complete code base.

We have achieved what we wanted but is this the best solution. Of course, this is the simplest and more importantly it works!! But it sure has its own pitfalls. Lets see what are those.

### Pitfalls

##### Relying on Angular's Change Detection
Here, you have to understand how Angular magically updates the __DOM__ whenever the `reads` variable in the `BackendService` gets updated. This happens through a change detection mechanism that gets triggered on every event that happens and Angular checks all the variables referenced in the HTML to see if its reference or value has changed. If there is a change, Angular rerenders the correspoding part of the DOM.


    

[part-1]: {{< ref "ui-state-management-part-1.md" >}}
[service-mgmt-code-base]: https://github.com/sundarcodes/my-good-reads-app/tree/master/frontend/angular/services-based-state-management/my-good-reads-app
