<template>
  <q-page class="flex flex-center">
    <!-- when the query response is not received yet, data from it is undefined,
    so before referring to it we need to use v-if -->
    <div v-if="post">
      GraphQL query result:<br>{{ post.title }}
    </div>
    <div v-else>
      loading...
    </div>
    <img
      alt="Quasar logo"
      src="~assets/quasar-logo-full.svg"
    >
  </q-page>
</template>

<script>
import gql from 'graphql-tag'

export default {
  name: 'PageIndex',

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
