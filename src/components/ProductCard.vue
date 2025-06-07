<template>
  <div class="col-md-4 mb-4 animate__animated animate__fadeInUp">
    <div class="card h-100 shadow-sm">
      <img :src="product.image" class="card-img-top" alt="product image" />
      <div class="card-body">
        <h5 class="card-title">{{ product.title }}</h5>
        <p class="card-text text-muted">${{ product.price }}</p>
        <div class="d-flex justify-content-between">
          <button class="btn btn-warning btn-sm" @click="$emit('edit', product)">Edit</button>
          <button class="btn btn-danger btn-sm" @click="$emit('delete', product)">Delete</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { Product } from '../types/Product'

defineProps<{
  product: Product
}>()
</script>
