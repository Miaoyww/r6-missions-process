<template>
  <el-button type="primary" @click="elementAdd">点击添加一个组件</el-button>
  <el-button type="primary" @click="elementRemove">点击删除一个组件</el-button>
  <draggable
      :list="cards"
      item-key="id"
      v-bind="dragOptions"
      handle=".handle"
      ghost=".ghost-card">
    <template #item="{ element, index }">
      <mission-card
          :key="element.id"
          :id="element.id"
          @closeCard="elementRemove(index)"/>
    </template>
  </draggable>
</template>
<style scoped>
.ghost-card {
  opacity: 0.3;
}
</style>
<script setup>
import {ref} from 'vue'
import MissionCard from '@/controls/MissionCard.vue';
import {v4 as uuidV4} from 'uuid';
import draggable from "vuedraggable";
import axios from "axios";

const missions = undefined;
const cards = ref([]);

// 添加一个mission卡片
function elementAdd(id) {
  if (id) {
    ``
  }
  cards.value.push({
    id: uuidV4(), // element key
  })
}
function getMissions(){
  axios({
    baseURL: "http://localhost:5173",
    url:"/api/missionlist",
    method: "get"}).then(res=>{
      console.log(res)
  })
}
getMissions()
// 删除一个mission卡片
function elementRemove(index) {
  cards.value.splice(index, 1)
}

const dragOptions = {
  animation: 250,
  ghostClass: "ghost-card"
}
</script>
<script>
export default {
  name: "index"
}
</script>


