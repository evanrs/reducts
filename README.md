# Reduct
Redux enhancer for composable process modeling.

## Status
The concept is maturing, therefore I only document our ideal API which may not reflect the current implementation—or lack thereof.

## Motivation
Modelling a long running business process with Redux is too difficult. It is easy to reason about state changes in Redux because they are synchronous, however asynchronous changes are left unsolved.

Conventional wisdom suggests extending the store with thunks. In this solution we dispatch function closures that may themselves dispatch more closures, possibly without end. These are uncoordinated state transitions.

Without coordinated state transitions it is hard to maintain the promise of predictabiliy in Redux. To restore predictablity we might introduce more middleware to ochestrate specific transitions. But, as the size and scope of our middleware increases it hides more knowledge of the underlying process in a layer of indirection.

These solutions have low interoperability, they are not easiliy composed, and they require we adopt our own flavor of Redux.

Reduct provides tools for modelling state tranistions. With inspiration from [business process modeling](https://en.wikipedia.org/wiki/Business_process_modeling) Reduct seeks to make reasoning about dependent asynchronous events as predictable as Redux makes synhcronous events.

## Example
Using Reduct should produce an inteligent self documenting implementation.
```es6
duct(
  'When a request fails',
  duct.matches({ type: 'REQUEST_FAILED' }).distinct().foward('match'),
  'Display an error',
  duct.dispatches({ type: 'ALERT', payload: duct.receive('match') }).foward('alert'),
  'Stall any further requests until',
  duct.captures({ type: /REQUEST_/ }),
  'Until the user dismisses the alert',
  duct.matches({ type: 'DISMISS', id: duct.receive('alert.id') }),
  'Then dispatch pending requests',
  duct.releases()
)
```
This will produce stateful middleware to use with Redux.


Ideally we imagine this to be a fully serializable instruction set.
```es6
someDuct.toJSON() === {
  type: 'DUCT',
  duct: [
    'When matching request failures',
    { type: 'DUCT_MATCH',
      match: {
        type: 'REQUEST_FAILED'
      },
      distinct: true,
      forward: 'match'
    },
    'Display an error',
    { type: 'DUCT_DISPATCH',
      action: {
        type: 'ALERT',
        payload: {
          type: 'DUCT_RECEIVE',
          receive: 'match'
        }
      },
      foward: 'alert',
    },
    'Stall any further requests until',
    { type: 'DUCT_CAPTURE',
      action: {
        type: {
          type: 'DUCT_REGEX',
          regex: 'REQUEST_'
        }
      }
    },
    'Until the user dismisses the alert',
    { type: 'DUCT_MATCH',
      action: {
        type: 'DISMISS',
        id: {
          type: 'DUCT_RECEIVE',
          receive: 'alert.id'
        }
      }
    },
    'Then dispatch pending requests',
    { type: 'DUCT_RELEASE'
    }
  ]
}
```
Complex interactions could be added at runtime or from the backend.
