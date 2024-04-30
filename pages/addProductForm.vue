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

        <label
            for="productImage"
           
            >Imagen del producto</label
          >
          <input
            @change="handleFileChange"
            type="file"
            id="productImage"
            class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            required
          /><br>
  
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
        image_url: ''
      };
    },
    
    methods: {
      async addProduct() {
        const productData = {
          name: this.productName,
          price: this.productPrice,
          description: this.productDescription,
          image: this.image_url
        };
  
        try {
          const fileData = new FormData();
    if (imgProduct.value) {
      fileData.append("file", imgProduct.value);
      const response = await $fetch(
        `${runtimeConfig.public.backendurl}/upload-image`,
        {
          method: "POST",
          body: fileData,
        }
      );
      this.image_url = response.filePath;
    }
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
  const imgEvento = ref(null);

function handleFileChange(event) {
  imgEvento.value = event.target.files[0];
}
  </script>

  