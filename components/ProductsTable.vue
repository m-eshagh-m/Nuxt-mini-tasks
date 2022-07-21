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
      :items="filteredProducts"
      :items-per-page="5"
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
export default {
  props: {
    products: {
      type: Array,
      required: true,
    },
  },

  //   data
  data() {
    return {
      search: '',
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
    filteredProducts() {
      const filterCategory = this.filters.category
      let allFilteredProducts = []

      // check the lenght of category array
      if (filterCategory.length !== 0) {
        let filteredProducts = []
        for (const cat of filterCategory) {
          filteredProducts = this.products.filter(
            (prodcut) => prodcut.category === cat
          )

          // combine two array
          allFilteredProducts = [
            ...new Set([...filteredProducts, ...allFilteredProducts]),
          ]
        }
      }

      // range price filter
      const minPrice = this.filters.range[0]
      const maxPrice = this.filters.range[1]

      if (minPrice !== this.minPrice || maxPrice !== this.maxPrice) {
        let filteredProducts = []
        // if category filter applies
        if (allFilteredProducts.length !== 0) {
          filteredProducts = allFilteredProducts.filter(
            (prodcut) => prodcut.price >= minPrice && prodcut.price <= maxPrice
          )
        } else {
          // if category filter not applies
          filteredProducts = this.products.filter(
            (prodcut) => prodcut.price >= minPrice && prodcut.price <= maxPrice
          )
        }

        // combine two array
        allFilteredProducts = [...filteredProducts]
      }

      // if no filter apply
      if (allFilteredProducts.length === 0) {
        allFilteredProducts = this.products
      }

      return allFilteredProducts
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
