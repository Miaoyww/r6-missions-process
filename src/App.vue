<script setup>
import {ref} from 'vue'
import MissionCard from '@/controls/MissionCard.vue';

const cards = ref([])
const cardRef = ref()
// ------------临时---------------
elementAdd();

// -------------------------------

function elementAdd() {
  cards.value.push({})
}

function elementRemove(index) {
  console.log("deleting index", index)
  cards.value.splice(index, 1)
}
</script>

<template>

  <button @click="elementAdd">点击添加一个组件</button>
  <button @click="elementRemove">点击删除一个组件</button>
  <div class="cards-div">
    <el-space :size="20" wrap>
      <mission-card v-for="(item, index) in cards" :card-index="index" @closeCard="elementRemove"/>
    </el-space>
  </div>
</template>

<style>
.cards-div{
  width: 1000px;
  /*margin-left:auto;*/
  /*margin-right:auto;*/
}
</style>
