<template>
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 p-6">



      <div v-for="template in templates" :key="template.id" class="border-4 border-purple-600 shadow-lg rounded-2xl overflow-hidden">
        <img :src="template.image" :alt="template.title" class="w-full h-48 object-cover" />
        <div class="p-4">
          <h3 class="text-xl text-white font-semibold">{{ template.title }}</h3>
          <p class="text-white mt-2">{{ template.description }}</p>
          <!-- <button 
            class="mt-4 bg-purple-500 text-white px-4 py-2 rounded-lg hover:bg-purple-600 transition"
            @click="deployTemplate(template.id)"
          >
            Clone Nsite to Editor
          </button> -->


          <NuxtLink :to="localePath('/editor')" class="no-underline mt-4 bg-purple-500 text-white px-4 py-2 rounded-lg hover:bg-purple-600 transition">
            Clone Nsite to Editor
          </NuxtLink>


        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  const localePath = useLocalePath()
  const templates = ref([
    {
      id: 1,
      title: 'Single Landing Page',
      description: 'A simple single landing page with Nostr Links to your profile and other clients.',
      image: '/templates/soveng.png'
    },
    {
      id: 2,
      title: 'Cypher Space',
      description: 'A version of the Cypher Space client you can selfhost as a Nsite no cloudhost needed.',
      image: '/templates/cypher.png'
    },
    {
      id: 3,
      title: 'Sov Biz',
      description: 'A simple Nostr Ecommerce implementation based on the revised Nip-99 spec.',
      image: '/templates/sovbiz.png'
    },
    {
      id: 4,
      title: 'Sov Pub',
      description: 'A simple Nostr Long form content blog equipt with zaps and comment section.',
      image: '/templates/sovpub.png'
    }
  ]);
  
  const deployTemplate = (title) => {
    alert(`This is still a Work in progress to push this template to the editor.`);
  };
  </script>
  