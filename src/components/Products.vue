<template>
  <div class="container">
    <h1>Product Management</h1>
    
    <!-- Display error messages -->
    <div v-if="error" class="alert alert-danger">{{ error }}</div>
    
    <div v-else>
      <!-- Action Buttons -->
      <div class="action-buttons">
        <button @click="fetchProducts" class="btn btn-primary">Get Products</button>
        <button @click="toggleAddProductForm" class="btn btn-success">Add Product</button>
        <button @click="toggleUpdateProductForm" class="btn btn-warning">Update Product</button>
        <button @click="toggleDeleteProductForm" class="btn btn-danger">Delete Product</button>
      </div>
      
      <!-- Product List Section -->
      <div v-if="showProducts" class="product-list">
        <h2>Products</h2>
        <ul>
          <li v-for="product in products" :key="product.id" class="product-item">
            <strong> ID: {{ product.id }}</strong><br><strong> Name: {{ product.name }}</strong> <br>Price: {{ product.price }}$ <br> Description: {{ product.description}} <br> Image: {{ product.image}}
          </li>
        </ul>
      </div>
      
      <!-- Add Product Form -->
      <div v-if="showAddForm" class="form-section">a
        <h2>Add Product</h2>
        <form @submit.prevent="addProduct" class="form">
          <div class="form-group">
            <label>Product Name</label>
            <input v-model="newProduct.name" class="form-control" placeholder="Product name" required />
          </div>
          <div class="form-group">
            <label>Product Description</label>
            <input v-model="newProduct.description" class="form-control" placeholder="Product description" required />
          </div>
          <div class="form-group">
            <label>Product Price</label>
            <input v-model="newProduct.price" type="number"  step="0.01" class="form-control" placeholder="Product price" required />
          </div>
          <div class="form-group">
            <label>Image</label>
            <input v-model="newProduct.image"  class="form-control" placeholder="image .." required />
          </div>
          <button type="submit" class="btn btn-success">Add Product</button>
        </form>
      </div>
      
      <!-- Update Product Form -->
      <div v-if="showUpdateForm" class="form-section">
        <h2>Update Product</h2>
        <form @submit.prevent="updateProduct" class="form">
          <div class="form-group">
            <label>Product ID</label>
            <input v-model="updateProductData.id" type="number" class="form-control" placeholder="Product ID" required />
          </div>
          <div class="form-group">
            <label>Product Name</label>
            <input v-model="updateProductData.name" class="form-control" placeholder="Product name" />
          </div>
          <div class="form-group">
            <label>Product Description</label>
            <input v-model="updateProductData.description" class="form-control" placeholder="Product description" />
          </div>
          <div class="form-group">
            <label>Product Price</label>
            <input v-model="updateProductData.price" type="number" step="0.01" class="form-control" placeholder="Product price" />
          </div>
          <div class="form-group">
            <label>Image</label>
            <input v-model="updateProductData.image"  class="form-control" placeholder="image .." />
          </div>
          <button type="submit" class="btn btn-warning">Update Product</button>
        </form>
      </div>
      
      <!-- Delete Product Form -->
      <div v-if="showDeleteForm" class="form-section">
        <h2>Delete Product</h2>
        <form @submit.prevent="deleteProduct" class="form">
          <div class="form-group">
            <label>Product ID</label>
            <input v-model="deleteProductId" type="number" class="form-control" placeholder="Product ID" required />
          </div>
          <button type="submit" class="btn btn-danger">Delete Product</button>
        </form>
        </div>
        <p v-if = "showCount ">Total products: {{ products.length }}</p>
        <p v-if = "showStatus " class="done">Done..</p>
   
  </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      newProduct: {
        name: '',
        description: '',
        price: null,
        image: '', 
      },
      updateProductData: {
        id: null,
        name: '',
        description: '',
        price: null,
        image: '', 
      },
      deleteProductId: null,
      showAddForm: false,
      showUpdateForm: false,
      showDeleteForm: false,
      showProducts: false, 
      showCount: false,
      showStatus: false,
      error: null 
    };
  },
  methods: {
    fetchProducts() {
      axios.get('http://localhost:9090/api/products')
        .then(response => {
          this.products = response.data;
          this.error = null; // Clear error if successful
          this.showProducts = true; // Show product list after fetching
          this.showCount = true; // Show total count of products after fetching
          this.showAddForm=false;
          this.showUpdateForm=false;
          this.showDeleteForm=false;
          this.showStatus = true; // Show success message after fetching
        })
        .catch(error => {
          this.error = "Failed to load products: " + error.message;
        });
    },
    addProduct() {
      axios.post('http://localhost:9090/api/products', this.newProduct)
        .then(response => {
          this.newProduct = { name: '', description: '', price: null, image: '' }; //reset values
          this.error = null; // Clear error if successful
          this.showStatus=true; 
        })
        .catch(error => {
          this.error = "Failed to add products: " + error.message;
        });
    },
    updateProduct() {
      const productData = {  //create a new product with the user input
        name: this.updateProductData.name, 
        description: this.updateProductData.description, 
        price: this.updateProductData.price,
        image: this.updateProductData.image, 
      };
      
      axios.put(`http://localhost:9090/api/products/${this.updateProductData.id}`, productData)
        .then(response => {
          this.updateProductData = { id: null, name: '', description: '', price: null, image: ''};
          this.error = null;
          this.showStatus=true;
        })
        .catch(error => {
          this.error = "Failed to update products: " + error.message;
        });
    },
    deleteProduct() {
      axios.delete(`http://localhost:9090/api/products/${this.deleteProductId}`)
        .then(response => {
          this.deleteProductId = null;
          this.error = null; // Clear error if successful
          this.showStatus=true;
        })
        .catch(error => {
          this.error = "Failed to delete products: \n" + error.message;
        });
    },
    toggleAddProductForm() {
      this.showAddForm = !this.showAddForm;
      this.showUpdateForm = false;
      this.showDeleteForm = false;
      this.showProducts = false;
      this.showCount = false;
      this.showStatus=false;
    },
    toggleUpdateProductForm() {
      this.showUpdateForm = !this.showUpdateForm;
      this.showAddForm = false;
      this.showDeleteForm = false;
      this.showProducts = false;
      this.showCount = false;
      this.showStatus=false;
    },
    toggleDeleteProductForm() {
      this.showDeleteForm = !this.showDeleteForm;
      this.showAddForm = false;
      this.showUpdateForm = false;
      this.showProducts = false;
      this.showCount = false;
      this.showStatus=false;
    },
  },
  mounted() {
  }
};
</script>

<style scoped>
.container {
  width: 100%;
  margin: 0 auto;
  padding: 20px;


}



h1 {
  text-align: center;
  color: #42b983;
  margin-bottom: 30px;
  font-size: 60px;
}

.alert {
  text-align: center;
  margin-bottom: 20px;
}
li::marker{
  color: white;
}
.action-buttons {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}


.product-list ul{
  color: #000;
}
.product-item {
  padding: 10px;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  margin-bottom: 10px;
  border-radius: 4px;
  color: #000;
}

.form-section {
  margin-bottom: 30px;
  background-color: #f4f4f4;
  padding: 20px;
  border-radius: 4px;
  color: #000;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.form-control {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.btn {
  padding: 10px 15px;
  border-radius: 4px;
  border: none;
  color: white;
  cursor: pointer;
  margin:10px;
}

.btn-primary {
  background-color: #007bff;
}

.btn-success {
  background-color: #28a745;
}

.btn-warning {
  background-color: #ffc107;
}

.btn-danger {
  background-color: #dc3545;
}
.done{
  color: green;
  font-weight: bold;
  animation: fadeOut 1.5s ease-in-out forwards;
}

@keyframes fadeOut {
  0% { opacity: 1; }
  100% { opacity: 0; display: none; }
}
</style>
