---
title: Custom Redux stores
description: Managing application state via Redux
---

Out-the-box, Apollo - the GraphQL client that ReactQL includes - uses [Redux](http://redux.js.org/) to manage its own internal query state.

You don't need to do anything for this. Apollo takes care of itself.

But what about other parts of your application? What if you want to store some kind of variables 'globally' across your app, and allow one or more React components to 'listen' to - or effect - the state?

That's where [Redux](http://redux.js.org/) comes back into play.

<h2 id="redux">How Redux works</h2>

---
[Redux](http://redux.js.org/)  is a library that adopts a [Flux-like](https://github.com/facebook/flux) pattern, that allows you to trigger 'actions' that affect the global state. Any component 'listening' to state automatically re-render in response to changes:

![Flux](images/flux-diagram.png)

[Redux](http://redux.js.org/) adopts a similar pattern, but simplifies a lot of the boilerplate usually required to make this work.

Instead of many 'stores' of state, your application has just one single state object, which you can apply 'reducers' to that affect changes to state.

<h2 id="example">An example: To-Do list</h2>

---
In the [ReactQL examples](https://github.com/reactql/examples) repo, there's a '[No GraphQL, Just Redux](https://github.com/reactql/examples/tree/master/no-graphql)' project that provides a full-blown example of implementing Redux.

You can clone this code, and run it locally.

For the rest of this doc, I'm going to show you how to replicate this repo _without_ removing GraphQL, so you can use the two side-by-side.

I encourage you to review the [Redux documentation](http://redux.js.org/) too, to get familiar with how Redux works. Rather than repeating concepts, I'll leave that to the library's author to tackle and instead focus on code.

<h2 id="reducers">Adding custom reducers</h2>

---
A '[reducer](http://redux.js.org/docs/basics/Reducers.html)' is a function that modifies the state of your application, and returns a copy.

This is what enables Redux to know that something has changed, and ultimately feed those changes back to any listening React components.

The first thing we need to do is create a reducer.

Since we're building a simple to-do list, let's create a reducer that has two simple functions: Adding a to-do, and removing one. We'll create this at `store/reducer.js`, since we'll only have one reducer throughout our application (the name's not important; if we have more than one reducer, we might call it 'reducers/todo.js' or something similar.)

**store/reducers.js**
```js
// Custom reducers.  We'll create a simple to-do list handler.
export default function reducer(state = [], action) {
  // eslint-disable-next-line default-case
  switch (action.type) {
    case 'ADD_TODO':
      if (action.todo && !state.includes(action.todo)) {
        return [...state, action.todo];
      }
      break;
    case 'REMOVE_TODO':
      return state.filter(todo => action.todo !== todo);
  }
  return state;
}
```

What's happening here?

Redux will pass in our current 'state' object. If we're calling this for the first time, we won't have an object, so by default we create an empty `state` array.

`action` will contain whatever our action (which we've not yet built) passes to the reducer. We'll get to that in a moment, but for now let's assume it'll have an `action.type` string, which tells our reducer what to do with the `state`.

If `action.type === 'ADD_TODO'`, then we check to ensure that same to-do item doesn't already exist on our state. If not, we'll create a *new* array, passing in all of the old values, along with our new to-do.

> Note: Creating a new array and not simply mutating the old one is a [key concept](http://redux.js.org/docs/recipes/UsingObjectSpreadOperator.html) of Redux. This allows us to keep our state 'clean' and free of side-effects, by each new action always resulting in a new/fresh copy of the state. We can then guarantee our React components are never relying on stale data, and it also makes it trivial to implement 'undo' features that travel back to previous states.

If `action.type === 'REMOVE_TODO'`, we'll pass back a new array with the todo filtered out. Again, note that we're not just removing it from the current array -- we pass back a fresh copy, just with that value omitted.

Once we've built our reducer, we need to add it to our Redux instantiation code that both the server and browser will use to wire up Redux.

Edit [kit/lib/redux.js](https://github.com/reactql/kit/blob/master/kit/lib/redux.js) by importing your reducer and adding it to the `combineReducers()` object that you're passing:

**kit/lib/redux.js**
```js
// ...
// Import the new reducer we've just created
import todoReducer from 'store/reducer';
// ...
// Add it to the existing `combineReducers()` call, alongside Apollo
export default function createNewStore(apolloClient) {
  const store = createStore(
    combineReducers({
      apollo: apolloClient.reducer(),
      todos: todoReducer, // <--- this is where we've added our custom reducer!
    }),
    !SERVER ? window.__STATE__ : {}, // initial state
    compose(
        applyMiddleware(apolloClient.middleware()),
        (!SERVER && typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== 'undefined') ? window.__REDUX_DEVTOOLS_EXTENSION__() : f => f,
    ),
  );

  return store;
}
```

Note that we've given the reducer object a `todos` root keyword. This means changes to the state will affect _only_ that 'part' of the state. Apollo has its own `apollo` root, to avoid clashing with other 'parts' of our application state.

That's it - we now have a custom reducer that's concerned with responding to our to-do actions

<h2 id="actions">Adding custom actions</h2>

---
Now, we need to create a couple of simple '[actions](http://redux.js.org/docs/basics/Actions.html)' that will 'notify' our reducer when something needs changing.

Create a file called `store/actions.js` with this:

**store/actions.js**
```js
export function addToDo(todo) {
  return {
    type: 'ADD_TODO',
    todo,
  };
}
export function removeToDo(todo) {
  return {
    type: 'REMOVE_TODO',
    todo,
  };
}
```

Actions are super simple. They basically just return an object with a `type` key (that tells Redux what type of action this is) and then whatever other data we need to carry along with the request.

Remember the `switch()` statement in our reducer that looks at `action.type` to respond to `ADD_TODO` and `REMOVE_TODO`? The return statement of our action gets fed into the second parameter of our reducer, which is how we know what kind of action was sent.

We'll use these actions in the next step, inside our components.

<h2 id="components" title="React components">Adding React components that 'listen' to or 'trigger' state changes</h2>

---
Finally, we just need to set-up our React components that either 'listen' to store state, or trigger actions necessary to change it.

Editing [src/app.js](https://github.com/reactql/kit/blob/master/src/app.js), we'll write some components that do just that.

**src/app.js**
```
// ----------------------
// IMPORTS
// ...
//
// @connect decorator to inject store state and dispatching
import { connect } from 'react-redux';
// Actions to add/remove to-do
import { addToDo, removeToDo } from 'store/actions';
//
// To-do components.  This will demonstrate Redux at work.
//
// Add a new to-do
@connect() class AddToDo extends React.PureComponent {
  setRef = ref => { this.ref = ref; }
  addToDo = () => {
    this.props.dispatch(addToDo(this.ref.value));
    this.ref.value = '';
  };
  render() {
    return (
      <div>
        <input ref={this.setRef} type="text" />
        <button onClick={this.addToDo}>Add to-do</button>
      </div>
    );
  }
}
//
// List the to-dos
@connect(state => ({ todos: state.todos }))
class ListToDos extends React.PureComponent {
  static propTypes = {
    todos: PropTypes.arrayOf(
      PropTypes.string,
    ),
  }
  // Return a thunk that dispatches the `removeToDo`
  removeToDo = todo => () => {
    this.props.dispatch(
      removeToDo(todo),
    );
  }
  render() {
    const { todos } = this.props;
    let inner;
    if (!todos.length) {
      inner = (
        <h4>No to-dos yet. Add one.</h4>
      );
    } else {
      inner = (
        <ul>
          {todos.map(todo => (
            <li key={todo}>
              {todo}
              <button onClick={this.removeToDo(todo)}>Remove</button>
            </li>
          ))}
        </ul>
      );
    }
    return (
      <div className={css.todos}>
        {inner}
      </div>
    );
  }
}
// Wire up the above components into a single `<ToDoHandler>`
const ToDoHandler = () => (
  <div>
    <AddToDo />
    <ListToDos />
  </div>
);
```

There's a lot happening here, so let's break it down.

First, we import a function called `connect()` from the [react-redux](https://github.com/reactjs/react-redux) library. This provides our functions with a 'higher-order component' that injects in state/action dispatching via props.

We then import our custom add/remove to-do actions.

In our `<AddToDo>` component, we use the `connect` function as a decorator. This function automatically passes a `dispatch()` function to our props. It's this function that allows us to tell Redux that an action has been triggered.

We wire up a simple form. The input registers itself on `this.ref`, which allows us to easily grab the value in the text box. The button fires off the dispatch event, with the text box value. The call to `addToDo()` returns a full object that then looks like this:

```js
{
  type: 'ADD_TODO',
  todo: 'Whatever you entered in the input box'
}
```

This gets fed down to our reducer, which returns back the 'new' state which now looks like this:

```js
[
  'Whatever you entered in the input box'
]
```

If you enter something new into the input box and hit the button again, the state would now be:

```js
[
  'Whatever you entered in the input box',
  'Something new that you entered'
]
```

So, now we have our means to *add* to our state.

We then create the component that *listens* to the state -- our `<ListToDos>`.

Here, we use the same `@connect` decorator, but this time we pass in a function that tells `connect` which parts of our application state we want to listen to.

Since we have access to the FULL state object (including the `apollo` root key!), and we only care about the `todos` key, we'll return an object with just that part.

Inside our component, we now have access to the `todos` via `this.props.todos`. Our component is then just basic Javascript - we check that `this.props.todos` isn't an empty array (if it is, we encourage the user to add a to-do) and display them as a `<li>` list.

Finally, we wire up another action - this time the `removeToDo` - to change the state of the application once again.

> Note: Updating React components is done declaratively. We don't 'tell' React to update - the `connect` HOC automatically updates the `props` that are fed in to the component and thus React implicitly re-renders when the state data changes.

<h2 id="demo">Demo</h2>

---
Now if we fire up our project with `npm start`, we see this:

![No-todos](images/examples/redux1.png)

If we add a to-do to the input box and click the button, we get:

![Our first to-do](images/examples/redux2.png)

Pretty cool!

But even cooler, is that we can do the same on the *server*....

<h2 id="server">Server-side state</h2>

---

Let's imagine that our initial to-dos are loaded from a database, and we want to populate the first render back to the screen with those to-dos intact.

If we do this only in the browser, we might initially see a 'flicker' where the `todos` state object is empty, and then the `<li>` items suddenly populate at some time in the future whenever the data has been loaded.

That's not a great experience for the user, who may wonder why the list that says 'no to-dos' suddenly changes on them.

Instead, let's inject the _initial_ state on the server, and let the client automatically rehydrate this state for use in the browser.

We already have all of the pieces in play to do that - ReactQL does that out-the-box. All we need to do is tell the server what our initial state is. Let's edit [kit/entry/server.js](https://github.com/reactql/kit/blob/master/kit/entry/server.js) to simulate adding our first to-do:

**kit/entry/server.js**
```js
// ...
// Import our 'AddToDo' action
import { addToDo } from 'store/actions';
// ...
// Inside the `reactHandler` function...
// Create a new Redux store for this request
const store = createNewStore();
//
// Add a to-do on the server side, to demonstrate how the state is
// sent back down the wire and dehydrated by the browser
store.dispatch(
  addToDo('This To-Do was sent from the server!'),
);
//
// ... continue with the rest of our server code
```

If we fire up our development web server, we now see this:

![From the server](images/examples/redux3.png)

Awesome! Our browser has 'rehydrated' the state sent back down from the server, and continued where the server finishes.

If we check out the source code, we can see the 'to-do' data has been added to `window.__STATE__`:

![SSR source](images/examples/redux4.png)

That's how the browser knows what the initial state is. See [server-side rendering](ssr.html) for more info on how this works

<h2 id="devtools">Redux DevTools</h2>

---
In the Chrome webstore, there's an awesome free [Redux DevTools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd) extension that lets you view Redux state from within the Developer console.

Remember this line from [kit/lib/redux.js](https://github.com/reactql/kit/blob/master/kit/lib/redux.js)?

```js
(!SERVER && typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== 'undefined') ? window.__REDUX_DEVTOOLS_EXTENSION__() : f => f,
```

This gives the Redux DevTools extension what it needs to attach itself to the Redux state, which then activates comprehensive tooling to debug most every aspect of Redux:

![Redux DevTools](images/examples/redux5.png)

You can even dispatch your own action objects from within here, to simulate what happens when firing from within functions. Try this one an see what happens:

```js
{
  type: 'ADD_TODO',
  todo: 'Watch me magically appear on the screen!'
}
```

Enjoy!
