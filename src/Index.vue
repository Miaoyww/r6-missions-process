<template>
  <el-button type="primary" @click="elementAdd">点击添加一个组件</el-button>
  <el-button type="primary" @click="test">测试按钮</el-button>
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
          :selector-options="selectorOptions"
          :missions-detail="missionsDetail"
          :data="cardData[element.id]"
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

const selectorOptions = ref([{value: "no-mission", label: "请选择任务"}]);
const missionsDetail = ref({'no-mission': {desc: "选择任务后任务说明会自动补全", target: 0}});
let cardData = {}

const cards = ref([]);

// 添加一个mission卡片
function elementAdd() {
  cards.value.push({
    id: uuidV4(), // element key
  })
}

// 删除卡片
function elementRemove(index) {
  cards.value.splice(index, 1)
}

function getMissions() {
  axios({
    withCredentials: true,
    url: "/api/mission_list",
    method: "get"
  }).then(res => {
    const missions = res.data;
    for (let index in missions) {
      const missionItem = missions[index];
      missionsDetail.value[missionItem.id] = {
        desc: missionItem.data.desc,
        target: missionItem.data.target
      }
    }
    for (let index in missions) {
      const missionItem = missions[index];
      selectorOptions.value.push({
        value: missionItem.id,
        label: missionItem.data.name,
        about: missionItem.data.about ? missionItem.data.about : undefined
      })
    }
    console.log(selectorOptions.value);
    console.log(missionsDetail.value);
  })
}

function getCards() {
  axios({
    withCredentials: true,
    url: "/api/cards/get",
    method: "get"
  }).then(res => {
    cardData = res.data["cards"]
    cards.value = res.data["index"]
    // console.log(res.data["index"])
    console.log(cardData)
  })
}

getMissions()
getCards()

function test() {
  console.log(cards)
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


