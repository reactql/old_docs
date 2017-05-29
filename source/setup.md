---
title: Setup & Installation
sidebar_title: Installation
description: How to deploy ReactQL for your next project
---

<h2 id="video" title="Video: Installing ReactQL">Video: Installing ReactQL (Windows, Mac, Linux)</h2>

<iframe width="560" height="315" src="https://www.youtube.com/embed/gOMHRBonsUQ" frameborder="0" allowfullscreen style="max-width: 100%"></iframe>

<h2 id="what">System requirements</h2>

ReactQL should work with most flavours of OS X, Linux and Windows.

You'll need [Node.js](https://nodejs.org) (7.6+ recommended).

[Yarn](https://yarnpkg.com/) is optional, but recommended. It'll speed up building third-party modules for your new starter kit. If the `yarn` command is not found on your system, module bundling will fall-back to `npm` automatically.

<h2 id="installation">Installation</h2>

---
Open Terminal (Mac) or Command Prompt (Windows) and run:

```bash
npm i -g reactql
```

This will install the `reactql` CLI tool on your local machine.

<h2 id="new_project">Starting a new project</h2>

---
Run:

```bash
reactql new
```

... to run through the interactive project wizard.

ReactQL will download the [starter kit source](https://github.com/reactql/kit) from Github, extract the archive to your chosen folder, install the required NPM modules, and set everything up. When finished, usage instructions will be dumped to your console.

<h2 id="es6_or_typescript">Typescript support</h2>

---
You can choose from ES6 Javascript or Typescript flavours of the starter kit.

See the [Typescript page](typescript.html) for key differences.

<h2 id="development">Running in development</h2>

---
In development, ReactQL will:

- Run a 'hot reloading' Webpack dev server at [http://localhost:8080](http://localhost:8080)
- Run a development web server for [server-side rendering](ssr.html) at [http://localhost:8081](http://localhost:8081)
- Build both browser-side and server-side development bundles
- Enable sourcemaps for your CSS and Javascript code
- Activate hot code reloading. Any changes to your code will update automatically in your Webpack dev server; to get a new server-side first page render from your web server, just hit refresh in the browser.

To activate development mode, `cd` to your starter kit folder, and run:

```bash
npm start
```

<h2 id="production">Bundling for production</h2>

---
In production, ReactQL will:

- Build a server bundle, which includes a built-in Koa web server
- Activate server-side rendering (SSR) for your React components / GraphQL data fetching
- Create a minified browser bundle
- Crunch your CSS, images and other web assets

To run it, you first need to build the server and client bundles with:

```bash
npm run build
```

Then, simply run:

```bash
npm run server
```

... which (by default) spawns a server at [http://localhost:4000](http://localhost:4000)

> Tip: `npm run build-run` has same effect as running the above two commands separately.

<h2 id="browser">Browser-only bundling (i.e. no web server)</h2>

---
If you only wish to target a browser and you don't care about server-side rendering or running a web server, you can build assets with:

`npm run build-static`

In addition to the regular Javascript, CSS and asset pipeline, the above command will also generate a static `index.html` file that will automatically load stylesheets and Javascript in the browser.

You can then upload the contents of `dist/public` to any static file host (S3, Netlify, Github pages, etc) or FTP to your root web directory on any host, and you'll have a complete application, minus the web server.

> Note: Your `index.html` file will contain only the minimum needed to bootstrap your application -- your JS code will do the rest. You can customise the HTML that is sent back by editing `kit/views/browser.html`. Note that CSS and JS will be injected automatically by Webpack into the `<head>` and footer sections of the page.

To run a local static web server (for testing your bundle), run:

`npm run static`

Or, you can combine the above two commands with:

`npm run build-static-run`

<h2 id="upgrading">Upgrading</h2>

---
Every time you run `reactql`, the tool will check the NPM repository to see if a new version is available. If it is, a message will be displayed on your console.

To upgrade the ReactQL cli tool, simply re-run the original installation command:

```bash
npm i -g reactql
```

This will grab the latest version of the `reactql` tool, and overwrite the old one.

Note: The [starter kit source code](https://github.com/reactql/kit) (and [Typescript version](https://github.com/reactql/kit)) is managed in a separate repo. The version of the kit that's installed with `react new` is hard-coded and versioned along with the CLI. To get the latest kit, bump your CLI version using `npm i -g reactql`. 

> Upgrading: There's no way to upgrade an _active_ project to the latest version, since doing so might require changes to code you've written that modifies the original starter kit. The best way to upgrade is to create a new project, copy over your custom files, and add any of your code changes manually (particularly code you may have extended inside the `kit` folder)
