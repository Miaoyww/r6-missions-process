<template>
  <div style="text-align: center">
    <Login/>
    <el-button type="primary" @click="elementAdd" style="max-width: 500px">点击添加一个组件</el-button>
    <el-button type="primary" @click="test">测试按钮</el-button>
    <draggable
        style="text-align:left"
        class="card-wrap"
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
  </div>
</template>
<style scoped>
.flip-list-move {
  transition: transform 0.5s;
}
.ghost-card {
  opacity: 0.3;
}

.card-wrap {
  display: grid;
  margin-top: 10px;
  grid-template-columns: repeat(auto-fit, minmax(314px, 470px));
  grid-row-gap: 20px;
  grid-column-gap: 20px;
  justify-content: center;
}

</style>
<script setup>
import {ref} from 'vue'
import MissionCard from '@/controls/MissionCard.vue';
import Login from '@/views/Login.vue';
import {v4 as uuidV4} from 'uuid';
import draggable from "vuedraggable";
import axios from "axios";

let selectorOptions = [{value: "no-mission", label: "请选择任务"}];
let missionsDetail = {'no-mission': {desc: "选择任务后任务说明会自动补全", target: 0}};
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

async function getMissions() {
  await axios({
    withCredentials: true,
    url: "/api/mission_list",
    method: "get"
  }).then(res => {
    const missions = res.data;
    for (let index in missions) {
      const missionItem = missions[index];
      missionsDetail[missionItem.id] = {
        desc: missionItem.data.desc,
        target: missionItem.data.target
      }
    }
    for (let index in missions) {
      const missionItem = missions[index];
      selectorOptions.push({
        value: missionItem.id,
        label: missionItem.data.name,
        about: missionItem.data.about ? missionItem.data.about : undefined
      })
    }
    console.log(selectorOptions);
    console.log(missionsDetail);
  })
  console.log(5555)
}

async function getCards() {
  await axios({
    withCredentials: true,
    url: "/api/cards/get",
    method: "get"
  }).then(res => {
    cardData = res.data["cards"]
    cards.value = res.data["index"]
    // console.log(res.data["index"])
    console.log(cardData)
    console.log(333)
  })
  console.log(4444)
}

await getMissions();
await getCards();

function test() {
  console.log(cards)
  const a = cards.value[1]
  cards.value[1] = cards.value[0]
  cards.value[0] = a

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


