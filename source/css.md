---
title: CSS stylesheets
description: Next-gen CSS with PostCSS, SASS and LESS
---

<h2 id="files">Writing CSS files</h2>

---
CSS can be written and placed anywhere. A common pattern is to write the `.css` alongside regular `.js` files, so they 'live' in the same place.

You can import and use styles in your code like regular Javascript:

Inside your CSS:

**someCssFile.css**
```css
.someClass {
  font-weight: bold;
  text-align: center;
  color: red;
}
```

**someJsFile.js**
```js
import css from './someCssFile.css';

// .someClass will get transformed into a hashed, 'local' name, to avoid
// collisions with the global namespace
export default props => (
  <div>
    <p className={css.someClass}>Hello world</p>
  </div>
)
```

> You can import `.css` (plain CSS), `.scss` &amp; `.sass` ([SASS](http://sass-lang.com/)) and `.less` ([LESS](http://lesscss.org/)) files. All three flavours export [localised classnames](#local).

<h2 id="cssnext" title="CSSNext">Next-generation CSS parsing (PostCSS)</h2>

---
All stylesheets - plain CSS, LESS or SASS - are all ultimately parsed through [CSSNext](http://cssnext.io/) as a final step, a PostCSS plug-in.

That means you can use things like nested statements out-the-box in plain `.css`:

```css
.anotherClassName {
  h1 {
    text-size: 2em;
  }
  p {
    color: green;
  }
}
```

As well as variable names, and other goodies:

```css
:root {
  --mainColor: red;
}
a {
  color: var(--mainColor);
}
```

> Since SASS and LESS use different syntax, you'll need to write styles that are compatible with the respective parsers before reaching the CSSNext step.

Vendor prefixes will automatically be added, so you can write the bare CSS and let PostCSS worry about polyfilling browsers.

See [CSSNext](http://cssnext.io/) for more details and syntax.

<h2 id="nesting">Nesting</h2>

---
Your plain `.css` files automatically support SASS-style nesting:

```cs
.someClass {
  h1 {
    color: green;
  }
  .someOtherClass {
    color: red;
  }
}
```

This is achieved via [postcss-nested](https://github.com/postcss/postcss-nested) and means that, if you only want to do SASS-style nesting, you can avoid the additional overhead of a SASS parser.

<h2 id="sass">SASS support</h2>

---
ReactQL has [SASS](http://sass-lang.com/) support. Any imported file with a `.scss` or `.sass` extension will be parsed through [node-sass](https://github.com/sass/node-sass) first.

`@import` statements are also parsed through SASS first, so you can use parent settings, SASS functions and variables in the calling `.sass / .scss`, too.

<h2 id="less">LESS support</h2>

---
ReactQL offers [LESS](http://sass-lang.com/) support, too. `.less` files will be parsed through the Webpack [less-loader](https://github.com/webpack-contrib/less-loader).

You can use any feature offered by the [LESS parser](http://lesscss.org/).

<h2 id="local" title="Local modules">Local modules - preventing bleed-out</h2>

---
By default, class styles in your CSS/SASS/LESS files will be exported as _local_ modules. Class names will be hashed to prevent bleeding out to your global namespace, and those same class names are then injected into the React code that is bundled for you. e.g:

![CSS local classnames](images/classnames.png)

The benefit to this is tight coupling of style logic with your components.  You don't have to worry that you've used globally unique class names, because Webpack will transpile your names into garbled, hashed versions to avoid conflicts.

<h2 id="global_styles">Global styles</h2>

---
If you want to override the 'local by default' classnames, you have two options:

**1. The `:global` prefix**

Simply prefix `:global` before any rule and it'll be made available across your entire app:

```css
:global .thisClassWontRename {
  font-color: blue;
}
```

In this case, `.thisClassWontRename` will be the exact name used in the output to your final .css file, so you can use this class name wherever you want the style to apply - anywhere in your code.

`:global` will work across all parsers -- plain CSS, SASS and LESS -- because all CSS is ultimately parsed through the [CSS loader](https://github.com/webpack-contrib/css-loader).

> Note: Avoid using `@import` statements inside of a `:global` block. Imports are intended to be top-level statements, which means they will 'break out' of a surrounding global block and wind up as local modules. Instead, use the method below.

**2. Use `*.global.(css|sass|scss|less)` files**

By suffixing your style files with `.global.css` (plain CSS), `.global.scss` / `.global.sass` (SASS) or `.global.less` (LESS), classnames within those files are preserved and won't be localised.

This is useful for integrating third-party style frameworks where components expect the original classnames to be used:

**app.js**
```js
// Import your style globally. No need to assign it to a variable --
// it'll wind up in our style.[hash].css file just the same
import './styles.global.scss';
```

**styles.global.scss**
```scss
/* example: importing the Foundation Sites SASS library */
@import '~foundation-sites/scss/foundation';
@include foundation-everything;
```

<h2 id="bundling">Bundling styles</h2>

---
In development, your styles will be bundled with full source maps into the resulting Javascript that's generated by Webpack Dev Server. No external CSS file is created or called.

In production, code is extracted out into a final `style.[hash].css` file that is automatically minified and included in the first-page HTML via a `<link>` tag that's sent from the server-side.

Webpack will do the heavy lifting and match up your React names with the CSS localised styles, so it'll work exactly the same way as in dev.

You don't need to do anything for this to happen- ReactQL takes care of it for you.

<h2 id="pipeline">Style pipeline</h2>

---
You can write stylesheets in plain CSS (.css), [SASS](http://sass-lang.com/)) (.scss / .sass) or [LESS](http://lesscss.org/) (.less).

You can also mix and match all three, import styles into your components, and reference classes within those stylesheets as if you're referencing a regular Javascript object.

Classnames will automatically be 'localised' so they're scoped to the component that imported them -- preventing 'bleed out' into the global namespace (examples are given below), unless your file ends `.global.(css|sass|scss|less)`, in which case the original classnames are preserved.

**Every stylesheet goes through the following Webpack compilation pipeline:**

1. If you're using SASS or LESS, the CSS is first run through the relevant Webpack loader (details in the relevant section below) to transform it into plain CSS.

2. Your original CSS code (or the resulting CSS dumped by the SASS/LESS loader) is then passed through PostCSS to add vendor prefixes, enable [CSSNext](http://cssnext.io/) features, SASS-style [nesting](https://github.com/postcss/postcss-nested), and minify your code.

3. The Webpack [CSS loader](https://github.com/webpack-contrib/css-loader) inspects your classnames, and produces a hashed, or 'localised' version of them, to prevent namespace clashes with other styles that may use the same classnames in other components.  You can then import the stylesheet just as you'd import any other Javascript file, and the original classname will be a key that points to the hashed version, making it easy to reference the new classname in your React components.

4. In production, your styles are concatenated, minified and placed in `dist/public/assets/css/style.[hash].css` via the [Extract Text plug-in](https://github.com/webpack-contrib/extract-text-webpack-plugin) .  Even if you use multiple imports, multiple style formats (SASS, LESS), or code that is only imported on code-split components, all resulting CSS code will wind up in this one file.

5. Your stylesheet is automatically loaded on the initial view by your web server. This ensures all styles are available to your components, preventing 'flicker' upon first load.

> In development, your styles are bundled into the resulting Javascript code, so there may be a brief moment when your content appears unstyled before the bundle is loaded. This won't happen in production, since the CSS is loaded in the `<head>` section before any HTML content is parsed by the browser.

<h2 id="server">Server-side support</h2>

---
All stylesheet flavours are fully supported on the server, so you can write your code without fear that stylesheets will break universal/server-side rendering.

In production, your styles will automatically be extracted out to a `dist/public/assets/css/style.[hash].css` and your server bundle will know the relevant 'hashed' classname to include in your `class=` attribute on your components.

This is done for you automatically - no code changes required.
