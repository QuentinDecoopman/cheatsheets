# Redux

- [Index](/Readme.md)
- [Doc](https://redux.js.org/)

## Installation

```bash
npm install @reduxjs/toolkit

npm i react-redux
```

## Actions

```js
const ADD_TODO = "ADD_TODO";

const addTodoAction = (text) => {
  return {
    type: ADD_TODO,
    payload: text,
  };
};
```

## Reducers

```js
const initialState = /* initial state */;

const todoReducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };
    default:
      return state;
  }
};

```

Ou

```js
const initialState = /* initial state */;

const todoReducer = createReducer(initialState, (builder) => {
  builder.addCase(ADD_TODO, (state, action) => {
    state.todos = action.payload;
  });
});
```

## Store

```js
import { configureStore } from "@reduxjs/toolkit";
import Reducer from "./reducers/NomReducer";

const store = configureStore({
  reducer: { todo: todoReducer },
});
export default store;
```

## Dispatch

```js
store.dispatch(addTodoAction("Buy groceries"));
```

## getState

```js
const updatedState = store.getState();
```

## Subscribe

```js
store.subscribe(() => {
  console.log("State updated:", store.getState());
});
```
