<template>
  <div class="product-table">
    <!-- Nuevo botón para añadir un nuevo producto -->
    <button @click="showModal = true" class="add-new-product">
      Añadir nuevo producto
    </button>
    <!-- Modal para agregar un nuevo producto -->
    <div class="modal" v-if="showModal">
      <div class="modal-content">
        <span class="close" @click="showModal = false">&times;</span>
        <h1>Añadir nuevo producto</h1>

        <form @submit.prevent="addProduct">
          <label for="productName">Nombre:</label>
          <input
            type="text"
            id="productName"
            v-model="productName"
            required
          /><br />

          <label for="productPrice">Precio:</label>
          <input
            type="number"
            id="productPrice"
            v-model.number="productPrice"
            min="0"
            step="0.01"
            required
          /><br />

          <label for="productDescription">Descripción:</label>
          <textarea
            id="productDescription"
            v-model="productDescription"
          ></textarea
          ><br />

          <label for="productImage">Imagen:</label>
          <input
            type="file"
            id="productImage"
            @change="handleImageChange"
            accept="image/*"
            required
          /><br />

          <button type="submit">Agregar Producto</button>
        </form>
      </div>
    </div>
    <!--fin del modal-->

    <table v-if="!showModal">
      <thead>
        <tr>
          <th>Nombre</th>
          <th>$Precio</th>
          <th>Imagen</th>
          <th>Descripción</th>
          <th>Acciones</th>
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
          <td>
            <!-- Botón para eliminar el producto -->
            <button class="remove-product" @click="removeProduct(product)">
              Eliminar producto
            </button>

            <!-- Botón para registrar venta -->
          
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRuntimeConfig } from "#app";
const runtimeConfig = useRuntimeConfig();
const products = ref([]);

onMounted(async () => {
  try {
    const response = await fetch(runtimeConfig.public.backendurl + "/products");
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

const removeProduct = async (product) => {
  try {
    // Obtener el token de las cookies
    const token = document.cookie.replace(
      /(?:(?:^|.*;\s*)token\s*=\s*([^;]*).*$)|^.*$/,
      "$1"
    );

    const response = await fetch(
      runtimeConfig.public.backendurl + "/products/" + product.id,
      {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
          Authorization: `${token}`, // Agregar el token al encabezado Authorization
        },
      }
    );

    if (!response.ok) {
      throw new Error("Failed to remove product");
    }
    if (response.ok) {
      // Actualizar la lista de productos después de eliminar el producto
      const updatedProducts = products.value.filter((p) => p.id !== product.id);
      products.value = updatedProducts;
    }
  } catch (error) {
    console.error("Error removing product:", error);
  }
};

const registerSale = async (product) => {
  try {
    // Obtener el token de las cookies
    const token = document.cookie.replace(
      /(?:(?:^|.*;\s*)token\s*=\s*([^;]*).*$)|^.*$/,
      "$1"
    );

    // Crear el objeto de datos del balance
    const balanceData = {
      product: product.id,
    };

    // Enviar la solicitud para crear un nuevo registro en la tabla de balances
    const response = await fetch(runtimeConfig.public.backendurl + "/balance", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Authorization: `${token}`,
      },
      body: JSON.stringify(balanceData),
    });

    if (!response.ok) {
      throw new Error("Failed to register sale");
    }

    alert("Sale registered successfully!");
    // Puedes hacer cualquier otra acción después de registrar la venta si es necesario
  } catch (error) {
    console.error("Error registering sale:", error);
    alert("Error registering sale. Please try again.");
  }
};

const showModal = ref(false); // Estado para controlar la visibilidad del modal
const productName = ref("");
const productPrice = ref(0);
const productDescription = ref("");
const productImage = ref("");

const addProduct = async () => {
  const productData = {
    name: productName.value,
    price: productPrice.value,
    description: productDescription.value,
    image_url: productImage.value,
  };

  try {
    const token = document.cookie.replace(
      /(?:(?:^|.*;\s*)token\s*=\s*([^;]*).*$)|^.*$/,
      "$1"
    );

    const response = await fetch(
      runtimeConfig.public.backendurl + "/products",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `${token}`, // Agregar el token al encabezado Authorization
        },
        body: JSON.stringify(productData),
      }
    );

    if (!response.ok) {
      throw new Error("Failed to add product");
    }

    alert("Product added successfully!");
    // Actualizar la lista de productos después de agregar el nuevo producto
    const newProduct = await response.json();
    products.value.push(newProduct);
    // Cerrar el modal después de agregar el producto
    showModal.value = false;
  } catch (error) {
    console.error("Error adding product:", error);
    alert("Error adding product. Please try again.");
  }
};

const handleImageChange = async (event) => {
  const file = event.target.files[0]; // Obtener el archivo de la lista de archivos seleccionados

  const formData = new FormData(); // Crear un objeto FormData para enviar archivos
  formData.append("file", file); // Agregar el archivo al objeto FormData

  try {
    const response = await fetch(
      `${runtimeConfig.public.backendurl}/upload-image`,
      {
        method: "POST",
        body: formData,
      }
    );

    if (!response.ok) {
      throw new Error("Failed to upload image");
    }

    const data = await response.json();
    // Actualizar el valor del campo de imagen con la ruta de la imagen cargada
    productImage.value =await data.filePath;
    console.log(productImage.value)
  } catch (error) {
    console.error("Error uploading image:", error);
    alert("Error uploading image. Please try again.");
  }
};
</script>
