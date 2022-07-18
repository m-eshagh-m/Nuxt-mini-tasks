<template>
  <v-row>
    <ProductsTable :products="products" />
  </v-row>
</template>

<script>
import axios from 'axios'

export default {
  name: 'ProductsPage',

  // fetch data
  async asyncData() {
    const { data } = await axios.get('https://fakestoreapi.com/products/')
    return { products: data }
  },
}
</script>
