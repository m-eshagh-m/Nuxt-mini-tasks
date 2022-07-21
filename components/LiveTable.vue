<template>
  <v-container fluid>
    <v-row class="mb-2">
      <v-col cols="12" md="4">
        <span>Search</span>
        <v-text-field
          v-model="search"
          solo
          color="teal"
          prepend-inner-icon="mdi-magnify"
        />
      </v-col>
      <v-col cols="12" md="4">
        <span>Category</span>
        <v-autocomplete
          v-model="filters.category"
          :items="categories"
          item-color="teal"
          color="teal"
          hide-no-data
          hide-details
          multiple
          solo
          clearable
          chips
          prepend-inner-icon="mdi-filter-menu-outline"
          @click:clear="clearCategories"
        />
      </v-col>
      <v-col cols="12" md="4">
        <span>Range Price</span>
        <v-range-slider
          v-model="filters.range"
          :max="maxPrice"
          :min="minPrice"
          hide-details
          item-color="teal"
          color="teal"
          class="align-center"
        >
          <template #prepend>
            <v-text-field
              :value="filters.range[0]"
              class="mt-0 pt-0"
              hide-details
              type="number"
              style="width: 60px"
              @change="$set(filters.range, 0, $event)"
            ></v-text-field>
          </template>
          <template #append>
            <v-text-field
              :value="filters.range[1]"
              class="mt-0 pt-0"
              hide-details
              type="number"
              style="width: 60px"
              @change="$set(filters.range, 1, $event)"
            ></v-text-field>
          </template>
        </v-range-slider>
      </v-col>
    </v-row>
    <v-data-table
      :headers="headers"
      :search="search"
      :items="products"
      :items-per-page="5"
      :loading="loading"
      class="elevation-1"
    >
      <template #item="{ item }">
        <tr>
          <td>{{ item.id }}</td>
          <td><img :src="item.image" alt="" /></td>
          <td>{{ item.price }}</td>
          <td>{{ item.category }}</td>
          <td>{{ item.rating.count }}</td>
        </tr>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  //   data
  data() {
    return {
      products: [],
      search: '',
      loading: true,
      productsReceived: [],
      headers: [
        { text: 'Id', value: 'id' },
        { text: 'Image', value: 'image' },
        { text: 'Price', value: 'price' },
        { text: 'Category', value: 'category' },
        { text: 'Count', value: 'count' },
      ],
      filters: {
        category: [],
        range: [0, 2000],
      },
      categories: [
        "men's clothing",
        'jewelery',
        'electronics',
        "women's clothing",
      ],
      maxPrice: 1000,
      minPrice: 0,
    }
  },

  computed: {
    category() {
      return this.filters.category
    },
  },

  watch: {
    category: {
      handler(cats) {
        this.getFilteredData(cats)
      },
    },
  },

  // created
  beforeMount() {
    this.getData()
  },

  // methods
  methods: {
    // fetch data

    async asyncData(url) {
      const response = await axios.get(url)
      this.productsReceived = response.data
    },

    async getData() {
      const parameters = this.$route.query
      const paramsLength = Object.keys(parameters).length
      if (paramsLength !== 0) {
        this.filters.category = parameters.q
      } else {
        await this.asyncData('https://fakestoreapi.com/products?limit=5')
        this.products = this.productsReceived
        this.loading = false
      }
    },

    async getFilteredData(cats) {
      let filteredProducts = ''
      if (cats.length > 0) {
        for (const cat of cats) {
          const route = 'https://fakestoreapi.com/products/category/' + cat
          await this.asyncData(route)

          // combine two array
          filteredProducts = [
            ...new Set([...this.productsReceived, ...filteredProducts]),
          ]
          this.$router.push({ query: { q: cats } })
        }
        this.products = filteredProducts
        this.loading = false
      } else {
        await this.asyncData('https://fakestoreapi.com/products?limit=5')
        this.products = this.productsReceived
      }
    },

    clearCategories() {
      this.$router.replace({ query: null })
    },
  },
}
</script>

<style lang="scss">
.v-input--range-slider {
  background: #1e1e1e;
  min-height: 48px;
  padding: 4px 12px;
  box-shadow: 0px 3px 1px -2px rgb(0 0 0 / 20%),
    0px 2px 2px 0px rgb(0 0 0 / 14%), 0px 1px 5px 0px rgb(0 0 0 / 12%);
  border-radius: 4px;
}

table {
  img {
    width: 25px;
    height: 25px;
  }
}
</style>
