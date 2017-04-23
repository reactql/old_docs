---
title: React components with GraphQL data
sidebar_title: GraphQL components
description: How to write components that talk to a GraphQL server
---

When running `npm start`, you'll see an example of GraphQL talking to a live server end-point hosted at [graph.cool](https://www.graph.cool/)

Check out [src/app.js](https://github.com/reactql/kit/blob/master/src/app.js) to see this working in action.

The cool thing is, if you run the component on the server via `npm run build-run` and navigate to [http://localhost:4000](http://localhost:4000), you'll see the GraphQL response directly in the source code, too!

That's because ReactQL automatically requests data from GraphQL _before_ rendering the resulting HTML back to the browser.

Furthermore, it will send back the initial Redux state with the HTML, too, ensuring that the browser doesn't re-request data it already has.

Here's a blow-by-blow of how it works:

First, set the GraphQL API endpoint.  By default, I've included a sample to a [graph.cool](https://www.graph.cool/) service I created for this starter kit -- change this to your own GraphQL server in production:

**config/project.js**
```js
export const APOLLO = {
  uri: 'https://api.graph.cool/simple/v1/cinomw2r1018601o42x5z69uc',
};
```

Both the browser and server entry points will create an Apollo client based on the above config.

Then, in your [src/app.js](https://github.com/reactql/kit/blob/master/src/app.js) file, we define the GraphQL query to grab the data we need:

**src/app.js**
```js
// First, create the GraphQL query that we'll use to request data from our
// sample endpoint
const query = gql`
  query {
    allMessages(first:1) {
      text
    }
  }
`;
```

We use Apollo's `gql` template function to specify the data we require.

Next, we'll create the 'plain' component that will ultimately take the data in via props. Note: This doesn't have any 'special' GraphQL related syntax. It's just a plain React component that will take in GraphQL data via plain props:

```js
const Message = ({ data }) => {
  // `data` will initially be blank before GraphQL has returned with data, so test it exists
  const message = data.allMessages && data.allMessages[0].text;
  const isLoading = data.loading ? 'yes' : 'nope';
  return (
    <div>
      <h2>Message from GraphQL server: <em>{message}</em></h2>
      <h2>Currently loading?: {isLoading}</h2>
    </div>
  );
};
```

Now, for use in development, we can add custom `propTypes` to the component so that React knows what to expect.

In [kit/lib/apollo.js](https://github.com/reactql/kit/blob/master/kit/lib/apollo.js), we have a custom `mergeData()` helper that will provide us with the `data.loading` boolean, and allow us to merge in expected data.

In this example, we're expecting an `allMessages` array which will contain an object with `text` property, containing our message received from our GraphQL endpoint.

```js
// Add propTypes for React to expect data from GraphQL
Message.propTypes = {
  data: mergeData({
    allMessages: React.PropTypes.arrayOf(
      React.PropTypes.shape({
        text: React.PropTypes.string.isRequired,
      }).isRequired,
    ),
  }),
};
```

Finally, we create a higher-order component that wraps our plain component, and gives it GraphQL listening powers:

```js
const GraphQLMessage = graphql(query)(Message);
```

Now, we can use `<GraphQLMessage />` inside any other React component, and it'll automatically update with props when data has been retrieved or is updated via Apollo.

## How can I prevent data loading on the server?

Server-side rendering is an awesome feature that will bolster your SEO strategy, and generally improve the user experience by making the full page available on initial render.

However, sometimes you don't want or need all of the data to load at once. At times, this may slow down the user experience because the time-to-first-byte will be delayed until ALL GraphQL data is available.

At those times, you can tell Apollo _not_ to load on the server, by specifying options on your `graphql()` HOC:

```js
const GraphQLMessage = graphql(query, {
  options: { ssr: false }, // won't be called during SSR
})(Message);
```

See ["Skipping queries for SSR"](http://dev.apollodata.com/react/server-side-rendering.html#skip-for-ssr) in the official Apollo docs for info.
