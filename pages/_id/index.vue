<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div>
    <h1>{{ articles.title }}</h1>
    <p>{{ articles.body }}</p>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  async asyncData({ params }) {
    const { data } = await axios.get(
      'https://jsonplaceholder.typicode.com/posts/' + params.id
    )
    return { articles: data }
  },

  // add article title to title tag in head
  head() {
    return {
      title: this.articles.title,
    }
  },
}
</script>
