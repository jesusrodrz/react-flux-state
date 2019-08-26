

# React Flux State

React bindings to [flux-state](https://github.com/cobuildlab/flux-state) Library

This package ads a `subscribe` method to the standard React Component to avoid the unsubscribe boilerplate from the library, AKA: it does the unsubscribe for you

## Installation

1. Run on your terminal the following command:
```sh
$ npm i --save-dev @cobuildlab/react-flux-state
```
2. To import the library anywhere you would like to use it:
```js
import View, {useFluxStore} from '@cobuildlab/react-flux-state';
```

## Let's build a Flux Workflow for authentication

### 1) First, declare your Store

```js
import Flux from 'flux-state';

// Declare the events as constants. They are exported for reference in the subscriptions
export const LOGOUT = 'onLogout';
export const LOGIN = 'onLogin';


class SessionStore extends Flux.Store{
    constructor(){
        super();
        // Declare an Event
        this.addEvent(LOGOUT);
        // Or Declare an event with some imutable transformation logic
        this.addEvent(LOGIN, (state) => {
            // Do something with the data before propagating the Event
            return Object.assign(state, {"key": "value"})
        });
        // Or Declare an event with some plain transformation logic
        this.addEvent(LOGIN, (state) => {
            state.some_other_property = "Some other Data";
            return Object.assign(state, {"key": "value"})
        });
    }
}
export default new SessionStore();
```

### 2) Registering with the Store changes

```js
import React from 'react';
import SessionStore, {LOGIN, LOGOUT} from '/path/to/store';
import View, {useFluxStore} from 'react-flux-state';

// Class Based
class View extends View {
      constructor(){
          super();
      }
      componentDidMount() {
          this.subscribe(SessionStore, LOGIN, (state) => {
              // Do something usefull with the Event Data
              const userName = state.user.name;
              this.setState({userName});
          });
          // Register some method
          this.subscribe(SessionStore, LOGOUT, this.logOutEvent);
      }

      logOutEvent = (state) => {
        //DO something with the state or the state of the Store
        const storeState = SessionStore.getState()
      }
  }

// or Functional
const View = (props) => {
  // Set an Initial Value
  const loginState = useFluxStore(SessionStore, LOGIN, null);
  const userState = useFluxStore(SessionStore, USER);
  return (
    {loginState && <User user={loginState}>}
  )
};
```

### 3) Define some action that will trigger the event

```js
import Flux from 'flux-state';
import {LOGIN, LOGOUT} from '/path/to/store';

const authenticateAction = (username, password)=> {
      // Don't forget to Validate the data ex: username !=== undefined
      let dataToSave = {
          authenticated: true,
          username,
          password,
      }
      Flux.dispatchEvent(LOGIN, dataToSave)
}

export default {authenticateAction};
```
## Changelog

### v2.0.0:

- React Hook Implementation signature changed, now receives a callback to be executed when the event is triggered: `useFluxStore`

### v1.0.0:

- React Hook Implementation: `useFluxStore`
- Migration to `@cobuildlab/react-flux-store`

## Contributors

- Alejandro Sanchez [github.com/alesanchezr](https://github.com/alesanchezr) [alesanchezr.com](http://alesanchezr.com)
- Angel Lacret [github.com/alacret](https://github.com/alacret)
- Allan Thinks [github.com/alanthinks](https://github.com/alanthinks)
