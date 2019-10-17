<template>
  <div class="container">
    <h1>All Product</h1>

    <div v-for="product in filterBy(products, searchBar, 'name')">
      <h2>{{ product.name }}</h2>
      <p><img src="product.image_url"></p>
      <p>{{ product.description }}</p>
      <p>{{ product.price }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Vue2Filters from "vue2-filters";
export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      product: []
    };
  },
  created: function() {
    axios.get("/api/products").then(response => {
      this.products = response.data;
    });
  },
  methods: {}
};
</script>