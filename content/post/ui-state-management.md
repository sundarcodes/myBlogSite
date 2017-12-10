---
title: "State Management in Angular 2+ apps"
date: 2017-12-01
tags: ["State Management", "RxJS", "Mobx", "Redux", "ngRx", "Angular 2+"]
draft: false
---

This is part-1 of the blog series where I would be trying to explain state management in Angular 2+ app using different strategies.

In this part, I will try to demystify the term state, how to identify state and why do we need to manage state. So lets get started...

### How do you define state or what is state of an app ?

I have seen developers who just have Angular(1 & 2+) background struggle to answer this question as they haven't encountered the term **state** while developing apps. This is where getting to know different alternative like React, Vue helps. React is built on the philosophy of UI is a function of state. Please read [this][ui-function-of-state] to understand more about the philosophy.

Coming back to the question, a state as per the definition represent an app data at a given point of time.
Programatically speaking, a state is nothing but the variables - objects, number, arrays that you use in your JS code to represent the data to be displayed in the UI. So now you can relate to the `UI=f(state)` principle which implies UI changes as state changes.

Lets take a sample app and try to decipher the state based on the above lines. Incidentally, we would be also using same app to apply different state management strategies. It is a simple CRUD app which has a collection of my good reads (books or blogs) and you could manage it meaning - add/edit/delete. Also if you notice there is counter which indicates the number of books/blogs that I have read. So what do you think would be the state of this app ?

![alt text][good-reads-landing]

To help us decipher the state of the app, lets try to understand the different kinds of state.

### Local State
Local State refers to the set of data stored in your variables that pertains to just the component, meaning the set of data that are not exposed or shared by some other components.

### Global/Shared State
Global or shared state refers to the set of data that is being shared by more than 1 component meaning 1 or more component is directly or indirectly being dependent on the data.

With respect to the our good-reads app, we could say the local state could the checkbox state in the individual cards to display a check mark or not. The shared state would be the list of good-reads as this is being shared by the list-container and also the header/navbar component which has the read counter.

![alt text][good-reads-state]

### Now the next question, why do we need to manage state ?

Okay thats a very valid question. Managing local state is easy as it is just going to reside within a single component, so we don't really have to care about managing it as we know whatever changes that happens to the data would only affect the component and nothing else. But when it comes to shared state, the same argument would not hold good. As the state is being shared, we would need to find a way to manage this sharing. Picture the following :

![alt text][state-management-compexity]

The above picture is a Venn diagram representation of apps where the circles represent components in the app. The intersection points represent the shared states amongst the components. As you could see, larger the app, more the components, more the intersections and more is the complexity of maintaining the state. Hence we need a state management solution !!!

There you go, that brings to us the end of this post. I would be covering the different state management techniques along with their pros and cons in the next series of post.
I have built the same good-reads app using the following state management techniques.. You could find the entire source code [here][github-repo]

* Managing state using Angular services - Part 2
* Managing state using RxJS (Subjects n Observables) - Part 3
* Managing state using Mobx via angular-mobx - Part 4
* Managing state using Redux via ngRx - Part 5

See you in the next post soon !!

[ui-function-of-state]: http://beletsky.net/2016/04/the-functional-approach-to-ui.html
[good-reads-landing]: /img/my-good-reads-landing.png
[good-reads-state]: /img/my-good-reads-with-state.png
[state-management-compexity]: /img/state-mgmt-complexity.png
[github-repo]: https://github.com/sundarcodes/my-good-reads-app