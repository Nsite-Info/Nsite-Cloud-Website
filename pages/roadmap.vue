<script setup>
import Roadmap from '@/components/Roadmap.vue';

const upcomingFeatures = [
  'Single Site Resolver (Half Done)',
  'Update the translations (Nsite Info)',
  'Create a Gyser fund page',
  'Make Editor functional',
  'Turn this site into a Nsite',
  'Subdomain Name Routing & Sales (FTC)',
  'Allow Custom Domain Hooke up',
  'Simple Site Analytics',
  'Move Cypher Space template over',
  'Document how to Upload a theme for the Nsite'
];

const completedFeatures = [
  'Update the Learn Page with technical description',
  'Code Cleanup & Open Source',
  'Resolver Full (Subdomain Lvl)',
  'Document Repo with Github Action',
  'List what Blossom Servers Host the Site (debugger)',
  'Monthly BlogPosts',
];


</script>

<template>
  <div class="p-10  min-h-screen">

    <Roadmap :upcoming="upcomingFeatures" :completed="completedFeatures" />



  </div>
</template>
