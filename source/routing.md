---
title: Routing
description: Universal routes, with React Router 4
---

<h2 id="declarative" title="Declarative routes">Declarative routes with React Router 4</h2>

---
ReactQL includes [React Router v4](https://reacttraining.com/react-router/).

Routes are declared _declaratively_ inside the component chain. There's no separate configuration to maintain - you simply tell React what to display when a route matches.

The syntax looks like this:

```jsx
// From React Router
import { Link, Route } from 'react-router-dom';
const Component = (
  <div>
    <ul>
      <li><Link to="/">Home</Link></li>
      <li><Link to="/page/about">About</Link></li>
      <li><Link to="/page/contact">Contact</Link></li>
    </ul>
    <hr />
    <Route exact path="/" component={Home} />
    <Route path="/page/:name" component={Page} />
  </div>
);
```

In the first `<Route>` in the above example, the `<Home>` component will load when the path matches `/` exactly.

In the second, `<Page>` will show when the path matches _at least_ `/path/...`, with a `match.params.name` prop being dynamically passed to `<Page>`.

> See the [React Router](https://reacttraining.com/react-router) docs for full usage details.

<h2 id="params">Match parameters</h2>

---

If you have a route like this:

```js
<Route path="/page/:name" component={Page} />
```

... then the `name` param will be passed to `<Page>` as follows:

```js
// The <Page> component that we've passed to `component=` in our <Route>
const Page = ({ match }) => (
  <h1>Changed route: {match.params.name}</h1>
);
// The 'shape' of the props we can expect to be passed by <Route>
Page.propTypes = {
  match: React.PropTypes.shape({
    params: React.PropTypes.object,
  }).isRequired,
};
```

You can declare multiple parameters that will be made available via `props.match.params`;

<h2 id="ssr">Server-side routes</h2>

---
Routes work on both the server and in the browser. ReactQL will render the initial HTML to reflect the exact route that was requested, giving your visitors fast, relevant content without the typical 'flicker' that can often occur with single-page apps that rely solely on client-side routing.

If you run `npm run build-run` on a new ReactQL starter kit and navigate to [http://localhost:4000/page/about](http://localhost:4000/page/about), you can see an example of server-side rendering at work:

![SSR routing](images/routing/ssr.png)

<h2 id="linking">Linking in the browser</h2>

---
To create hyperlinks between local routes, use the `<Link>` component provided by React Router:

```js
// From React Router
import { Link } from 'react-router-dom';
const Component = (
  <div>
    <Link to="/page/contact">Contact</Link>
  </div>
);
```

In the browser, clicking a link will load it *instantly*. There's no round-trip to the server, meaning your site will function like a [single page app](https://en.wikipedia.org/wiki/Single-page_application) without any browser refreshes, or DOM tear-down per request.

Existing GraphQL, Redux store data and Javascript variables will remain in memory between route transitions.

<h2 id="not_found">Not found (404) routes</h2>

---
There's probably times you'll want to display content when a route _doesn't_ match. Say, if someone navigates to a link that doesn't exist, and you want to tell the user they've hit a wrong turn.

For those times, we've included a `<NotFound>` component that you can use like this:

```js
// `<Switch>` component that will execute the first matching route
import Switch from 'react-router-dom';
// NotFound 404 handler for unknown routes in `kit/lib/routing`
import { NotFound } from 'kit/lib/routing';
// Assuming we have a `<Home>` that will show up when the path is '/',
// <Page> that will show when the route is /page/whatever, and <NotFound>
// at all other times
export default () => (
  <Switch>
    <Route exact path="/" component={Home} />
    <Route path="/page/:name" component={Page} />
    <Route component={NotFound} />
  </Switch>
);
```

You can extend the component by adding your own content like this:

```js
// Create a component that extends `<NotFound>`
const YourOwn404 = () => (
  <NotFound>
    <h1>Hey - no route here by that name, buster!</h1>
  </NotFound>
);
// This will execute the first route that matches (`<YourOwn404>` if not)
export default () => (
  <Switch>
    <Route path="/route/that/exists" component={TryThisFirst} />
    <Route component={YourOwn404} />
  </Switch>
);
```

> On the server, if a component renders `<NotFound>` (or any component that extends it), then `routeContext.status === 404` inside the [`createReactHandler`](https://github.com/reactql/kit/blob/master/kit/entry/server.js#L72) function that renders the React chain. This allows you to add your own 404 strategy (say, redirecting to a common 'not found' page). If you want to do anything special on the server-side, the implementation is left up to you.

<h2 id="redirects">Redirects (301/302) routes</h2>

---
Like the `<NotFound>` component, there's a `<Redirect>` available too to handle client and server side route redirection:

```js
// `<Switch>` component that will execute the first matching route
//
import Switch from 'react-router-dom';
// `<Redirect>` either 'permanently' or temporarily
//
import { Redirect } from 'kit/lib/routing';
// Create a redirect component by extending `<Redirect>`
//
const RedirectElsewhere = () => <Redirect to="/somewhere/else" />;
// Redirect if the route isn't found
//
export default () => (
  <Switch>
    <Route exact path="/" component={Home} />
    <Route path="/page/:name" component={Page} />
    <Route component={RedirectElsewhere} />
  </Switch>
);
```

The ReactQL `<Redirect>` component extends the 'native' component of the same name in [React Router v4](https://reacttraining.com/react-router/web/api/Redirect) and offers the same `from`, `to`, and `push` attributes.

It adds the `permanent` boolean option, too. If `permanent=true`, then `routeContext.status === 301` inside the [`createReactHandler`](https://github.com/reactql/kit/blob/master/kit/entry/server.js#L72) that renders the React chain. If `permanent=false`, then `routeContext.status === 302`.

> TODO Note: Redirect handling on the server is coming soon!
