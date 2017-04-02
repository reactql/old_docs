---
title: Module resolution
description: Where ReactQL looks for your imported code
---

<h2 id="modules">How ReactQL finds your code</h2>

---
When you `import` or `require` modules, Webpack (and ESLint) will first look in your root project folder before checking `node_modules`.

This allows you to short-hand writing long, relative path names.

e.g. instead of writing:

```js
import x from '../../../components/some/file.js';
```

... you can instead simply write:

```js
import x from 'src/components/some/file.js';
```

The same is true of any filetype that Webpack recognises- .jpg, .css, .sass, .json, etc:

```js
// If, say, you wish to put all CSS code in a `styles` folder in the project root
import style from 'styles/some/file.css';
```
