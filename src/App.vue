<script setup>
import { ref, watch } from 'vue'

const searchTerm = ref('')
const filteredProducts = ref([])
const isLoading = ref(false)

const findProducts = async term => {
  if (term) {
    isLoading.value = true
    fetch(`https://dummyjson.com/products/search?q=${term}`)
      .then(res => res.json())
      .then(data => {
        filteredProducts.value = data.products
        if(data.products.length === 0) {
          alert("No result")
        }
      }).catch((error) => {
      alert(error)
    }).finally(() => {
      isLoading.value = false
    })
  } else {
    filteredProducts.value = []
  }
}
const debounce = (fn, wait) => {
  let timer
  return (...args) => {
    clearTimeout(timer)
    timer = setTimeout(() => {
      fn.apply(this, args)
    }, wait)
  }
}

watch(searchTerm, debounce(newTerm => findProducts(newTerm), 300))
</script>

<template>
  <div class='w-full h-full flex flex-col gap-5 justify-center items-center'>
    <h1 class='text-4xl font-bold'>Gift Search Bar</h1>
    <input type='text' class='p-2 border-2 border-gray-dark' v-model='searchTerm' placeholder='Start typing...' />
    <p v-if='isLoading'>Please wait...</p>
    <ul v-else class='list-disc'>
      <li v-for='product in filteredProducts' :key='product.id'>{{ product.title }} - ${{ product.price }}</li>
    </ul>
  </div>
</template>
