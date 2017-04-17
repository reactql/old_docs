---
title: Styleguide
description: Tweaked Airbnb ESLinting - with extra super powers
---

<h2 id="airbnb">Airbnb+</h2>

---
If you're using an editor to write code against this starter kit with [ESLint](http://eslint.org/) powers, you might notice squiggly red lines under perfectly innocent blocks of Javascript code.

That's because this starter kit's [ESLint](http://eslint.org/) config is based on [Airbnb style guide](https://github.com/airbnb/javascript), which enforces consistently across your code base.

ReactQL extends the Airbnb config in a few ways:

<h2 id="async_await">Async/await</h2>

---
You'll find plenty of `async/await` code already used in the starter kit, including route definitions in the built-in server bundle:

```js
// We use this middleware in Koa 2 to time how long the request takes
app.use(async (ctx, next) => { // <--- routes are defined as async functions
  const start = ms.now();
  await next(); // <-- we await the render chain before continuing
  const end = ms.parse(ms.since(start));
  const total = (end.microseconds + (end.milliseconds * 1e3) + (end.seconds * 1e6)) / 1e3;
  ctx.set('Response-Time', `${total}ms`); // <-- we can manipulate the header later
});
```

Async functions are 'native' in Node 7.6 and above. In earlier versions (and in the browser), a async/await polyfill will be added into your resulting bundle automatically.

<h2 id="generators">Generators, for...of</h2>

---
Generators are valid, and so are `for...of` loops that take advantage of them, so you can run code like this:

```js
function* getPages() {
  for (let count = 0; count < 10; count++) {
    yield someAsyncMethod(`http://example.com/page/${count}`);
  }
}
// Process each Promise sequentially
(async function(){
  for (let page of getPages()) {
    await page;
    // do something with `page` before retrieving the next one...
  }
})();
```

In the majority of cases, you probably don't want to write 'blocking' code that awaits Promises sequentially, but ReactQL doesn't get in the way of legitimate use cases.

<h2 id="decorators">Decorators</h2>

---
@decorator syntax is enabled through [babel-plugin-transform-decorators-legacy](https://github.com/loganfsmyth/babel-plugin-transform-decorators-legacy), so you can decorate classes like so:

```js
@graphql(gql`
  query TodoAppQuery {
    todos {
      id
      text
    }
  }
`)
export default class TodoApp extends PureComponent {
  render() {
    const { data: { todos } } = this.props;
    return (
      <ul>
        {todos.map(({ id, text }) => (
          <li key={id}>{text}</li>
        ))}
      </ul>
    );
  }
}
```

<h2 id="stateless" title="Stateless React">Use stateless React components</h2>

----
When you're writing React components that only deal with `props` or `context`, it's usually best to write them as 'stateless' components like this:

```js
const Button = props => (
  <button className={props.active ? 'active' : ''}>Some text</button>
);
```

React will pass `props` as the first parameter, and `context` as the second. This allows you to avoid writing classes with a `render()` method, if all you want to do is create a simple component.

On the flip-side, this _will_ raise an error with the linter:

```js
// Don't do this -- it will result in a linting error!
class Button extends React.Component {
  render() {
    return (
      <button className={this.props.active ? 'active' : ''}>Some text</button>
    );
  }
}
```

Alternatively, if you _do_ want to use classes with a single `render()` method, extend from `React.PureComponent` and make sure you're using at least one of `this.props` or `this.context` to avoid linting errors-- this is especially useful if you want to use [decorators](styleguide.html#decorators), like this:

```js
@graphql(query)
// This will work fine -- the linter will be happy
class GraphQLMessage extends React.PureComponent {
  // It's okay to have a single `render` here, so long as we are extending
  // from `React.PureComponent` and are using at least `this.props` or
  // `this.context`
  render() {
    const { data } = this.props;
    const message = data.allMessages && data.allMessages[0].text;
    const isLoading = data.loading ? 'yes' : 'nope';
    return (
      <div>
        <h2>Message from GraphQL server: <em>{message}</em></h2>
        <h2>Currently loading?: {isLoading}</h2>
      </div>
    );
  }
}
```

<h2 id="multiple_react" title="Multiple React per page">Multiple React components per page</h2>

----
Generally, it's a good idea to give your React components their own `.js` file. But sometimes, you might write 'helper' components that won't get used outside a parent context.

ReactQL lets you write multiple components per file, without generating linter warnings.


<h2 id="propTypes" title="PropTypes">PropTypes must be valid</h2>

---
React `propTypes` should be a known shape-- 'any' is forbidden.

This gives you richer console messages in development in the event your React components are missing data, or receiving props they're not expecting.

<h2 id="fat_arrow" title="Fat-arrow single props">Singular fat-arrow params don't need brackets</h2>

----
Fat-arrow parameters need only be wrapped in parentheses when there's more than one of them.

So this is perfectly valid:

```js
// props, not (props)
const HOC = props => <div>{props.message}</div>;
```

<h2 id="line_breaks" title="Linebreaks">Windows + Linux compatible line-breaks</h2>

---
Line-break style can be either \*nix (`\n`) or Windows (`\r\n`).

<h2 id="unary" title="Unary operators">Unary operators allowed in `for` loops</h2>

---
Unary operators `++` and `--` are allowed as the final statement in a `for` loop.

e.g. this is valid:

```js
for (let count = 0; i < 5; i++) {
  // do something with `i`
}
```

<h2 id="extensions" title="File extensions">File extensions</h2>

---
JSX can be written in both `.js` or `.jsx` files

<h2 id="imports" title="Global import/require">Global import/require is allowed</h2>

----
Since we're targeting a universal code base in dev and production, there may be code you need to import conditionally. Therefore, `require` and `import` can be used globally.

This is valid:

```js
let someModule;
if (SERVER) {
  // This will only ever get loaded on the server, and since the code path will
  // be eliminated by Webpack on the browser, won't wind up in the browser bundle
  someModule = require('someModule').default;
}
// `someModule` will be `undefined` in the browser, but a usable module on the server
```

You can also use `import` outside of top-level code, which will return a promise with the resolved module. This is useful for code-splitting:

```js
async function getModule() {
  try {
    // get the code asynchronously - works in the browser and on the server!
    const mod = await import('./someModule'); // <-- must be defined statically
    // `mod` is now available for use
  } catch (e) {
    // handle not finding the module
  }
}
```

<h2 id="modules">Module resolution</h2>

---
Module resolution first looks in our project root folder, and _then_ `node_modules`, so we can write:

```js
import Button from 'src/button.js';
```

... wherever we are in the codebase, instead of requiring paths relative to the caller.

<h2 id="self_closing">React self-closing tags</h2>

---
React self-enclosing tags should be on the same line as the final prop, e.g.:

```js
<Component
  someAttribute={true} />
```
