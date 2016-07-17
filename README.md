# Reduct
Redux enhancer for composable process modeling.

## Motivation
Modelling a long running business process with Redux is too difficult. It is easy to reason about state changes in Redux because they are synchronous, however asynchronous changes are left unsolved.

Conventional wisdom suggests extending the store with thunks. In this solution we dispatch function closures that may themselves dispatch more closures, possibly without end. These are uncoordinated state transitions.

Without coordinated state transitions it is hard to maintain the promise of predictabiliy in Redux. To restore predictablity we might introduce more middleware to ochestrate specific transitions. But, as the size and scope of our middleware increases it hides more knowledge of the underlying process in a layer of indirection.

These solutions have low interoperability, they are not easiliy composed, and they require we adopt our own flavor of Redux.

Reduct provides tools for modelling state tranistions. With inspiration from [business process modeling](https://en.wikipedia.org/wiki/Business_process_modeling) Reduct seeks to make reasoning about dependent asynchronous events as predictable as Redux makes synhcronous events.
