<script setup>
defineProps({
  upcoming: Array,
  completed: Array,
  blogPosts: Array,
});
</script>

<template>
  <div class="max-w-4xl mx-auto p-6 rounded-2xl shadow-lg">
    <!-- Roadmap Header -->
    <h2 class="text-3xl font-bold text-white mb-4">Product Roadmap</h2>

    <p class="text-white">This project is looking to provide a simple webfront where you can deploy, edit and clone existing Nsites. Just using your private key you'll be able to see where your site is deployed, change relays and blossom servers and see some site statistics from our Nsite resolver ( gateway ) . Eventually you'll also be able to hook your own custom domain to the resolver and buy a subdomain route, the code will be open once cleaned up and will be split in a couple different versions (Simple Nsite resolver, Full gateway Docker,...). 
      This project will be the continuation of the <a href="https://cypher.space" class="underline" target="_blank">Cypher Space client & Template </a>. If you think this project is usefull and want to support reach out to help with the development or consider sending some sats. 💜 🧡 </p>


    <Framer />



    <div class="flex mt-12">
    <!-- Upcoming Features -->
    <div class="mb-6 flex-1 mx-2">
  <h3 class="text-xl font-semibold text-white mb-2">Upcoming Features</h3>
  <ul class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-2 gap-4">
    <li v-for="(item, index) in upcoming" :key="index" class="p-3 rounded-lg shadow text-white bg-gray-800">
      {{ item }}
    </li>
  </ul>
</div>

<div class="mb-6 flex-1 mx-2">
  <h3 class="text-xl font-semibold text-white mb-2">Completed Features</h3>
  <ul class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-2 gap-4">
    <li v-for="(item, index) in completed" :key="index" class="p-3 rounded-lg shadow text-white bg-gray-800">
      {{ item }}
    </li>
  </ul>
</div>


  </div>


  </div>
</template>
