<template>
  <ArticlesList :articles="articles" />
</template>

<script>
import axios from 'axios'

export default {
  name: 'ArticlesPage',

  // fetch data of articles
  async asyncData() {
    const { data } = await axios.get(
      'https://jsonplaceholder.typicode.com/posts/'
    )
    return { articles: data }
  },
}
</script>
