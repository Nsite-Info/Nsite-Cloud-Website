<template>
    <div v-if="visible" class="p-4 bg-yellow-100 border-l-4 border-yellow-500 text-yellow-700 rounded-lg flex items-center justify-between mb-12">
      <div class="flex items-center">
        <ExclamationTriangleIcon class="w-12 h-12 mr-2" />
        <p>Warning: This Project is still under heavy development, even the Nsite Resolver only works with small sites. Check out the <NuxtLink :to="localePath('/roadmap')" class="inline underline"> Roadmap Page </NuxtLink> to read the project scope and todo list, i'll post a update every month on the progress here once I have moved over the blog section. </p>
      </div>
      <button @click="visible = false" class="text-yellow-700 hover:text-yellow-900">
        <XMarkIcon class="w-5 h-5" />
      </button>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import { XMarkIcon, ExclamationTriangleIcon } from '@heroicons/vue/24/solid';
  
  const localePath = useLocalePath()


  const visible = ref(true);
  </script>
  