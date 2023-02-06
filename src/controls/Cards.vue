<script setup>
import {ref} from 'vue'
import MissionCard from '@/controls/MissionCard.vue';
import {v4 as uuidV4} from 'uuid';
import draggable from "vuedraggable";


const cards = ref([]);

function elementAdd() {
  cards.value.push({
    id: uuidV4(),
    selectedValue: "",
    missionItems: undefined
  })
}

function elementRemove(index) {
  cards.value.splice(index, 1)
}

function updateUserInputValue(param) {
  console.log(param[0]);
  console.log(param[1]);
  console.log(param[2]);
  // cards.value[param[0]][param[1]] = param[2];
  console.log('updateUserInputValue function completed!');
}

const dragOptions = {
  animation: 250,
  ghostClass: "ghost-card"
}
</script>
<script>

</script>
<template>
  <el-button type="primary" @click="elementAdd">点击添加一个组件</el-button>
  <el-button type="primary" @click="elementRemove">点击删除一个组件</el-button>
  <draggable
      :list="cards"
      item-key="id"
      v-bind="dragOptions"
      handle=".handle">
    <template #item="{ element, index }">
      <mission-card
          :key="element.id"
          :id="element.id"
          :mission-items="element.missionItems"
          :selected-value="element.selectedValue"
          @closeCard="elementRemove(index)"
          @updateUserInputValues="updateUserInputValue"/>
    </template>
  </draggable>
</template>

<style scoped>
.ghost-card {
  opacity: 0.2;
}
</style>