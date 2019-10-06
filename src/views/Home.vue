<template>
  <div class="home">
    <h1>New Product</h1>
    <div>
      Title: <input type="text" v-model="newProductName"><br>
      Price: <input type="text" v-model="newProductPrice"><br>
      Description: <input type="number" v-model="newProductDescription"><br>
      Image URL: <input type="text" v-model="newProductImage">
    </div>
    <button v-on:click="createProduct()">Add a Product</button>

    <h1>{{ message }}</h1>

    <div v-for="product in products">
      <h2>Name: {{product.name}}</h2>
      <img v-bind:src="product.image_url" v-bind:alt="recipe.title">
      <div>
        <button v-on:click="showProduct(product)"> More Info</button>
      </div>
      <div v-if="product === currentProduct">
        <p>Title: {{ product.title }}</p>
        <p>Price: {{ product.price }}</p>
        <p>Description: {{ product.description }}</p>
      </div>
    </div>  

  </div>
</template>

<style>
  img {
    width: 150px;
  };
</style>

<script>
import axios from "axios";  
export default {
  data: function() {
    return {
      message: "All Products",
      products: [], 
      newProductName: "", 
      newProductPrice: "", 
      newProductDescription: "", 
      newProductImage: "", 
      currentProduct: {}
    };
  },
  created: function() {
    axios.get("/api/products").then(response => {
      this.products = response.data;
      console.log(this.products);
    });  
  },
  methods: {
    createProduct: function() {
      var params = {
        name: this.newProductName, 
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImage
      };

      axios.post("api/products", params)
        .then(response => {
          console.log("Success", response.data);
          this.products.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    }, 
    showProduct: function(product) {
      if (this.currentProduct === product) {
        this.currentProduct = {};
      } else {
        this.currentProduct = product;
      }
    }
  }
};
</script>
