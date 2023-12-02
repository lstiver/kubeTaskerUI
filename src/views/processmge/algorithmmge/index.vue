<template>
  <div class="p-4">
    <GrowCard :loading="loading" class="enter-y" />
    <DragAlg class="!my-4 enter-y" :loading="loading" />
  </div>
</template>
<script lang="ts" setup>
  import { ref } from 'vue';
  import DragAlg from './components/DragAlg.vue';
  import SiteAnalysis from './components/SiteAnalysis.vue';

  const loading = ref(true);

  setTimeout(() => {
    loading.value = false;
  }, 1500);
</script>
