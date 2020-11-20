# What Is

## Context

Context is the baked in Redux for React. It has been around for awhile and only recently been 'blessed' to use by the React overlords.

I've used it for awhile on different projects for school and other side projects. It works really well for things like authentication and creating your app's own API.

## Hooks

HOOKS! EVERYONE IS TALKING ABOUT HOOKS!

When can I use it? Now!

Why would I want to use it? Because!

What do I need to do to start using it? Nothing!

Who can use it? Everyone!

Where can I use it? Functional components and custom made hooks!

How can I use it? I'll tell you later!

# How?

## Hooks First!

Told you I'd tell you. Let's go over hooks first since there are hooks in my Context example.

[Insert picture of old Reese's commercial. You got context in my hooks! You got hooks in my context!]

### How to Use Hooks

There are a few hooks that come baked into React so we'll go over these first and then over a custom hook I made for Context. Also these are only for functional components and we can replace many of the class components with functional components by using hooks.

### Baked In Goodies

Import these with React like so `import React, { useState } from 'react';` or call them like so `React.useState()` but we're going to import them...right? Right.

#### Basic Hooks

- useState()
- useEffect()
- useContext()

#### Additional Hooks

- useReducer()
- useCallback()
- useMemo
- useRef
- useImperativeHandle
- useLayoutEffect()
- useDebugValue

### useState()

`const [state, setState] = useState(initialState);`

First hook!!!!!!!!!!!!!!!!!!!!!!!!!!!

It will return a stateful value and a function to update that stateful value.

Whatever is in the () will be the initial state. For example:

`const [children, setChildren] = useState(['Annabelle','Abigail']);`

To update the state you just use the new function you just made.

```js
  const newChildren = ['Annabelle', 'Abigail', 'Adamarie'];
  setChildren(newChildren);
```

### useEffect()

This is the componentDidMount, componentDidUnmount, and componentDidUpdate life cycle methods comfortably squeezed into one hook. It just depends how you form it where it handles one, two, or all three methods.

Example time:

```js
// This is simple useEffect no cleanup no re-render on update. Will run after every render
useEffect(() => {
  const whining = props.child.whining();
});

// As you can see...this isn't usually wanted.
// This useEffect does cleanup which will end the whining after the component is unmounted
useEffect(() => {
  const whining = props.child.whining();
  return () => {
    whining.stop();
  }
});

// This one will run once after the component is mounted
useEffect(() => {
  const whining = props.child.whining();
}. []);

// Suddenly an array appears! The array is used to signify what will trigger a rerun of the useEffect hook. An empty array says nothing will cause it to rerun and so it'll only run once.

// This next one will update when the child is updated
useEffect(() => {
  const whining = props.child.whining();
}, [child]);

// And for coverage, here's all three life cycle methods. This will run on mount, after the child is updated, and cleanup and unmount.
useEffect(() => {
  const whining = props.child.whining();
  return () => {
    whining.stop();
  }
}, [child]);

```

#### Async useEffect()

Sometimes you'll want to use useEffect and grab data with an API call. Well, useEffect isn't built to be asynchronous. You can however define an asynchronous function inside the useEffect.

```js
const dispatch = useDadDispatch();
async const fetchData = React.useCallback(() => {
  dispatch({ 'GET' });
  try {
    const responseJson = await fetch('/dad-joke', {
      method: 'GET',
      cache: 'no-cache',
      credentials: 'same-origin',
      headers: {
        Accept: 'application/json',
      },
    }).then(r => r.json());
    dispatch({ type: 'GOT', response: responseJson });
  } catch (err) {
    dispatch({ type: 'FETCH_ERROR', errorMessage: 'Something went wrong' });
  }
}, [isDadAwake]);
useEffect(() => {
  // Then immediately call it.
  fetch(data);
}, []);
```

### useContext()

Takes a context object...which we will talk about later. Then returns the value.

### useReducer()

This is the only additional hook I'll cover. The rest I've not used and won't speak to them.

This one is like a bunch of `useState()`s smooshed together.

`const [state, dispatch] = useReducer(reducer, initialState, init);`

It returns state and dispatch. Work the same way as they do in Redux.

## How to Use Context

There are many different methods to using context, some are more complicated than others (I'm looking at you React docs). Other methods pack everything nicely and it doesn't change much of existing code! That's what we'll use.

### Context File Structure

Context gets it's own directory just like State. Because it is state. Just a different flavor. Like a Mountain Dew flavor. Redux is Voltage and Context is Code Red. Obvious winner there.

Each different context can either have it's on sub-directory or just one file. I would recommend having another directory that contains multiple files because the context files can get quite large and it could be more readable to split them up like we do with Redux.

```md
/context
  |
   /DadContext
    |
     actions.js
     hooks.js
     index.js
     provider.jsx
     reducer.js
     services.js
     types.js
   /MomContext
    |
     actions.js
     hooks.js
     index.js
     provider.jsx
     reducer.js
     services.js
     types.js
```

On to the example. Remember to read the comments.

```js
// actions.js
import {
  GET_BY_ROLE_REQUEST,
  GET_BY_ROLE_SUCCESS,
  GET_BY_ROLE_FAILURE,
  GET_ALL_REQUEST,
  GET_ALL_SUCCESS,
  GET_ALL_FAILURE,
} from './types';
import { service } from './services';

export const getAll = dispatch => {
  const request = () => ({ type: GET_ALL_REQUEST });
  const success = parents => ({ type: GET_ALL_SUCCESS, parents });
  const failure = error => ({ type: GET_ALL_FAILURE, error });
  dispatch(request());
  service.getAll()
    .then(
      parents => { dispatch(success(parents)) },
      error => { dispatch(failure(error.message)) },
    );
};

export const getById = (parentRole, dispatch) => {
  const request = role => ({ type: GET_BY_ROLE_REQUEST, role });
  const success = (role, selectedParent) => ({ type: GET_BY_ROLE_SUCCESS, role, selectedParent });
  const failure = (role, error) => ({ type: GET_ID__FAILURE, role, error });
  dispatch(request(parentRole));
  service.getById()
    .then(
      selectedParent => { dispatch(success(parentRole, selectedParent)) },
      error => { dispatch(failure(parentRole, error.message)) },
    );
};
```

```js
// hooks.js
import { useContext } from 'react';
import { ParentsStateContext, ParentsDispatchContext } from './provider';

export const useParentsState = () => {
  const context = useContext(ParentsStateContext);
  if (context === undefined) {
    throw new Error('useParentsState must be used within a ParentsProvider');
  } return context;
};

export const useParentsDispatch = () => {
  const context = useContext(ParentsDispatchContext);
  if (context === undefined) {
    throw new Error('useParentsDispatch must be used within a ParentsProvider');
  } return context;
};
```

```js
// index.js
export * from './actions';
export * from './hooks';
export * from './provider';
export * from './reducer';
export * from './services';
export * from './types';
```

```jsx
// provider.jsx
import React, { createContext, useReducer } from 'react';
import { node, instanceOf } from 'prop-types';

// Contexts...splitting up state and dispatch will help readability and performance.
const ParentsStateContext = createContext();
const ParentsDispatchContext = createContext();

// Providers -- Here's where things change a bit. You need a Provider to consume the context in the application. This is that provider.
function ParentsProvider({ children, mockParents, mockSelectedParent }) {
  const [state, dispatch] = useReducer(masterReducer, INITIAL_STATE);
  let data;
  if (mockParents || mockSelectedParent) {
    data = {
      parents: mockParents,
      selectedParent: mockSelectedParent,
    };
  } else {
    data = state;
  }

  return (
    <ParentsStateContext.Provider value={data}>
      <ParentsDispatchContext.Provider value={dispatch}>
        {children}
      </ParentsDispatchContext.Provider>
    </ParentsStateContext.Provider>
  );
};

// Provider PropTypes -- IF you have multiple providers in one context you can define propTypes once and use it for all the providers since it will be the same.
ParentsProvider.propTypes = {
  children: node.isRequired,
  mockParents: instanceOf(Object),
  mockSelectedParent: instanceOf(Object),
};
```

```js
// reducer.js
import {
  GET_ALL_REQUEST,
  GET_ALL_SUCCESS,
  GET_ALL_FAILURE,
  GET_BY_ROLE_REQUEST,
  GET_BY_ROLE_SUCCESS,
  GET_BY_ROLE_FAILURE,
  RESET_PARENTS,
  RESET_PARENT,
} from './types';

export const INITIAL_STATE = {
  parents: [],
  selectedParent: {},
  error: {
    isError: false,
    message: '',
  },
};

export default (state, options) => {
  const {
    type, selectedParent, error, parents,
  } = options;
  switch (type) {
    case GET_ALL_REQUEST:
      return { loading: true };
    case GET_ALL_SUCCESS:
      return { parents, loading: false };
    case GET_ALL_FAILURE:
      return { error, loading: false };
    case GET_BY_ROLE_REQUEST:
      return { loading: true };
    case GET_BY_ROLE_SUCCESS:
      return { selectedParent, loading: false };
    case GET_BY_ROLE_FAILURE:
      return { error, loading: false };
    case RESET_PARENT:
      return {
        ...state,
        selectedParent: INITIAL_STATE.selectedParent,
      };
    case RESET_PARENTS:
      return { INITIAL_STATE };
    default: return state;
  }
};
```

```js
// services.js
import axios from 'axios';

const getAll = async () => {
  const url = '/parents/';
  try {
    const response = await axios.get(url);
    return response.data;
  } catch (error) {
    throw new Error(error?.response?.message);
  }
};

const getById = async (id) => {
  const url = `/parents/${id}`;
  try {
    const response = await axios.get(url);
    return response;
  } catch (error) {
    throw new Error(error?.response?.message);
  }
};

export const service = {
  getById,
  getAll,
};

```

```js
// types.js
export const GET_ALL_REQUEST = 'GET_ALL_REQUEST';
export const GET_ALL_SUCCESS = 'GET_ALL_SUCCESS';
export const GET_ALL_FAILURE = 'GET_ALL_FAILURE';
export const GET_BY_ROLE_REQUEST = 'GET_BY_ROLE_REQUEST';
export const GET_BY_ROLE_SUCCESS = 'GET_BY_ROLE_SUCCESS';
export const GET_BY_ROLE_FAILURE = 'GET_BY_ROLE_FAILURE';
export const RESET_PARENTS = 'RESET_PARENTS';
export const RESET_PARENT = 'RESET_PARENT';

```

On to the UI side! **Remember to read the comments**

```jsx
// Application.jsx or whatever
import React from 'react';
import { string, instanceOf } from 'prop-types';
import ParentsConfigurationContainer from './ParentsConfigurationContainer';
import { ParentsProvider } from '../context/Parents';
import '../styles/main.scss';

const propTypes = {
  /** Logged in user's locale from Portal. */
  locale: string,
  /** Logged in user's object. */
  user: instanceOf(Object),
};

// Neat and pretty UI export. Look how clean it looks. OOOOOOO AAAAAAHHHH.
const ParentsConfigurationApplication = ({ user, locale }) => {
    const intl = React.useContext(ApplicationIntlContext);
  const userData = {
    name: `${user.firstName} ${user.lastName}`,
    initials: `${user.firstName.slice(0, 1)}${user.lastName.slice(0, 1)}`,
  };
  return (
    <ParentsProvider>
      <ParentsConfigurationContainer user={user} />
    </ParentsProvider>
  );
}

export default (props) => (
  // eslint-disable-next-line react/prop-types
  <ParentsConfigurationApplication {...props} />
);
```

That's pretty much it. It can get more complicated and will. This will get us started.

If you have any questions ~~swing by my desk~~ (don't come to my house please) or ping me on Teams.
