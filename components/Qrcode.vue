<template>
    <div class="flex justify-center items-center ">
      <div class=" p-6 text-center max-w-sm w-full">
        <h2 class="text-xl font-semibold mb-4 text-white">{{ title }}</h2>
        <a :href="link" target="_blank">
        <canvas ref="qrCanvas" class="mx-auto invert"></canvas>
    </a>
        <p class="text-gray-600 text-sm mt-4">
          <span class="font-mono text-gray-600 text-white truncate">{{ content }}</span>
        </p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { onMounted, ref, watch } from 'vue'
  import QRCode from 'qrcode'
  import { defineProps } from 'vue'
  
  // Define the content prop
  const props = defineProps({
    content: {
      type: String,
      required: true
    },
    title: {
      type: String,
      required: true
    },
    link: {
      type: String,
      required: true
    }
  })
  
  const qrCanvas = ref(null)
  
  // Function to generate the QR code
  const generateQRCode = () => {
    if (qrCanvas.value && props.content) {
      QRCode.toCanvas(qrCanvas.value, props.content, {
        width: 200,
        margin: 1,
      })
    }
  }
  
  // Watch the content prop and regenerate the QR code on change
  watch(() => props.content, generateQRCode)
  
  // Generate the QR code when the component is mounted
  onMounted(generateQRCode)
  </script>
  
  <style scoped>
  /* Add any additional styling here if needed */
  </style>
  