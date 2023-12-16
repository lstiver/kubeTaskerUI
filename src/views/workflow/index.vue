<template>
  <div>
    <!-- 在页面加载时调用 fetchDataOnLoad 函数 -->
    <div>{{ workflowData }}</div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { getWorkflow } from '/@/api/process/workflow.ts'; 

const namespace = 'your_namespace';
const workflowName = 'your_workflow_name';
const workflowData = ref(null);

// 在页面加载时调用 fetchDataOnLoad 函数
onMounted(async () => {
  await fetchDataOnLoad();
});

// 获取 Workflow 数据的函数
async function fetchDataOnLoad() {
  try {
    // 获取 Workflow 数据
    const workflowRespond = await getWorkflow(namespace, workflowName);

    // 判断获取的数据是否为空
    if (workflowRespond.workflow === null) {
      console.log('Workflow 数据为空');
    } else {
      console.log('Workflow 数据不为空');
      // 访问 workflowRespond.workflow 对象的属性
      console.log('Workflow 名称:', workflowRespond.workflow.name);
      // 将数据存储在 workflowData 中
      workflowData.value = workflowRespond.workflow;
    }
  } catch (error) {
    console.error('发生错误：', error);
  }
}
</script>
