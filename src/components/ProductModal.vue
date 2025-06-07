<template>
  <div class="modal fade show d-block" tabindex="-1" style="background: rgba(0,0,0,0.5)">
    <div class="modal-dialog">
      <div class="modal-content animate__animated animate__zoomIn">
        <div class="modal-header">
          <h5 class="modal-title">{{ title }}</h5>
          <button type="button" class="btn-close" @click="$emit('close')"></button>
        </div>
        <form @submit.prevent="handleSubmit">
          <div class="modal-body">
            <div v-if="mode === 'delete'">
              <p>Are you sure you want to delete <strong>{{ product?.title }}</strong>?</p>
            </div>
            <div v-else>
              <input class="form-control mb-2" v-model="form.title" placeholder="Title" required />
              <input class="form-control mb-2" v-model.number="form.price" type="number" placeholder="Price" required />
              <input class="form-control mb-2" v-model="form.category" placeholder="Category" />
              <input class="form-control mb-2" v-model="form.image" placeholder="Image URL" />
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="$emit('close')">Cancel</button>
            <button
              type="submit"
              class="btn"
              :class="{
                'btn-success': mode === 'create',
                'btn-warning': mode === 'edit',
                'btn-danger': mode === 'delete'
              }"
            >
              {{ submitText }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch, computed } from 'vue'
import axios from 'axios'
import { Product } from '../types/Product'

const props = defineProps<{
  mode: 'create' | 'edit' | 'delete',
  product: Product | null
}>()

const emit = defineEmits(['close', 'refresh'])

const form = ref<Product>({
  title: '',
  price: 0,
  category: '',
  image: '',
})

watch(
  () => props.product,
  (product) => {
    if (product && props.mode !== 'create') {
      form.value = { ...product }
    } else {
      form.value = { title: '', price: 0, category: '', image: '' }
    }
  },
  { immediate: true }
)

const handleSubmit = async () => {
    const id = props.product?.id
  let response

  if (props.mode === 'create') {
    response = await axios.post('https://fakestoreapi.com/products', form.value)
    emit('refresh', response.data, 'create')
  } else if (props.mode === 'edit' && id) {
    response = await axios.put(`https://fakestoreapi.com/products/${id}`, form.value)
    emit('refresh', response.data, 'edit')
  } else if (props.mode === 'delete' && id) {
    await axios.delete(`https://fakestoreapi.com/products/${id}`)
    emit('refresh', { ...props.product }, 'delete')
  }
}

const title = computed(() =>
  props.mode === 'create' ? 'Add Product' :
  props.mode === 'edit' ? 'Edit Product' : 'Delete Product'
)

const submitText = computed(() =>
  props.mode === 'create' ? 'Add' :
  props.mode === 'edit' ? 'Save' : 'Delete'
)
</script>
