---
title: FAQ
description: Frequently Asked Questions
---


<h2 id="why_use">Why should I use ReactQL?</h2>

---
This starter kit is the product of over 2 years working with React, and GraphQL since its initial public release.

IMO, this represents the best available tooling for web apps in 2017. I use this stuff every day in production, so you can be confident it'll be maintained and updated regularly.

Use ReactQL if you want to skip the set-up, and get your next project up in record time.

<h2 id="production_ready">Is this production ready?</h2>

---
Some of the third-party packages in my stack may be in alpha or beta stages, so it's up to you to evaluate if they're a good fit for your application.

I personally use this starter kit for every new React project I undertake, many of which are running successfully under heavy production load -- so I'm quite comfortable with the tech choices.

<h2 id="projects" title="What projects...">What kind of projects is ReactQL a good starter for?</h2>

---
Anything 'front end' that's backed by a GraphQL server. I typically break my projects down into a few pieces:

- **Front end**, with a built-in web server that serves public traffic. This usually sits behind an Nginx proxy/load balancer and compiles and dumps back the UI. **This is basically ReactQL**.

- **GraphQL API**. This could be Node.js, Python, Go, etc. But it's usually separate to the front-end code base, and is something the front-end talks to directly via the Apollo GraphQL client. _I generally don't use ReactQL for this_ unless there's a good reason to build a monolithic app (e.g. speed, the back-end not doing a whole lot, etc.)

- **Microservices**.  Smaller pieces of the stack that do one thing, and do it well. The API server is responsible for talking to this stuff.  Again, ReactQL doesn't really get a look in.

I'd generally recommend a similar layout to keep your code light and maintainable. With that said, if you want Node.js to handle both your front and back-office concerns, there's no reason you couldn't write that stuff here too, and keep it scoped to the server. I just prefer to keep it separate.

<h2 id="windows">Will this work on Windows?</h2>

---
Hopefully. I haven't tested it. But there's no OS X/Linux specific commands that are used to build stuff, so it _should_ work.

<h2 id="missing">What's missing?</h2>

---
There's currently no test library, so feel free to add your own.

<h2 id="where">Where do I start coding?</h2>

---
`src/app.js` is the main React component, that both the browser and the serve will look to. Overwrite it with your own code.

If you need to edit the build defaults, you can start digging into the `kit` dir which contains the 'under the hood' build stuff.  But that generally comes later.

<h2 id="tl_dr" title="TL;DR">**TL;DR:** I haven't got time to read the set-up guide. How can I get this running _now_?</h2>

---
- Step 1: Start a new project with `git clone --depth 1 https://github.com/leebenson/reactql <project_folder>`
- Step 2: `cd` into the folder, then install packages with `npm i`
- Step 3: Run `npm start`

This will spawn a dev server on [http://localhost:8080](http://localhost:8080)

To build in production, do steps 1-2 above then run `npm run build-run`
