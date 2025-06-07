<template>
  <div class="container py-4">
    <h2 class="text-center mb-4">🛍️ Product Catalog</h2>
    <SearchBar @search="search = $event" />
    <div class="text-end mb-3">
      <button class="btn btn-outline-success" @click="openModal('create')"> Add Product</button>
    </div>
    <div class="row">
      <ProductCard
        v-for="product in filtered"
        :key="product.id"
        :product="product"
        @edit="openModal('edit', product)"
        @delete="openModal('delete', product)"
      />
    </div>
   <ProductModal
  v-if="showModal"
  :mode="modalMode"
  :product="selectedProduct"
  @close="showModal = false"
  @refresh="handleRefresh"
/>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'
import { Product } from '../types/product'
import ProductCard from '../components/ProductCard.vue'
import ProductModal from '../components/ProductModal.vue'
import SearchBar from '../components/SearchBar.vue'

const products = ref<Product[]>([])
const showModal = ref(false)
const modalMode = ref<'create' | 'edit' | 'delete'>('create')
const selectedProduct = ref<Product | null>(null)
const search = ref('')

const fetchProducts = async () => {
  const res = await axios.get<Product[]>('https://fakestoreapi.com/products')
  products.value = res.data.reverse()
}
const handleRefresh = (newProduct?: Product, mode?: 'create' | 'edit' | 'delete') => {
  showModal.value = false

  if (newProduct && mode === 'create') {
    products.value.unshift(newProduct)
  } else if (newProduct && mode === 'edit') {
    const index = products.value.findIndex(p => p.id === newProduct.id)
    if (index !== -1) products.value[index] = newProduct
  } else if (newProduct && mode === 'delete') {
    products.value = products.value.filter(p => p.id !== newProduct.id)
  }
}


const filtered = computed(() =>
  products.value.filter(p => p.title.toLowerCase().includes(search.value.toLowerCase()))
)

const openModal = (mode: 'create' | 'edit' | 'delete', product: Product | null = null) => {
  modalMode.value = mode
  selectedProduct.value = product
  showModal.value = true
}

onMounted(fetchProducts)
</script>
