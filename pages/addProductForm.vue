<template>
    <div class="div1">
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
  
        <button type="submit">OK</button> <!-- Añadido el botón aquí -->
      </form>
    </div>
    <div class="imagediv"></div>
  </template>
  
  <script>
  import { useRuntimeConfig } from "#app";
const runtimeConfig = useRuntimeConfig()
  export default {
    data() {
      return {
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
          window.location.href = '/productsAdmin';
          // Puedes redirigir a otra página o hacer cualquier otra acción después de añadir el producto
        } catch (error) {
          console.error('Error adding product:', error);
          alert('Error adding product. Please try again.');
        }
      }
    }
  };
  </script>

  