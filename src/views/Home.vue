<template>
  <div class="home">
    <h1>New Product</h1>
    <div>
      Title: <input type="text" v-model="newProductName"><br>
      Price: <input type="text" v-model="newProductPrice"><br>
      Description: <input type="text" v-model="newProductDescription"><br>
      Image URL: <input type="text" v-model="newProductImageUrl">
    </div>
    <button v-on:click="createProduct()">Add a Product</button>

    

    <button v-on:click="setSortAttribute('price')">Sort By Price
      <i v-if="sortAttribute === 'price' && sortAscending === 1" class="icon-arrow-up"></i>
    </button>
    <button v-on:click="setSortAttribute('name')">Sort By Name</button>

    <div>
      <br>
      Search:
      <input type="text" v-model="searchBar" list="names">
    </div>
    <datalist id="names">
      <option v-for="product in products">{{ product.name}}</option>  
    </datalist> 
      <div v-for="product in orderBy(filterBy(products, searchBar),sortAttribute, sortAscending)">
            
        <h2>Name: {{ product.name }}</h2>
        <img v-bind:src="product.image_url" v-bind:alt="product.title">
      <div>
        <button v-on:click="showProduct(product)"> More Info</button>
      </div>
      
    
      <div v-if="product === currentProduct">
        <p>Title: {{ product.title }}</p>
        <p>Price: {{ product.price }}</p>
        <p>Description: {{ product.description }}</p>
        <h4>Edit Product</h4> 
        <div>
        Title: <input type="text" v-model="product.title"><br>
        Price: <input type="text" v-model="product.price"><br>
        Description: <input type="text" v-model="product.description"><br>
        Image URL: <input type="text" v-model="product.image_url">
      </div>
      <button v-on:click="updateProduct(product)"> Update </button><br>
      <button v-on:click="destroyProduct(product)"> Destroy </button>
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
import Vue2Filters from "vue2-filters";  

export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      message: "All the Products",
      products: [], 
      newProductName: "", 
      newProductPrice: "", 
      newProductDescription: "", 
      newProductImage: "", 
      currentProduct: {}, 
      searchBar: "", 
      sortAttribute: "", 
      sortAscending: 1
    };
  },
  created: function() {
    axios.get("/api/products").then(response => {
      this.products = response.data;
      console.log(this.products);
    });  
  },
  methods: {
    setSortAttribute: function(attribute) {
      if (this.sortAttribute === attribute) {
        this.sortAscending = this.sortAscending * -1;
      } else {
        this.sortAscending = 1;
        this.sortAttribute = attribute;
      }

    },

    createProduct: function() {
      var params = {
        name: this.newProductName, 
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImageUrl
      };

      axios.post("/api/products", params)
        .then(response => {
          console.log("Success", response.data);
          this.products.push(response.data);
          this.newProductName = "";
          this.newProductPrice = "";
          this.newProductDescription = "";
          this.newProductImageUrl = "";
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
    },
    updateProduct: function(product) {
      var params = {
        name: product.name, 
        price: product.price,
        description: product.description,
        image_url: product.imageUrl
      };
      axios.patch("/api/products/" + product.id, params)
        .then(response => {
          console.log("Success", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
    destroyProduct: function(product) {
      axios.delete("/api/products" + product.id)
        .then(response => {
          console.log("Success", response.data);
          var index = this.products.indexOf(product);
          this.products.splice(index, 1);
        });
    }
  }
};
</script>
