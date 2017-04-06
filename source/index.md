---
title: ReactQL
sidebar_title: Welcome
description: Modern starter kit for React, Apollo, Redux, React Router 4, Webpack 2
---

<h2 id="what">What is ReactQL?</h2>

ReactQL is a starter kit for React + GraphQL.

It combines best-of-breed choices for UI, GraphQL data fetching, CSS and code bundling, handles server-side rendering (SSR) properly, and is fast and extensible.

If you want to skip the tedious set-up code in your next front-end project, try ReactQL.

<h2 id="video">Video: Intro to ReactQL</h2>

<iframe width="560" height="315" src="https://www.youtube.com/embed/hFm4PBQghgA" frameborder="0" allowfullscreen style="max-width: 100%"></iframe>

<h2 id="stack">Features</h2>

- [React](https://facebook.github.io/react/) for UI
- [Apollo Client (React)](http://dev.apollodata.com/react/) for GraphQL
- [React Router 4](https://github.com/ReactTraining/react-router/tree/v4) for declarative browser + server routes
- [Redux](http://redux.js.org/) for flux/store state management
- [Webpack 2](https://webpack.js.org/), with [tree shaking](https://webpack.js.org/guides/tree-shaking/)
- [PostCSS](http://postcss.org/) with [next-gen CSS](http://cssnext.io/) and inline  [@imports](https://github.com/postcss/postcss-import)
- [SASS](http://sass-lang.com) and [LESS](http://lesscss.org/) support (also parsed through PostCSS)
- Full route-aware server-side rendering (SSR) of initial HTML
- Universal building - both browser + Node.js server
- Dev + [React-compatible hot code reloading](http://gaearon.github.io/react-hot-loader/); zero refresh, real-time updates
- Built-in [Koa 2](http://koajs.com/) web server, with async/await routing
- HTTP header hardening with [Helmet for Koa](https://github.com/venables/koa-helmet)
- Declarative/dynamic `<head>` section, using [react-helmet](https://github.com/nfl/react-helmet)
- Easily extendable [webpack-config](https://fitbit.github.io/webpack-config/) files
- Separate local + vendor bundles, for better browser caching/faster builds
- Dynamic polyfills, courtesy of [babel-preset-env](https://github.com/babel/babel-preset-env)
- Aggressive code minification with [Uglify](https://webpack.github.io/docs/list-of-plugins.html#uglifyjsplugin)
- [GIF/JPEG/PNG/SVG crunching](https://github.com/tcoopman/image-webpack-loader) for images
- [Gzip compression](https://webpack.js.org/plugins/compression-webpack-plugin/) and serving of static assets as pre-compressed `.gz` files
- [ESLint](http://eslint.org/)ing based on a tweaked [Airbnb style guide](https://github.com/airbnb/javascript)
- Tons of commentary/documentation to fill you in on what's happening under the hood
