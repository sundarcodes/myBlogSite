---
title: "State Management in Angular 2+ apps - Part 3"
date: 2017-12-15
tags: ["State Management", "RxJS", "Observables", "Change Detection Strategy","Angular 2+"]
draft: true
---

In [part-1][part-1] of this series, we got to know what state is and the need to have a state management solution especially in the case of a large web app. In this post, we will be seeing how we can solve our shared state problem using Observables. We will also see how we can overcome the pitfalls of the Angular services approach discussed in [part-2][part-2] of this series.

Observables is something that I have seen which unsettles developers. It does take some time to settle in especially people coming from a imperative background like instance backend Java developers. We will be using RxJS, a library that implements observables and a load of other operators. Warning - RxJS again has a steep learning curve. You must give it time to sink in if you are new to this.

#### What/Why Observables ?
Let me try to explain this in the context of state management. Observables provides a mechanism to link a piece of data or state with a view or component that is interested in it by employing the [Observer][observer-design-pattern] Design Pattern. It simply connects the data or a subject to a list of Components or Observers. RxJS is the library which has the implementation of Observables. Infact, Angular has RxJS as a part of its dependency as the framework heavily uses these concepts and tonns of other utitlities in the RxJS library. If you need a detailed explaination, please do read this wonderful [blog][observables-blog] by the author of RxJS 5 BenLesh. One other misunderstanding of Observables is that its used for your async events like XHR, setTimeout etc. As clarified in BenLesh's blog post, Observables can be sync or async and it depends on the data source type. If the data has to be fetched from a backend, the observable would turn out to be async or if the data source is just an array stored in your service, it would be sync in nature.

#### How are we going to implement Observables ?
Now that we got the fundas out, lets look at how Observables and the RxJS library are going to help us manage our state. Talking in terms of the Observer pattern, our list of reads is going to be the subject which our home component and navbar component would be interested in. So we are going to wrap our shared state which is the `reads` collection inside a subject using RxJS BehaviorSubject class.

```
  private readsSubject: BehaviorSubject<GoodRead[]>;
  public allReads$: Observable<GoodRead[]>;
  public readsCounter$: Observable<number>;
```

We are declaring 3 variable here. `readsSubject` which is going to hold our reads collections and hence the term subject. `allReads$` is an observable which the home component would be interested in and `readsCounter$` is other obserable which the navbar component would need to be hooked into. We are basically modelling our state management problem to fit into solution given by the Observer pattern. BTW, prefixing a variable with `$` is just a convention to indicate it is an Observable type.

For the question on why we are using a BehaviorSubject instead of Subject, please look [here][why-behavior-subject].

One more point that you can notice is, we have declared our subject as private and observables as public. This is to take advantage of the data hiding features that typescript gives to inadvertently get access to the subject outside the service and updating it. You may also wonder, how we are going tie up the observables from the subject. Well, here we go. We do that in the contructor.

```
constructor(private http: HttpClient) {
    this.readsSubject = new BehaviorSubject([]);
    this.allReads$ = this.readsSubject.asObservable();
    this.readsCounter$ = this.allReads$.map((arr: GoodRead[]) => arr.filter(read => read.isRead).length);
  }
```
There is an `asObservable()` api exposed on the Subject which returns an observable hooked on to that subject. Any one interested in subscribing to this subject, could do so by subscribing to this observable. We could also directly subscribe to the Subject but that would mean exposing the Subject out to the consumers which violates the data hiding principle. For the `readCounter$` observable, we would just need to send out the number of good reads. Observables have a load of operators just like we have in `underscore` or `lodash` with which we could get the reads count out. It is similar to what we did in the Angular services approach.

Now in our components, all we need to do is to subscribe on these observables using Angular `async` pipe.

```
  <div class="col-sm-4"
      *ngFor="let readItem of backendService.allReads$ | async">
```

```
  <span class="badge badge-primary badge-pill">{{backendService.readsCounter$ | async}}</span>
```

You would be interested to know how the subject is getting updated, for instance lets say when we mark an item as read. Below it the code for that.
```
  markItem(id: number, isRead: boolean) {
    const url = `${this.baseAPIRURL}/update/${id}`;
    return this.http.patch<GoodRead>(url, {
      isRead
    })
      .do(rsp => {
        const allReads = this.readsSubject.getValue();
        const itemToBeUpdated = allReads.find(read => read.id === id);
        itemToBeUpdated.isRead = isRead;
        this.readsSubject.next(allReads);
      })
  }
```
We send a http PATCH request to the backend and once we get the response, we get the currently stored data in the subject using `getValue()`, then update the item that is marked and finally publish the updated array using `next()`. When we do a next on a subject, it triggers the call back function passed in the `subscribe` api of all the observables interested in this subject. You could see below in the home component, this markItem() function is being subscribed and you would the console log whenenver you check or uncheck an item.
```
 toggleItemRead(id: number, isRead: boolean) {
    this.subscriptions = this.backendService.markItem(id, isRead)
      .subscribe(() => {
        console.log(`Item marked as ${isRead ? 'read' : 'unread'}`);
      });
  }
```
Well, if you are getting to know about Observables/RxJS for the first time, this may feel little overwhelming initially, but once you get your head around it, you would really appreciate it. It took me good part of 3 months to finally sink in the Observables concept. So just be patient... By the looks of it, Observable could soon land up as a part of the browser API soon. Check out [this][observable-proposal].

Ok, coming back to our state management problem. Lets see how this approach eliminates the pitfalls of Angular Services approach discussed in [part-2][part-2].

[part-1]: {{< ref "ui-state-management-part-1.md" >}}
[part-2]: {{< ref "ui-state-management-part-2.md" >}}
[observables-mgmt-code-base]: https://github.com/sundarcodes/my-good-reads-app/tree/master/frontend/angular/observables-based-state-management/my-good-reads-app
[observer-design-pattern]: https://en.wikipedia.org/wiki/Observer_pattern
[observables-blog]: https://medium.com/@benlesh/learning-observable-by-building-observable-d5da57405d87
[why-behavior-subject]: https://stackoverflow.com/a/40231605
[observable-proposal]: https://github.com/whatwg/dom/issues/544