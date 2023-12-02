<template>
  <div style="display: flex; align-items: center;">
  <span class="w-1/5">流程名称： </span>
    <a-input-search
      v-model:value="value"
      placeholder="此处是接口获取的流程名称"
      enter-button="确认修改"
      size="middle"
      @search="onSearch"
      label="流程名称"
      class="w-4/5 "
    />
  </div>
  <divc class="date">
  <span>创建日期：  {{ date }}</span>
  </divc>
</template>
<script lang="ts" setup>
import { reactive, toRaw } from 'vue';
import { ref } from 'vue';

const value = ref<string>('');

const onSearch = (searchValue: string) => {
  console.log('use value', searchValue);
  console.log('or use this.value', value.value);
};

const date = "2023-9-13"
</script>

<style>
.date {
  margin-top: 10%;
}


</style>
