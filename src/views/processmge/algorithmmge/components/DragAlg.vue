<template>
  <div class="msg">{{ state.message }}</div>
  <div class="itxst">
    <div class="group">
      <draggable
        :list="state.modules.arr1"
        ghost-class="ghost"
        handle=".move"
        filter=".forbid"
        :force-fallback="true"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @end="onEnd"
        :group="state.groupA"
        :fallback-class="true"
        :fallback-on-body="true"
        :touch-start-threshold="50"
        :fallback-tolerance="50"
        :move="onMove"
        :sort="false"
        :pull="clone"
      >
        <template #item="{ element }">
          <div class="item move">
            <label class="move">{{ element.name }}</label>
            <!-- <span v-html="element.name == '消息' ? 'www.itxst.com' : '内容....'"></span> -->
          </div>
        </template>
      </draggable>
    </div>
    <div class="group">
      <draggable
        :list="state.modules.arr2"
        ghost-class="ghost"
        handle=".move"
        filter=".forbid"
        :force-fallback="true"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @end="onEnd"
        group="itxst"
        :fallback-class="true"
        :fallback-on-body="true"
        :touch-start-threshold="50"
        :fallback-tolerance="50"
        :move="onMove"
      >
        <template #item="{ element }">
          <div class="item move">
            <label class="move">{{ element.name }}</label>
            <!-- <span>内容....</span> -->
          </div>
        </template>
      </draggable>
    </div>
  </div>
</template>
<script setup>
  import { ref, reactive } from 'vue';
  //导入draggable组件
  import draggable from 'vuedraggable';

  const state = reactive({
    groupA: {
      name: 'itxst',
      put: true, //允许拖入
      pull:'clone',
      put:false
      // pull: () => {
      //   let res = state.modules.arr1.length > 3;
      //   //当A组元素小于等于3个时，不允许再拖出元素了
      //   state.message = res ? '只能拖出1个' : '不能再拖了';
      //   return res;
      // },
    },
    modules: {
      arr1: [
        { name: '算法1', id: 1 },
        { name: '算法2', id: 2 },
        { name: '算法3', id: 3 },
        { name: '算法4', id: 4 },
      ],
      arr2: [
        { name: '已有算法1', id: 5 },
        { name: '已有算法2', id: 6 },
        { name: '已有算法3', id: 7 },
      ],
    },
  });

  //拖拽开始的事件
  const onStart = () => {
    console.log('开始拖拽');
  };

  //拖拽结束的事件
  const onEnd = () => {
    console.log('结束拖拽');
  };

  const onMove = (e, originalEvent) => {
    //不允许停靠
    if (e.relatedContext.element.disabledPark == true) return false;

    return true;
  };
</script>
<style>
  body {
    margin: 0;
    padding: 0;
    background-color: #f1f1f1;
  }

  .msg {
    padding: 10px 20px 0;
  }

  .itxst {
    display: flex;
    padding: 20px;
    background-color: #f1f1f1;
  }

  .group {
    display: flex;
    flex-direction: column;
    place-content: center flex-start;
    width: 32%;
    margin-right: 20px;
  }

  .item {
    display: flex;
    height: 36px;
    margin-bottom: 10px;
    padding: 0;
    border: solid 1px #ddd;
    background-color: #fff;
    text-align: left;
    user-select: none;
  }

  .item > label {
    padding: 6px 10px;
    color: #333;
  }

  .item > label:hover {
    cursor: move;
  }

  .item > span {
    padding: 6px 10px;
    color: #666;
  }

  .ghost {
    border: solid 1px rgb(19 41 239) !important;
  }

  .chosenClass {
    border: solid 1px red;
    opacity: 1;
  }

  .fallbackClass {
    background-color: aquamarine;
  }
</style>
