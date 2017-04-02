---
title: Project layout
description: Where your code lives, and how it's wired up
---

<h2 id="folders">Folders</h2>

---
This starter has been designed to provide you with a few skeletal pieces to get you going, and make it easier to layer in your own code.

Here's the folder layout:

## `.git`

---
Git folder.  By default, this points to the ReactQL starter kit, so you can safely delete this if you want to initialise your own git repo and start contributing to it.

## `config`

---
Project configuration files, such as the folder structure that ReactQL will use to determine where your code lives.

You can put your own settings in `config/project.js` or create new files in this folder to host them.

By default, `config/project.js` contains just the URI that Apollo will use to connect to your back-end GraphQL server. You can extend this with your own configuration options, for example SMTP details or other API endpoints.

This is written in ES6 style with no defaults, so you can import select configuration options from the client side without exposing credentials thanks to automatic tree-shaking/dead code elimination.

## `kit`

---
The bulk of ReactQL is found in `./kit`.  If you need to dive into the Webpack config or add some custom functionality to your web server or browser instantiation, you'd do it here.

For the most part though, you probably won't need to touch this stuff.

### `kit/entry`

---
This is where Webpack looks for entry points for building the browser and the server.

Note: There's no separate entry for 'vendors', since the bundle is built automatically by testing whether packages reside in `node_modules`

### `kit/lib`

---
Custom libraries built for ReactQL.  You'll find custom helpers for Redux and Apollo for creating new stores, and building connections to your GraphQL endpoint via Apollo.

### `kit/webpack`

---
Webpack configuration files, notably:

- `base.js`: Base configuration that all others inherit from
- `eslint.js`: Entry point for the linter, to properly follow local module folders and avoid false positives
- `browser.js`: Base configuration for the browser.  Inherits from base, and is extended by browser_dev and browser_prod
- `browser_dev.js`: Config for the browser spawned at [http://localhost:8080](http://localhost:8080) when running `npm start`
- `browser_prod.js`: Production browser bundling.  Minifies and crunches code and images, gzips assets, removes source maps and debug flags, builds your browser bundles ready for production.  This is automatically served back to the client when you run `npm run build-run`
- `server.js`: Node.js web server bundle.  Even though we're running this through Node, Webpack still gets involved and properly handles inline file and CSS loading, and generates a build that works with your locally installed Node.js. You can build the server with `npm run build-server` or build and run with `npm run build-run`

## `src`

---
This is where all of your own code will live. By default, it contains the `<App>` component in `app.js` which demonstrates a few of the concepts that this starter kit offers you, along with styles defined in `app.css`.

You can overwrite these files directly with your own code and use this as a starter point.

## `static`

---
Put static files here that you want to wind up serving in production.

Webpack will automatically crunch any of your GIF/JPEG/PNG/SVG images it finds in here, as well as generate pre-compressed `.gz` versions of every file so that your Koa web server on [http://localhost:4000](http://localhost:4000) (after running `npm run build-run`) can serve up the compressed versions instead of the full-sized originals.

<h2 id="root">Root files</h2>

---
There are various configuration files that you'll find in the root:

- `.babelrc`:  This exists solely to transpile the ES6 Webpack config into something Node.js can natively run. It has no bearing on the [Babel](http://babeljs.io/) config that's used to transpile your Webpack code, so you can safely this leave alone.

- `.eslint.js`: [ESLint](http://eslint.org/) configuration. If your editor/IDE has ESLint installed, it will use this file to lint your code.  See "[Styleguide](styleguide.html)" to see the syntax rules that ReactQL lints your code with.

- `.eslintignore`: Files the linter can safely ignore.

- `.gitignore`: Files to ignore when checking in your code.  This is built around the ReactQL starter kit, but you will probably want to use it as a base for your own code since it ignores the usual Node stuff, along with `dist` and some of the caching folders used by Webpack.

- `package.json`: NPM packages used in this starter kit.  When you're extending this kit with your own code, you'll probably want to gut out the name, description and repo links and replace with your own. Just keep `dependencies` and `devDependencies` intact for the kit to continue to work.

- `webpack.config.babel.js`: The Webpack entry point.  This will invoke the config found in `kit/webpack` per the `npm run [...]` command that spawned it.
