<template>
  <div style="text-align: center">
    <Login/>
    <el-button type="primary" @click="elementAdd" style="max-width: 500px; margin: 10px 0px" :disabled="!isToken1">
      添加任务
    </el-button>
    <el-button style="display: none" type="primary" @click="test">测试按钮</el-button>
    <el-alert
        v-if="!isToken2"
        title="Token无效"
        type="error"
        description="请输入正确的Token"
        show-icon
        center
        :closable="false"
        :effect="isBlack ? 'dark' : 'light'"
    />
    <div>
      <draggable
          style="text-align:left"
          class="card-wrap"
          :list="cards"
          item-key="id"
          v-bind="dragOptions"
          handle=".handle"
          ghost=".ghost-card"
          @move="actionNumber = 2">
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
  </div>
</template>
<style scoped>

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

@media (prefers-color-scheme: dark) {
  .el-button--primary {
    --el-button-text-color: white;
    --el-button-bg-color: #2a598a;
    --el-button-border-color: #2a598a;
    --el-button-hover-bg-color: #3375b9;
    --el-button-hover-border-color: #3375b9
  }
}

</style>
<script setup>
import {onBeforeUnmount, ref} from 'vue';
import MissionCard from '@/controls/MissionCard.vue';
import Login from '@/views/Login.vue';
import {v4 as uuidV4} from 'uuid';
import draggable from "vuedraggable";
import axios from "axios";


let selectorOptions = [{value: "no-mission", label: "请选择任务", about: undefined}];
let missionsDetail = {'no-mission': {desc: "", target: 0}};
let cardData = {};
let actionNumber = -1;

const isToken1 = ref(false)
const isToken2 = ref(true)
const cards = ref([]);

onBeforeUnmount(() => {
  clearInterval(timer)
})

const timer = setInterval(postIndex, 500);

(async function () {
  await axios({
    withCredentials: true,
    url: "/api/token/verify",
    method: "get"
  }).then(async res => {
    isToken1.value = res.data;
    isToken2.value = res.data;
    if (res.data) {
      await getMissions();
      await getCards();
    }
  })
})()

const isBlack = ref(false)

const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)')

function darkModeHandler() {
  if (mediaQuery.matches) {
    isBlack.value = true
  } else {
    isBlack.value = false
  }
}

darkModeHandler()
// 监听模式变化
mediaQuery.addEventListener('change', darkModeHandler)

// 添加一个mission卡片
function elementAdd() {
  const id = uuidV4()
  cards.value.unshift({
    id: id, // element key
  })
  axios({
    withCredentials: true,
    url: "/api/cards/add",
    method: "get",
    params: {id: id}
  })
}

// 删除卡片
function elementRemove(index) {
  axios({
    withCredentials: true,
    url: "/api/cards/del",
    method: "get",
    params: {id: cards.value[index]["id"]}
  })
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
}

async function getCards() {
  await axios({
    withCredentials: true,
    url: "/api/cards/get",
    method: "get"
  }).then(res => {
    cardData = res.data["cards"]
    cards.value = res.data["index"]
    console.log(res.data)
  })
}

function test() {
  console.log(cards)
  // const a = cards.value[1]
  // cards.value[1] = cards.value[0]
  // cards.value[0] = a

}

async function postIndex() {
  if (actionNumber === 0) {
    axios({
      withCredentials: true,
      url: "/api/cards/index",
      method: "post",
      data: {
        data: (function () {
          let k = []
          console.log(cards.value)
          for (const i in cards.value) {
            k.push(cards.value[i]["id"])
          }
          return k
        })()
      }
    })
  }
  if (actionNumber >= 0) {
    actionNumber--;
  }
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


