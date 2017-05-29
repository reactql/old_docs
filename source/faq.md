---
title: FAQ
description: Frequently Asked Questions
---

<h2 id="tl_dr" title="TL;DR">**TL;DR:** I'm short on time. How do I run this now?</h2>

---
- Step 1: Install ReactQL with `npm i -g reactql`
- Step 2: Create a new project with `reactql new`, and run through the wizard
- Step 3: `cd` into your new project, and run `npm start`

This will spawn a 'hot reloading' client dev server on [http://localhost:8080](http://localhost:8080), and a full development web server with [server-side rendering](ssr.html) [http://localhost:8081](http://localhost:8081)

To build and run in production, replace `npm start` with `npm run build-run`

<h2 id="why_use">Why should I use ReactQL?</h2>

---
This starter kit is the product of over 2 years working with React, and GraphQL since its initial public release.

IMO, this represents the best available tooling for web apps in 2017. I use this stuff every day in production, so you can be confident it'll be maintained and updated regularly.

Use ReactQL if you want to skip the set-up, and get your next project up in record time.

<h2 id="where">Where do I start coding?</h2>

---
`src/app.js` is the main React component, that both the browser and the serve will look to. Overwrite it with your own code.

If you need to edit the build defaults, you can start digging into the `kit` dir which contains the 'under the hood' build stuff.  But that generally comes later.


<h2 id="production_ready">Is this production ready?</h2>

---
Some of the third-party packages in my stack may be in beta or pre-beta stages, so it's up to you to evaluate if they're a good fit for your application.

I personally use this starter kit as a basis for several heavy product loads-- so I'm quite comfortable with the tech choices.

<h2 id="projects" title="What projects...">What kind of projects is ReactQL a good starter for?</h2>

---
Anything 'front end' that's backed by a GraphQL server. I typically break my projects down into a few pieces:

- **Front end**, with a built-in web server that serves public traffic. This usually sits behind an Nginx proxy/load balancer and compiles and dumps back the UI. **This is basically ReactQL**.

- **GraphQL API**. This could be Node.js, Python, Go, etc. But it's usually separate to the front-end code base, and is something the front-end talks to directly via the Apollo GraphQL client. _I generally don't use ReactQL for this_ unless there's a good reason to build a monolithic app (e.g. speed, the back-end not doing a whole lot, etc.)

- **Microservices**.  Smaller pieces of the stack that do one thing, and do it well. The API server is responsible for talking to this stuff.  Again, ReactQL doesn't really get a look in.

I'd generally recommend a similar layout to keep your code light and maintainable. With that said, if you want Node.js to handle both your front and back-office concerns, there's no reason you couldn't add a GraphQL server to `kit/entry/server.js` and handle your API requests from the same host.

<h2 id="windows">Is this Windows compatible?</h2>

---
Yes. The kit has been tested on Windows 10, but will probably work on other versions, too. Environment variables and paths are written to be Windows-compatible.

> Note: Node.js is generally less performant on Windows vs. Mac or Linux distros, so expect build times to be up to 2-3x slower. This shouldn't generally affect your development environment (other than start-up times).

<h2 id="missing">What's missing?</h2>

---
There's currently no test library, so feel free to add your own.

<h2 id="integration">Will ReactQL play nicely with themes or other frameworks?</h2>

---
ReactQL provides UI, flux/app state, routing, code bundling and SASS/PostCSS stylesheets out the box. It should play nicely with any framework that isn't opinionated about any of those things.

If you're using a theme that's built on React, many times it will include its own competing build flow for the site -- e.g. [Gulp](http://gulpjs.com/) for build tasks, [LESS](http://lesscss.org/) for stylesheets, [Browsersync](https://www.browsersync.io/) for hot code reloading, etc. The work involved in translating that flow to ReactQL can vary. It depends how tightly coupled the components are to the flow the author provides.

Try to opt for components that are 'self-contained', that don't rely on specific build tools to compile. I'd encourage you to use components that you are in control of that don't require lots of third-party tooling, where possible. Sometimes, it makes sense to use HTML themes and manually translate to React instead.

<h2 id="backend">I'm using [insert database/back-end X here]. Will ReactQL work?</h2>

---
ReactQL is designed to consume [GraphQL APIs](http://graphql.org/), and includes its own built-in web server to serve HTML on the front-end. It's completely agnostic about your back-end, and will connect to any API that can speak GraphQL.

If you're using a database such as MySQL or PostgreSQL, and you'd typically use REST to interact with a legacy system, consider building a GraphQL server that's designed to handle more dynamic requests instead.

<h2 id="graphql_server">How do I build a GraphQL server?</h2>

---
GraphQL is a layer 'above' an existing database. It's agnostic about your back-end technology or stack, and simply defines the shape of queries it will accept. It's up to you to define how data is retrieved. There's lots of community tooling to handle translating queries to SQL, handling data loading, caching, etc.

Check out [Awesome GraphQL](https://github.com/chentsulin/awesome-graphql) for a list of current libraries and resources for your back-end platform.

FWIW, I generally use [Apollo Server](http://dev.apollodata.com/tools/) (Node.js) and [Graphene](http://graphene-python.org/) (Python) in my own projects for building GraphQL servers.

> ReactQL is intended for apps that *consume* GraphQL, rather than supply it. Creating a GraphQL server is a back-end concern that's outside of the scope of this starter kit.
