<template>
  <div class="product-table">
    <table>
      <thead>
        <tr>
          <th>Nombre</th>
          <th>$Precio</th>
          <th>Imagen</th>
          <th>Descripcion</th>
          
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <td>{{ product.name }}</td>
          <td>{{ product.price }}</td>
          <td>
            <img :src="getImageUrl(product.image_url)" alt="Product Image" />
          </td>
          <td>{{ product.description }}</td>
          
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRuntimeConfig } from "#app";
const runtimeConfig = useRuntimeConfig()
const products = ref([]);


onMounted(async () => {
  try {
    const response = await fetch(runtimeConfig.public.backendurl+"/products");
    if (!response.ok) {
      throw new Error("Failed to fetch products");
    }
    const data = await response.json();
    products.value = data;
  } catch (error) {
    console.error("Error fetching products:", error);
  }
});

const getImageUrl = (imageName) => {
  return `${runtimeConfig.public.backendurl}/${imageName}`;
};

const addToCart = (product) => {
  // Implement your addToCart logic here
};

const removeFromCart = (product) => {
  // Implement your removeFromCart logic here
};
</script>

