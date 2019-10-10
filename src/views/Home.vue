<template>
  <div class="home">
    
    <div>
      <h1>New Product</h1>
      <ul>
        <li v-for="error in errors">{{ error }} </li>
      </ul>

  

      <div>
        Name: <input type="text" v-model="newProductName">
      </div>

      <div>
        Price: <input type="decimal" v-model="newProductPrice">
      </div>

      <div>
        Description: <input type="text" v-model="newProductDescription">
      </div>

      <div>
        Image_URL: <input type="text" v-model="newProductImageUrl">
      </div>



      <button v-on:click="createProduct()">Create product</button>
    </div> <!-- end of .new-form-section -->


    
  <div class="index-section">
    <h1>All Products</h1>

    <div v-for="product in products"> 
      <h2>{{ product.name }}</h2>
      <img v-on:click="showProduct(product)" v-bind:src="product.image_url">

      <div v-if="product === currentProduct">
        <p>Price: {{ product.price }}</p>
        <p>Description: {{ product.description}} </p>

      <div class="edit-form-section">
        <<h4>Edit Product</h4>

        <div>
          Name: <input type="text" v-model="product.name">
        </div>

        <div>
          Price: <input type="decimal" v-model="product.price">
        </div>

        <div>
          Description: <input type="text" v-model="product.description">
        </div>

        <div>
          Image_URL: <input type="text" v-model="product.image_url">
        </div>

        <button v-on:click="updateProduct(product)">Update</button>
      </div> <!-- end of .edit-form-section -->

      <div class ="destroy-section">
        <button v-on:click="destroyProduct(product)">Destroy</button>
      </div> <!-- end of .destroy-section -->
    </div> 

    </div>
      
    <br>
  </div>
</div> <!-- end of .index-section -->

  </div> 
</template>


<style>
img {
  width: 250px;
}
</style>


<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      products: [],
      errors: [],
      newProductName: "",
      newProductPrice: "", 
      newProductDescription: "",
      newProductImageUrl: "",
      currentProduct: {}
    };
  },
  created: function() {
    axios
      .get("/api/products")
      .then(response => {
        this.products = response.data;

      });

  },
  methods: {
    destroyProduct: function(productObject) {
      axios
        .delete("/api/products/" + productObject.id)
        .then(response => {
          var index = this.products.indexOf(productObject);
          this.products.splice(index, 1); 

        }); 
    },


    updateProduct: function(productObject){
      var clientParams = {
        name: this.productObject.name, 
        price: this.productObject.price,
        description: this.productObject.description,
        image_url: this.productObject.image_url

      };

      axios
        .patch("/api/products/" + productObject.id)
        .then(response => {
          console.log("Success", response.data); 

        });

    },
    showProduct: function(productObject) {
      if (this.currentProduct !== productObject) {

         this.currentProduct = productObject; 
      } else {
        this.currentProduct = {}; 
      }
     
    },
    createProduct: function() {
      console.log("Create the product...");

      var clientParams = {
      
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImageUrl

      }; 


      axios
        .post("/api/products", clientParams)
        .then(response =>{
          console.log("success", response.data); 
          this.products.push(response.data); 
          this.errors = [];


        this.newProductName = "";
        this.newProductPrice = "";
        this.newProductDescription = ""
        this.newProductImageUrl = ""; 


        })
        .catch(error => {
          console.log(error.response.data.errors); 
        });
        

    }
  }
};
</script>