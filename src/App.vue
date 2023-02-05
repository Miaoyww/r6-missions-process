<script setup>
import {ref} from 'vue'
import MissionCard from '@/controls/MissionCard.vue';

const cards = ref([])

// ------------临时----------------
cards.value.push({})
// -------------------------------

function elementAdd() {
  cards.value.push({})
}

function elementRemove() {
  if (cards.value.length >= 1) {
    cards.value.pop()
  }
}
</script>

<template>

  <button @click="elementAdd">点击添加一个组件</button>
  <button @click="elementRemove">点击删除一个组件</button>
  <mission-card v-for="item in cards"/>

</template>