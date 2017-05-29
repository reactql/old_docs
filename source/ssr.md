---
title: Server side rendering (SSR)
description: Initial HTML on the server; continuation in the browser
---

<h2 id="why">Why use server side rendering?</h2>

---
Server-side rendering (SSR, for short) gives your users a full HTML render of the first route they navigate to on your site.

The benefits are:

* A faster experience for your users- both perceived, and actual. Instead of waiting for your code bundles to download and get parsed by the browser's Javascript engine, the browser can render markup and styles instantly. Code is parsed whilst your user is already reading your content, speeding up the user's perception of your app's responsiveness.

* A faster experience for mobile or low-powered devices.

* Better SEO.  Whilst Google et al are getting smarter at interpreting Javascript, nothing beats serving 'raw' markup.

* A seamless experience between the server and browser. Once your initial HTML has been rendered, the client picks up where the server finishes - subsequent hyperlinks loads instantly, and the browser's own Javascript engine takes over to respond to data requests without further round-trips to the server.

<h2 id="pre_rendering">SSR vs. pre-rendering</h2>

---
Pre-rendering is a technique for interpreting client-only apps ahead of time, and serving the 'cached' data to a user. Libraries and services such as [prerender.io](https://prerender.io/) make it possible to simulate many of the benefits of server-side rendering, without writing code that targets a server platform.

Whilst pre-rendering is generally a step up from serving 'headless' client bundles, 'real' SSR provides the ultimate experience for your users.

Here are the key differences:

1. **Data is often 'stale'**. With pre-rendering, you're serving a cached version of the page, which could be minutes or hours out-of-date.  By contrast, ReactQL's SSR allows you to serve the 'real' request on demand, and offer content specific to that request.

2. **Initial HTML is often generic**. Pre-rendering works by spawning a headless Javascript browser, loading the route, running Javascript on the page, and capturing the resulting DOM.  Since this happens en masse and not per visitor, you often forego the opportunity to personalise the HTML with data specific to the user.

3. **[Flash of unstyled content](https://en.wikipedia.org/wiki/Flash_of_unstyled_content) or 'flicker' effects are commonplace**. Since you're serving generic data and there's no server to pre-process data loading requests, DOM changes will often be made after the initial HTML is served.  This can lead to 'flicker' effects or content that suddenly gets style information applied, changing its position or look on the page.  By contrast, ReactQL sends down full, optimised CSS style information with the initial HTML, so content looks good immediately - and stays that way.

4. **No heavy lifting by the server**. A web server can strengthen the user experience by making asynchronous data requests to your GraphQL server or database, caching relevant information, or generally providing functionality that's not available on the client-side-- which can all add to the initial HTML sent down.  By contrast, pre-rendering doesn't have a 'web server'-- it blindly dumps back static HTML and leaves the rest to the client.

<h2 id="how">How ReactQL implements SSR</h2>

---
ReactQL includes a [Koa web server](http://koajs.com/). In [production mode](bundling.html#production), the entry point Webpack will use to generate your web server code is [`kit/entry/server_prod.js`](https://github.com/reactql/kit/blob/master/kit/entry/server_prod.js). In development, it's [`kit/entry/server_dev.js`](https://github.com/reactql/kit/blob/master/kit/entry/server_dev.js)

In both modes, navigating to a server route returns a full HTML dump to the client.

An example response-- notes below:

![SSR, with features](images/ssr/features.png)

This demonstrates the following features:

<h3 id="head" title="Head rewinding">1. `<head>` re-winding</h3>

---
Thanks to [React Helmet](https://github.com/nfl/react-helmet), you can declare `<head>` content anywhere inside your React chain, and it'll wind up in the resulting HTML.

Use it like this:

```js
// React Helmet
import Helmet from 'react-helmet';
//
// Define <Helmet> (i.e. <head>) settings anywhere
const Component = (
  <div>
    <Helmet
      title="ReactQL application"
      meta={[{
        name: 'description',
        content: 'ReactQL starter kit app',
      }]} />
  </div>
);
```

Helmet makes it easy to add request-specific, dynamic titles and meta data.

<h3 id="stylesheets" title="Pre-compiled CSS">2. Pre-compiled CSS stylesheets</h3>

---
Stylesheets used anywhere in your app are optimised, minified and concatenated into a single file in production, which winds up in `dist/public/assets/css/style.css`.

> Learn more about [ReactQL stylesheets here](css.html)

Files ending in `.scss` or `.sass` as first processed by [node-sass](https://github.com/sass/node-sass). Files ending in `.css` skip the SASS compilation step. Both types are then processed by [PostCSS](http://postcss.org/), using [CSSNext](http://cssnext.io/) rules.

Stylesheet code is heavily optimised, common CSS rules are combined, and browser prefixes are added automatically.

Although it's generally a good idea to avoid loading styles that aren't relevant to the current route, the aggressive optimisation and automatic gzipping typically results in a very small `style.css` file. In the original starter kit, `style.css.gz` is just 317 bytes.

Given the small file sizes involved, it's generally okay to keep the css file as a blocking `<link>` in the resulting HTML. The effort of splitting stylesheets generally yields a low ROI, and can give way to a [flash of unstyled content](https://en.wikipedia.org/wiki/Flash_of_unstyled_content).

<h3 id="html" title="React to HTML">3. React to HTML</h3>

---
React components that are active in the current route are rendered to HTML, and sent back to the browser inside the `#main` div, like so:

```html
<body>
  <div id="main">
    <!-- React code will be converted to HTML and placed here -->
  </div>
</body>
```

ReactQL uses ReactDOMServer's [renderToString()](https://facebook.github.io/react/docs/react-dom-server.html#rendertostring) method, allowing the browser to load the resulting HTML into the local bundle and continue serving further React changes on the client-side.

> Before HTML is sent back to the browser, GraphQL queries are executed on the server asynchronously, and 'awaited'. See [React components with GraphQL data](graphql.html) for a how-to on writing components that require GraphQL data.

<h3 id="stores" title="Store dehydration">4. Store dehydration (Redux)</h3>

---
If the server has already requested data from a third-party source like a GraphQL server, it makes little sense to repeat that same request on the client-side.

Instead, ReactQL automatically sends down the 'state' of the application along with the initial HTML render.

The browser can then use this data to 'rehydrate' the state of the application, and continue from the point the server finished - without surplus API calls or unnecessary round-trips to a third-party resource.

The built-in [Apollo Client (for React)](http://dev.apollodata.com/react/) uses [Redux](http://redux.js.org/) under the hood, for managing 'store' state and implementing its data loading and caching strategy.

To make it easier to manage your own application state, ReactQL includes custom instantiation code for managing Redux instead of relying on Apollo's implicit store state. This makes it trivial to add [custom reducers](http://redux.js.org/docs/basics/Reducers.html), and share store state from the server to the client.

> ReactQL includes built-in support for [Redux Devtools](https://github.com/gaearon/redux-devtools) in the browser

<h3 id="bundling">5. Code bundling</h3>

---
Two client-side bundles are produced by the server config, and loaded by your browser:

1. `vendor.js`. Third-party code from NPM modules that are used in the starter kit, or imported into your own code.

2. `bundle.js`. Your own app code.

Vendor code is produced automatically from modules that appear in the `node_modules` folder. Any other code winds up in `bundle.js`.

> If you use code-splitting, Webpack will produce additional files such as `bundle.0.js`, `bundle.1.js`, etc. These are loaded asynchronously as needed - you don't need to explicitly define a loading mechanism, Webpack will do this for you.
