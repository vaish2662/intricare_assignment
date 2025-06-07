<template>
  <div class="mb-3">
    <input
      class="form-control"
      placeholder="🔍 Search products..."
      v-model="query"
      @input="$emit('search', query)"
    />
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
const query = ref('')
</script>
