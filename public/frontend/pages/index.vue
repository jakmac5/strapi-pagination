<template>
  <div>
    <h1>POSTS</h1>
    <div v-for="post in posts" :key="post.Id">
      <span>{{ post }}</span>
    </div>
    <button @click="showMore()">load more</button>
  </div>
</template>

<script>
import Vue from 'vue'
import PostsQuery from '~/apollo/posts'

export default Vue.extend({
  data() {
    return {
      posts: '',
      start: 0,
      ammountOfPosts: 1,
    }
  },

  apollo: {
    posts: {
      prefetch: true,
      query: PostsQuery,
      variables() {
        return {
          start: null,
          limit: this.ammountOfPosts,
        }
      },
    },
  },
  methods: {
    showMore() {
      // this.page++
      // Fetch more data and transform the original result
      this.$apollo.queries.posts.fetchMore({
        // New variables
        variables: {
          start: (this.start += this.ammountOfPosts),
        },
        // Transform the previous result with new data
        updateQuery: (previousResult, { fetchMoreResult }) => {
          return {
            __typename: previousResult.posts.__typename,
            // Merging the tag list
            posts: [...previousResult.posts, ...fetchMoreResult.posts],
          }
        },
      })
    },
  },
})
</script>
