<script setup>
import {computed, ref} from "vue";

const prop = defineProps({
  cardIndex: Number
})
console.log("get card Index",prop.cardIndex)
const missionDetails = ref({
  '': {
    desc: "在您选择任务后自动补全",
    target: 0
  },
  ms1: {
    desc: "使用Jackal、Caveira、Lion或Alibi的独特技能暴露10名敌人的信息。",
    target: 10
  }

});
const missionNames = [
  {
    value: "ms1",
    label: "情报工作"
  }
];
const emits = defineEmits(['emitValue']);
const missionName = ref('');
const missionCurrentProcess = ref(0);
const processPercentage = computed(() => {
  return missionCurrentProcess.value > 0 ? Math.round((missionCurrentProcess.value / missionDetails.value[missionName.value].target) * 100) : 0
})
const userControlsDisable = ref(true);

function processPlus() {
  if (missionCurrentProcess.value < missionDetails.value[missionName.value].target) {
    missionCurrentProcess.value++;
  }
}

function processMinus() {
  if (missionCurrentProcess.value > 0) {
    missionCurrentProcess.value--;
  }
}

function onValueChanged(value) {
  missionCurrentProcess.value = 0;
  userControlsDisable.value = !(value !== '');
}

const closeCard = () => {
  emits('closeCard', prop.cardIndex);
}
</script>

<template>
  <div class="card-div">
    <el-card class="box-card" shadow="never">
      <div class="wrapper">
        <el-select
            v-model="missionName"
            clearable
            filterable
            remote
            reserve-keyword
            @change="onValueChanged"
            placeholder="任务名称"
            remote-show-suffix>
          <el-option
              v-for="item in missionNames"
              :key="item.value"
              :label="item.label"
              :value="item.value"/>
        </el-select>
        <div style="horiz-align: right;vertical-align: top">
          <el-button @click="closeCard" class="card-button" style="font-size:15px;border-color: transparent">
            <el-icon>
              <CloseBold/>
            </el-icon>
          </el-button>
        </div>
      </div>
      <div>
        <!-- 任务简介 -->
        <p>{{ missionDetails[missionName].desc }}</p>
        <!-- 任务进度 -->
        <div style="text-align: right">
          <el-progress :percentage="processPercentage"
                       :show-text="false"/>
          <div style="margin-top: 2px">{{ missionCurrentProcess }}/{{ missionDetails[missionName].target }}</div>
        </div>
        <!-- 用户控件 -->
        <div style="text-align: right">
          <el-button @click="processMinus" class="card-button" :disabled="userControlsDisable">-</el-button>
          <el-button @click="processPlus" class="card-button" :disabled="userControlsDisable">+</el-button>
          <el-button class="card-button" :disabled="userControlsDisable">
            <el-icon>
              <Document/>
            </el-icon>
          </el-button>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'missionCard'
}
</script>

<style scoped>
.wrapper {
  width: 100%;
  height: auto;
  display: flex;
  justify-content: space-between;
  flex-direction: row;
  flex-wrap: wrap;
}

.card-div {
  margin: 20px;
  padding: 0px;
}

.card-button {
  width: 30px;
  height: 30px;
  font-size: 20px;
}

.el-card {
  width: 400px;
  height: 230px;
  --el-card-border-color: #c1c1c1
}

.el-button + .el-button {
  margin-left: 5px;
}
</style>