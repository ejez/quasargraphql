# Quasargraphql

## Introduction

This project shows how to add graphql support to your quasar projects.

The project uses [Apollo GraphQL](https://www.apollographql.com) and its vue plugin [Vue Apollo](https://apollo.vuejs.org)

## Setup

The setup demonstrates two methods for adding GraphQL, the first is simple and does not support ssr, the second method includes additional code needed for ssr support. You can combine both if you develop/build your project in multiple modes (ssr, spa ...). We recommend starting with the first method initially to get a good grasp of the basics.

We will follow a similar install procedure to the one described in [Vue Apollo docs](https://apollo.vuejs.org/guide/installation.html#apollo-client-full-configuration), and when needed we will describe the additional steps specific to quasar.

The added code is full of comments and explanations.

### Non-ssr setup

1. Install the dependencies:

    ```sh
    yarn add vue-apollo graphql apollo-client apollo-link apollo-link-http apollo-cache-inmemory graphql-tag
    ```

2. Copy `src/boot/apollo.js` and `src/graphql/create-apollo-client.js` to your project. The boot file will attach an `apolloProvider` to our vue app. The `apolloProvider` is instantiated from class `VueApollo` which represents the specific implementation of apollo in Vue. It is fed with an `apolloClient` instance (from class `ApolloClient`)

3. Declare the boot file in `quasar.conf.js`:

    ```js
        boot: [
          ...
          'apollo'
        ]
    ```

### Ssr setup

1. Install the dependencies:

    ```sh
    yarn add vue-apollo graphql apollo-client apollo-link apollo-link-http apollo-cache-inmemory graphql-tag node-fetch
    ```

    **Note:** notice the added package `node-fetch` ([more info](https://www.apollographql.com/docs/link/links/http/#fetch-polyfill))

2. Copy `src/boot/apollo-ssr.js` and `src/graphql/create-apollo-client-ssr.js` to your project, also add the following inside the body tag of `src/index.template.html`:

    ```html
    <% if (htmlWebpackPlugin.options.ctx.mode.ssr) { %>
      {{{ renderState({ contextKey: 'apolloState', windowKey: '__APOLLO_STATE__' }) }}}
    <% } %>
    ```

    The added code for ssr support will attach the graphql query results retrieved in the server to the `ssr context` (`ssrContext.apolloState`), the `apolloState` holding the query results will be passed to the client through the html template (using `{{{ renderState(...) }}}`), and will be used to initialize apollo cache in the client to avoid running again the queries. (more info in the code comments)

3. Declare apollo boot file in `quasar.conf.js`:

    ```js
        boot: [
          ...
          'apollo-ssr'
        ]
    ```

### Combining both methods

If you use multiple quasar modes (ssr, spa...), you can follow the steps described in both methods, however for declaring boot files in `quasar.conf.js` use:

```js
    boot: [
        ...
      ctx.mode.ssr
        ? 'apollo-ssr'
        : 'apollo'
    ]
```

## Usage

Check the guide in [Vue Apollo docs](https://apollo.vuejs.org/guide/apollo/)

Example usage:

```html
<template>
  <div>
    <!-- when the query response is not received yet, data from it is undefined,
    so we need to use v-if -->
    <div v-if="post">
      GraphQL query result:<br>{{ post.title }}
    </div>
    <div v-else>
      loading...
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag'

export default {
  // https://apollo.vuejs.org/guide/apollo/#usage-in-vue-components
  apollo: {
    post: {
      query: gql`query {
        post(id: 5) {
          title
        }
      }`
    }
  }
}
</script>
```

## Example setup

To see an example setup compare the repository `master` with the initial tag `v0.1.0`:

[https://github.com/ejez/quasargraphql/compare/v0.1.0..master](https://github.com/ejez/quasargraphql/compare/v0.1.0..master)

## Demo

Demo [here](https://quasargraphql.netlify.com)
