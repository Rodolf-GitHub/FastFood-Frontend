<template>
    <div>
      <!-- Botón para abrir el modal -->
      <button @click="showModal = true">Add Product</button>
  
      <!-- Modal -->
      <div class="modal" v-if="showModal">
        <div class="modal-content">
          <span class="close" @click="showModal = false">&times;</span>
          <h1>Add Product</h1>
    
          <form @submit.prevent="addProduct">
            <label for="productName">Name:</label>
            <input type="text" id="productName" v-model="productName" required><br>
    
            <label for="productPrice">Price:</label>
            <input type="number" id="productPrice" v-model.number="productPrice" min="0" step="0.01" required><br>
    
            <label for="productDescription">Description:</label>
            <textarea id="productDescription" v-model="productDescription"></textarea><br>
  
            <label for="productImage">Image:</label>
            <input type="text" id="productImage" v-model="productImage" required><br>
    
            <button type="submit">OK</button>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { useRuntimeConfig } from "#app";
const runtimeConfig = useRuntimeConfig()
  export default {
    
    data() {
      return {
        showModal: false, // Variable para controlar la visibilidad del modal
        productName: '',
        productPrice: 0,
        productDescription: '',
        productImage: ''
      };
    },
    methods: {
      async addProduct() {
        const productData = {
          name: this.productName,
          price: this.productPrice,
          description: this.productDescription,
          image: this.productImage
        };
  
        try {
            
          const token = document.cookie.replace(
            /(?:(?:^|.*;\s*)token\s*=\s*([^;]*).*$)|^.*$/,
            '$1'
          );
          const response = await fetch(runtimeConfig.public.backendurl+"/products", {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
              Authorization: `${token}`, // Agregar el token al encabezado Authorization
            },
            body: JSON.stringify(productData)
          });
  
          if (!response.ok) {
            throw new Error('Failed to add product');
          }
  
          alert('Product added successfully!');
          this.showModal = false; // Cerrar el modal después de añadir el producto
          // Puedes redirigir a otra página o hacer cualquier otra acción después de añadir el producto
        } catch (error) {
          console.error('Error adding product:', error);
          alert('Error adding product. Please try again.');
        }
      }
    }
  };
  </script>
  
  <style>
  /* Estilos para el modal */
  .modal {
    display: none;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0,0,0,0.5);
  }
  
  .modal-content {
    background-color: #fefefe;
    margin: 15% auto;
    padding: 20px;
    border: 1px solid #888;
    width: 80%;
  }
  
  .close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
  }
  
  .close:hover,
  .close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
  }
  </style>
  