---
title: Setup & Installation
sidebar_title: Installation
description: How to deploy ReactQL for your next project
---

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

ReactQL will create a new starter kit in the folder of your choosing, install the required NPM modules, and set everything up. When finished, usage instructions will be dumped to your console.


<h2 id="development">Running in development</h2>

---
In development, ReactQL will:

- Run a development server at [http://localhost:8080](http://localhost:8080)
- Build a browser-side bundle only
- Enable sourcemaps for your CSS and Javascript code
- Activate hot code reloading; any changes to your code will update automatically in the browser

To activate development mode, `cd` to your starter kit folder, and run:

```bash
npm start
```

Changes to your React components/styles will update in the browser in real-time, with no refreshes. Changes to other code may require a refresh.

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

<h2 id="upgrading">Upgrading</h2>

---
Every time you run `reactql`, the tool will check the NPM repository to see if a new version is available. If it is, a message will be displayed on your console.

To upgrade the ReactQL cli tool, simply re-run the original installation command:

```bash
npm i -g reactql
```

This will grab the latest version of the `reactql` tool, and overwrite the old one.

Since the starter kit is self-contained, there are no additional dependencies to manage -- the next time you run `reactql`, you'll be deploying the latest version.

> Note: There's no way to upgrade an active project to the latest version, since doing so might require changes to code you've written that modifies the original starter kit. The best way to upgrade is to create a new project, copy over your custom files, and add any of your code changes manually (particularly code you may have extended inside the `kit` folder)
