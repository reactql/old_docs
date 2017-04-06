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
