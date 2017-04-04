---
title: Code bundling
description: How ReactQL bundles, minifies and optimises your code
---

<h2 id="overview">Overview</h2>

---
ReactQL comes with an [extensible](https://github.com/leebenson/reactql/tree/master/kit/webpack) Webpack 2 configuration that bundles and optimises your code, for both development and production.

In this doc, you'll learn how it works with your Javascript, CSS and static assets.

<h2 id="basics">Basic concepts: Webpack 2</h2>

---
[Webpack](https://webpack.js.org/) is a code bundling tool that turns your raw source code into a single 'bundle' file that can run in the browser.

Out-the-box, ReactQL uses syntax that a regular browser cannot understand without being [transpiled](https://en.wikipedia.org/wiki/Source-to-source_compiler) down to plain Javascript.

This can include variants of JS that don't yet ship with common Javascript interpreters that are bundled with browsers (such as native `async/await` code, or module `import` statements), as well as features that are unique to Webpack, such as the ability to use local `.css` files and translate stylesheet classnames into [locally hashed versions](css.html#local).

This is where Webpack comes in. It will translate many files of source code into a single bundle that will be loaded along with your HTML code in the browser. That's why it's valid to write code like this:

```js
// importing a .css file and treating it like JS would be impossible in a browser...
import style from './someFile.css'
console.log(style.someClassName); // <-- woah!
```

... and actually see it working in the browser.

Furthermore, it includes several pre-processors that will crunch and optimise your code for production, as well as provides functionality for spawning a development server with sourcemaps, hot code reloading and more.

<h2 id="server">Server bundling</h2>

---
Webpack isn't just for the browser. ReactQL will also generate an optimised _server_ bundle that's ready to use in production.

This allows you to write your code to target a 'universal' platform, that will run in exactly the same way in the browser as well as via a Node.js server.

Just like in the browser, that makes code like this perfectly valid on the server:

```js
// importing a .css file and treating it like JS would be impossible in a browser...
import json from 'config/someConfig.json'
console.log(json.apiKey); // <-- what the...?!
```

The server bundle contains a built-in [Koa web server](http://koajs.com/) that will listen to HTTP requests, render the relevant React code into HTML, and serve it back to your user.

This allows you to offer fast *time-to-first-byte* to visitors, without waiting for the full browser bundle to download and process first. Instead, users will be greeted with initial HTML and all styles intact, giving a more 'progressive' experience when the bundle eventually loads.

<h2 id="config">Config files</h2>

---
See the [Webpack project layout](layout.html#kit-webpack) for details which Webpack files are relevant for bundling in development and production, in both a browser and server context. You can edit the config to include new Webpack loaders or change the configuration as needed.

<h2 id="where">Where Webpack looks for code</h2>

---
See [module resolution](modules.html).

> *TL;DR* Your project root first, then `node_modules`

<h2 id="styles">CSS / stylesheets</h2>

---
You can import `.css` or `.scss`/`.sass` ([SASS](http://sass-lang.com/)) stylesheets by simply `import/require`ing from any Javascript file:

```js
// `styles` will be an object that contains a list of exported 'hashed' classnames
import styles from 'src/css/whatever.css';
```

Classnames will be [hashed](https://github.com/webpack-contrib/css-loader#css-scope) to prevent bleeding out to the global scope. This can be overridden by nesting classes in a `:global` block

> See [Stylesheets docs](css.html) for examples.

`@import` statements within CSS will be concatenated in the final bundle:

```css
@import url(other/css/file.css);
```

If you're using SASS, imports will be processed via SASS first; settings, SASS variables and functions will be available to any CSS file that uses `@import` to pull from a parent SASS file.

<h2 id="images">Images</h2>

---
Import `.gif`, `.jp(e?)g`, `.png` or `.svg` images using standard `import` or `require` syntax:

```js
// `backgroundImage` will be a string of the filename, relative to `[root]/dist/public`
import backgroundImage from './bg.png';
```

You can then use the filename in your code, as usual:

```js
const Component = (
  <div>
    { /* `image` = string containing path to file name */ }
    <SomeOtherComponent image={backgroundImage} />
  </div>
);
```

You can also include images in your CSS files, which will be processed by Webpack, too:

```css
.logo {
  /* this will be processed exactly the same way as if imported in JS */
  background-image: url('./path/to/file.png');
}
```

### Image compression

In production, images are processed through [image-webpack-loader](https://github.com/tcoopman/image-webpack-loader), which minifies JPEG, GIF, PNG and SVG files via [imagemin](https://github.com/imagemin/imagemin) and copied to `[root]/dist/public/assets/img`. In development, original images files are served from memory (and compression is skipped), and no files are copied to the physical filesystem.

> By default, the JPG compression is set at 65% the quality of the original, and PNG has a target range of 65-90%. If images appear fuzzy, you can override this by editing [kit/webpack/base.js](https://github.com/leebenson/reactql/blob/master/kit/webpack/base.js)

<h2 id="fonts">Fonts</h2>

---
Font files (`.woff`, `.woff2`, `.ttf`, `.eot`) can be included in any CSS in the usual way:

```css
@font-face {
  font-family: 'SomeCustomFont';
  font-style: normal;
  font-weight: 400; url(static/fonts/customFont.woff2) format('woff2');
}
```

```js
// `fontFile` = 'assets/fonts/whatever.ttf'
import fontFile from 'static/whatever.ttf';
```

> Files imported in a `.js` file return the string to the filename, relative to `[root]/dist/public/`:

No transformation is performed on font files; original files will simply be copied to `dist/public/assets/fonts` in production.

<h2 id="json">JSON</h2>

---
JSON stored in a `.json` file will be processed through [json-loader](https://github.com/webpack-contrib/json-loader), and return a parsed Javascript object of the JSON data.

Example:

**data.json**
```json
{
  "name": "ReactQL"
}
```

**app.js**
```js
// `data` will be { name: "ReactQL" }
import data from './data.json';
console.log(data.name);
```

> JSON data is always bundled with the main code, and not stored in separate files - both in development and in production.

<h2 id="development">Development mode</h2>

---
To start a development server, run:

```bash
npm start
```

This is intended for on your local development machine. As you make changes, they will be reflected in the browser in real-time.

This is what Webpack will do when spawning a dev server:

1. It will load your configuration at [kit/webpack/browser_dev.js](https://github.com/leebenson/reactql/blob/master/kit/webpack/browser_dev.js)

2. Webpack will start processing code in the [kit/src/browser.js](https://github.com/leebenson/reactql/blob/master/kit/entry/browser.js) entry file, start a development server at [http://localhost:8080](http://localhost:8080) and compile your source code into 'in memory' code bundles, at [http://localhost:8080/vendor.js](http://localhost:8080/vendor.js) (for third-party NPM code) and [http://localhost:8080/bundle.js](http://localhost:8080/bundle.js) (your own code). No physical files are stored on the filesystem.

3. All routes will be served by [static/webpack.html](https://github.com/leebenson/reactql/blob/master/static/webpack.html). In development mode, there is no server-side rendering. Everything is loaded in the browser. That means GraphQL data fetching or anything 'async' will cause React to re-render with the correct props once it's retrieved them.

4. Stylesheets will be included as inline code inside `bundle.js`. You might notice a [flash of unstyled content](https://en.wikipedia.org/wiki/Flash_of_unstyled_content) as `/bundle.js` is first loaded before snapping your styles into place.

5. The `./static` folder becomes your public route. Anything you might place in here - images, third-party scripts, etc - will be served 'unprocessed', in their original form.

6.  A small snippet of code will be added to your in-memory `/vendor.js` entrypoint for handling hot code reload via websockets. Whenever you make changes to your code, the browser is 'injected' with the code difference and reflected in real-time.

Development mode is not intended for production environments. Code bundles will be larger than production versions due to unminified sourcemaps and original files, and there is no server-side rendering in this mode.

<h2 id="production">Production mode</h2>

---
To build assets fit for deployment, run:

```bash
npm run build
```

Webpack will follow these steps:

1. It will first build your *browser* bundle by loading the Webpack config file at [kit/webpack/browser_prod.js](https://github.com/leebenson/reactql/blob/master/kit/webpack/browser_prod.js), which will use [kit/src/browser.js](https://github.com/leebenson/reactql/blob/master/kit/entry/browser.js) as its entrypoint.

2. Webpack will then build your *server* bundle by loading the Webpack config file at [kit/webpack/server.js](https://github.com/leebenson/reactql/blob/master/kit/webpack/server.js), which will use [kit/src/server.js](https://github.com/leebenson/reactql/blob/master/kit/entry/server.js) as its entrypoint.

3. Assets will appear in a new `[root]/dist` folder. A sub-folder, `./dist/public` is created which becomes the public entry point for the built-in [Koa web server](http://koajs.com/).

3. All `./css` files are extracted out, optimised and concatenated into a separate `dist/public/assets/css/style.css` file.  Styles are processed through the [PostCSS](http://postcss.org/) loader. On the server, hashed versions of any class names used are calculated and included in the server bundle, so invoking those classes later alongside React components will match up with the compiled `style.css` file.

4. Imported images are optimised/crunched through [imagemin](https://github.com/imagemin/imagemin), and wind up in `dist/public/assets/img/`.

5. Imported fonts are copied to `dist/public/assets/fonts`.

> Once assets have been built, you can run `npm run server` to start a live web server

<h2 id="gzip">Gzip compression</h2>

---
Gzip is a standard compression technology supported by all major browsers, that can significantly reduce file sizes and data downloads, and provide users with a faster first load.

Static assets that wind up in the `dist/` folder -- including CSS, images and fonts -- get gzip'd `.gz` versions automatically courtesy of the [Compression Webpack Plugin](https://webpack.js.org/plugins/compression-webpack-plugin/). Alongside the original, a new `[file].gz` will appear in the same folder -- e.g. `styles.css` gets a `styles.css.gz` too.

The ReactQL web server that comes built-in will look for `.gz` file before serving the original.  If it finds one, it'll send that version down the wire instead.

Since your assets are minified one-time in advance, this prevents CPU cycles crunching the data in 'real-time' for every visit.

You don't need to do anything to activate this-- it's enabled autoamtically.
