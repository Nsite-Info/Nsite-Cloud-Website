<template>
  <div>
    <!-- <NuxtWelcome /> -->
    <NuxtLayout>
      <NuxtLoadingIndicator color="#ffcc00" :height="5" />
    <NuxtPage />
  </NuxtLayout>
  </div>
</template>
<script setup>

import data from "~/config/settings";

useHead({
  title: data.name + ' - ' + data.tagline,
  meta: [
    { property: 'og:description', content: 'Nsite deploy your static site on blossom servers, resolve over nostr gateways' },
    { property: 'og:image', content: 'https://nsite.cloud/bannerimg.png' },
    { name: 'twitter:card', content: 'summary_large_image' },
    { name: 'twitter:title', content: data.name + ' - ' + data.tagline },
    { name: 'twitter:image', content: 'https://nsite.cloud/bannerimg.png' }
  ],
  link: [
    { rel: 'manifest', href: '/manifest.json' }
  ],
  bodyAttrs: {
    class: "bg-colorBgLight dark:bg-colorBgDark",
  },
});




</script>