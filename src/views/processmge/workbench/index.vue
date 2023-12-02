<template>
  <PageWrapper>
    <div class="lg:flex width:100%">

      <div class="w-1/4 enter-y">
        <Inform />
      </div>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
      <div class="w-3/5 enter-y">
        <SiteAnalysis />
      </div>
    </div>
    &nbsp;&nbsp;
    <div>
      <DetailsForm />
    </div>
  </PageWrapper>
</template>
<script lang="ts" setup>
  import { PageWrapper } from '/@/components/Page';
  import WorkbenchHeader from './components/WorkbenchHeader.vue';
  import DetailsForm from './components/DetailsForm.vue';
  import Inform from './components/Inform.vue';
  import SiteAnalysis from './components/SiteAnalysis.vue';
</script>


