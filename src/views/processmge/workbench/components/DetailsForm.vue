<template>
  <div>
  <a-button class="editable-add-btn" @click="editalgorithm" style="margin-bottom: 10px" type="primary">编辑算法</a-button>
  </div>
  <a-table :columns="columns" :data-source="data" @change="onChange">
    <template #headerCell="{ column }">
      <template v-if="column.key === 'id'">
        <span> 算法编号 </span>
      </template>
    </template>

    <template #bodyCell="{ column, record }">
      <template v-if="column.key === 'status'">
        <span>
          <a-tag
            :color="record.status === '已完成' ? 'volcano' : record.status.length > 2 ? 'geekblue' : 'green'"
          >
            {{ record.status }}
          </a-tag>
        </span>
      </template>
      <template v-else-if="column.key === 'action'">
        <span>
          <a-button>查看</a-button>
        </span>
      </template>
    </template>
  </a-table>
</template>
<script lang="ts" setup>
  import axios from 'axios';
  import { computed, defineComponent, reactive, Ref, ref, UnwrapRef } from 'vue';
  import type { TableColumnType, TableProps } from 'ant-design-vue';

  type TableDataType = {
    key: number;
    name: string;
    type: number;
    cpuNum: number;
    status: string;
  };

  const columns: TableColumnType<TableDataType>[] =  [
    {
      title: 'ID',
      dataIndex: 'id',
      key: 'id',
      align: "center"
    },
    {
      title: '算法名称',
      dataIndex: 'name',
      key: 'name',
      align: "center"
    },
    {
      title: '算法类型',
      dataIndex: 'type',
      key: 'type',
      align: "center"
    },

    {
      title: '执行情况',
      key: 'status',
      dataIndex: 'status',
      align: "center",
      filters: [
      {
        text: '草稿',
        value: '草稿',
      },
      {
        text: '进行中',
        value: '进行中',
      },
      {
        text: '已完成',
        value: '已完成',
      },
    ],
    filterMultiple: false,
    onFilter: (value: string | number | boolean, record: TableDataType) => record.status === value,
    },
    {
      title: '查看结果',
      key: 'action',
      align: "center"
    },
  ];

  const onChange: TableProps<TableDataType>['onChange'] = (pagination, filters, sorter) => {
  console.log('params', pagination, filters, sorter);
};

  const data = ref([]);

  const load = async () => {
    try {
      let response = await axios('http://localhost:3003/posts');
      data.value = response.data;
      console.log(data.value);
    } catch (error) {
      console.log(error);
    }
  };
  load();

  const editalgorithm = () => {

}

</script>
