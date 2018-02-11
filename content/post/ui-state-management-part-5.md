---
title: "State Management in Angular 2+ apps - Part 5"
date: 2018-01-28
tags: ["State Management", "Redux", "ngRx", "Angular 2+"]
draft: true
---

In this last post of our series in state management, we will be seeing how we can solve our state problem using a solution called Redux. We will be use the angular variant of Redux called [ngRx][ngRx].

Redux has been inspired by [elm][elm-tutorial] and [Flux][flux]. Elm is a pure functional programming language used for developing the front end of modern day web apps. Flux is a stage management solution. If this is first time you are hearing about Redux, please do watch [these][dan-abramov-egghead] videos from the creator of Redux, [Dan Abramov][dan-abramov-twitter]. 

Redux works on 3 core concepts, lets try to understand each of them in the context of our goodreads app.

## Action
Actions are events that could update the state of your application. This could user generated events like mouse clicks, keypress, scroll etc, network events like XHR request, response, websockets or timer events like setTimeout() and setInterval().
## Store

## Reducer


[part-1]: {{< ref "ui-state-management-part-1.md" >}}
[part-2]: {{< ref "ui-state-management-part-2.md" >}}
[part-3]: {{< ref "ui-state-management-part-3.md" >}}
[ngRx]: https://github.com/ngrx/platform
[redux-github]: https://github.com/sundarcodes/my-good-reads-app/tree/master/frontend/angular/redux-based-state-management/my-good-reads-app
[dan-abramov-egghead]: https://egghead.io/series/getting-started-with-redux
[dan-abramov-twitter]: https://twitter.com/dan_abramov
[elm-tutorial]: http://elm-lang.org/
[flux]: https://facebook.github.io/flux/docs/overview.html