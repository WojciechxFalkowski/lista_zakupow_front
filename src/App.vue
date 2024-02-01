<template>
  <div class="container mt-4">
    <h2 class="mb-3">Lista Zakupów</h2>

    <!-- Formularz dodawania nowego produktu -->
    <div class="mb-3">
      <div class="input-group mb-3">
        <input v-model="newProductName" type="text" class="form-control" placeholder="Nazwa Produktu" required>
        <div class="input-group-append">
          <button @click="addProduct" type="button" class="btn btn-primary">Dodaj Produkt</button>
        </div>
      </div>
    </div>

    <!-- Lista produktów -->
    <ul class="list-group">
      <li v-for="product in products" :key="product._id" class="list-group-item d-flex justify-content-between align-items-center">
        <div class="form-inline">
          <div class="custom-control custom-checkbox">
            <input type="checkbox" class="custom-control-input" :id="`check-${product._id}`" v-model="product.purchased" @change="togglePurchase(product._id)">
            <label class="ps-2 custom-control-label" :for="`check-${product._id}`">{{ product.name }}</label>
          </div>
        </div>
        <button @click="deleteProduct(product._id)" type="button" class="btn btn-danger btn-sm">Usuń</button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// Reactive state
const products = ref([]);
const newProductName = ref('');
const BACKEND_URL = import.meta.env.VITE_BACKEND_URL
// Fetch products from the backend
const fetchProducts = async () => {
  try {
    const response = await fetch(`${BACKEND_URL}/products`);
    if (response.ok) {
      products.value = await response.json();
    }
  } catch (error) {
    console.error('Failed to fetch products:', error);
  }
};

// Add a new product
const addProduct = async () => {
  try {
    const response = await fetch(`${BACKEND_URL}/products`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ name: newProductName.value }),
    });
    if (response.ok) {
      newProductName.value = '';
      await fetchProducts(); // Refresh the list after adding
    }
  } catch (error) {
    console.error('Failed to add product:', error);
  }
};

// Toggle purchase status of a product
const togglePurchase = async (id) => {
  try {
    await fetch(`${BACKEND_URL}/products?id=${id}`, {
      method: 'PATCH',
    });
    await fetchProducts(); // Refresh the list after toggling
  } catch (error) {
    console.error('Failed to toggle purchase status:', error);
  }
};

// Delete a product
const deleteProduct = async (id) => {
  try {
    const response = await fetch(`${BACKEND_URL}/products?id=${id}`, {
      method: 'DELETE',
    });
    if (response.ok) {
      await fetchProducts(); // Refresh the list after deleting
    } else {
      console.error('Failed to delete product:', await response.text());
    }
  } catch (error) {
    console.error('Failed to delete product:', error);
  }
};


// Fetch products on component mount
onMounted(fetchProducts);
</script>


<style>
/* Add your CSS styling here */
</style>
