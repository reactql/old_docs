---
title: Server/browser awareness
description: How code knows whether it's running on the server, or in the browser
---

<h2 id="overview">Overview</h2>

Sometimes you'll have code that needs to run in a browser, but get skipped on the server -- or vice versa.

A typical example is jQuery, or any code that manipulates the DOM. The server has no concept of a DOM, and will crash the request if it encounters an attempt to access `window` or `document`.

ReactQL allows you to determine which environment you're running in, so you can target your code accordingly.

<h2 id="constant">The `SERVER` constant</h2>

---
ReactQL provides you with a `SERVER` constant that's available throughout your code.

If `SERVER === true`, you're on the server. If `false`, you're in the browser.

You can write simple `if` statements like this:

```js
class SomeComponent extends React.PureComponent {
  async componentDidMount() {
    // Check that we're only running in the browser
    if (!SERVER) {
      try {
        // Load a module - this will be skipped on the server
        const someModule = await import('someDOMModule');
        // Assume we are manipulating some local markup...
        this.refToTag = someModule.doSomething();
      } catch (e) {
        console.log('Module did not load!');
      }
    }
  }
}
```

<h2 id="env">Development or production</h2>

---
To check whether you're running in dev/production, use:

```js
// Will return a string of either 'development' or 'production'
process.env.NODE_ENV
```
