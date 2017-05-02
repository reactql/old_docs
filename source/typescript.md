---
title: ES6 or Typescript?
description: Choose between ES6 Javascript or Typescript
---

When you install a new project, you'll be asked whether you want to download the [ES6 Javascript](https://github.com/reactql/kit) or [Typescript version](https://github.com/reactql/kit.ts) of the ReactQL starter kit.

Both kits maintain the same core feature set, and can be started and built in the same way.

There are a few differences:

<h2 id="type_checking">Type checking</h2>

---
The Typescript variant includes type annotations, to help enforce structure and prevent common code bugs that occur when passing around unexpected data between functions. You can learn more at [typescriptlang.org](http://www.typescriptlang.org/).

The ES6 version is 'plain', dynamically typed Javascript.

Which version you choose depends on the requirements of your project. If you're a sole developer or want to develop fast prototypes, ES6 is generally fewer keystrokes. If you're a team and want to enforce rigour across your app, type checking might be for you.

<h2 id="build">Build pipeline</h2>

---
There are subtle differences to the Webpack build process.

The Typescript version uses [awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader) alongside the common Webpack build config, which both removes type annotations in the resulting bundle, and throws an error on type mismatches.

The ES6 Javascript version also throws syntax errors, but won't do any kind of type checking. It uses [Babel](http://babeljs.io/) to transpile ES6 to browser compatible code.

Both versions produce 'plain' Javascript for the server and browser; no runtime parser is needed.

<h2 id="build">Bundle sizes</h2>

---
Generally, resulting bundle sizes are relatively similar-- both ES6 and Typescript produced 'plain' Javascript that contains the appropriate polyfills to run properly in the browser, or via the Node.js web server. Bundle size alone is generally not a reason to choose one starter kit flavour over another.

However, the Typescript version currently weighs in at around *11kb less* than the ES6 variant, thanks for slightly more optimised helpers in Typescript vs. Babel and the lack of `regenerator-runtime`.

<h2 id="jsx">React / JSX</h2>

---
Typescript files that use JSX / React syntax, should be suffixed with a `.tsx` extension. That tells the Webpack build pipeline to parse JSX properly.

In the ES6 kit, React can appear in any `.js` file -- no special extension is needed.

<h2 id="source">Kit sources</h2>

---
Direct links to the relevant starter kit source repository:

* *ES6 version:* [https://github.com/reactql/kit](https://github.com/reactql/kit)
* *Typescript version:* [https://github.com/reactql/kit.ts](https://github.com/reactql/kit.ts)

> Note: You can clone the kit directly, or use the [CLI tool](setup.html) to choose which flavour of kit you'd like when starting a new project.
