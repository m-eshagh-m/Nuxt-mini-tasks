<template>
  <section>
    <h1>Articles list link</h1>
    <div>
      <ul>
        <li v-for="article in articles" :key="article.id" class="item">
          <nuxt-link :to="'/' + article.id">{{ article.title }}</nuxt-link>
        </li>
      </ul>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    articles: {
      type: Array,
      required: true,
    },
  },
}
</script>

<style lang="scss" scoped>
ul {
  list-style: none;

  li a {
    color: rgb(15, 140, 223);
    text-decoration: none;
  }
}
</style>
