<template>
  <a-table
    :columns="columns"
    :data-source="data"
    :pagination="pagination"
    :loading="loading"
    @change="handleTableChange"
  >
    <template #headerCell="{ column }">
      <template v-if="column.key === 'id'">
        <span> 流程编号 </span>
      </template>
    </template>

    <template #bodyCell="{ column, record }">
      <template v-if="column.key === 'tags'">
        <span>
          <a-tag
            v-for="tag in record.tags"
            :key="tag"
            :color="tag === '已完成' ? 'volcano' : tag.length > 2 ? 'geekblue' : 'green'"
          >
            {{ tag.toUpperCase() }}
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
  // import { SmileOutlined} from '@ant-design/icons-vue';
  import { computed } from 'vue';
  import type { TableProps } from 'ant-design-vue';
  import { usePagination } from 'vue-request';
  import axios from 'axios';

  const columns = [
    {
      title: 'ID',
      dataIndex: 'id',
      key: 'id',
    },
    {
      title: '流程名称',
      dataIndex: 'name',
      key: 'name',
    },
    {
      title: '资源消耗',
      dataIndex: 'cost',
      key: 'cost',
    },
    {
      title: 'CPU核数',
      dataIndex: 'cpuNum',
      key: 'cpuNum',
    },
    {
      title: '流程状态',
      key: 'tags',
      dataIndex: 'tags',
    },
    {
      title: '查看详情',
      key: 'action',
    },
  ];

  type APIParams = {
    results: number;
    page?: number;
    sortField?: string;
    sortOrder?: number;
    [key: string]: any;
  };
  type APIResult = {
    results: {
      id: number;
      name: string;
      cost: number;
      cpuNum: number;
      tags: '进行中' | '已完成' | '草稿';
    }[];
  };

  const queryData = (params: APIParams) => {
    return axios.get<APIResult>('http://localhost:3003/posts', { params });
  };
  (async () => {
    const res = await axios.get('http://localhost:3003/posts');
    console.log(res.data);
  })();

  const {
    data: data,
    run,
    loading,
    current,
    pageSize,
  } = usePagination(queryData, {
    //defaultParams: [parameter],
    // formatResult: ({ data, total }) => ({ data, total }),
    formatResult: (res) => res,
    pagination: {
      currentKey: 'pageNo',
      pageSizeKey: 'pageSize',
      totalKey: 'total', // 取值 data:{ data, total } 从 { 中开始 }
    },
  });

  const pagination = computed(() => ({
    total: 200,
    current: current.value,
    pageSize: pageSize.value,
  }));

  const handleTableChange: TableProps['onChange'] = (
    pag: { pageSize: number; current: number },
    filters: any,
    sorter: any,
  ) => {
    run({
      results: pag.pageSize,
      page: pag?.current,
      sortField: sorter.field,
      sortOrder: sorter.order,
      ...filters,
    });
  };
</script>
