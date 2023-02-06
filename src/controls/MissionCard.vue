<script setup>
import {computed, ref} from "vue";
import {CircleCheck, CloseBold} from "@element-plus/icons-vue";

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

const prop = defineProps(['id', 'selectedValue', 'missionItems'])
const missionNames = computed(() => {
  return prop.missionItems ? prop.missionItems : [{value: "ms1", label: "情报工作"}]
})
const emit = defineEmits(['closeCard', 'updateUserInputValues']);
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

function processComplete() {
  missionCurrentProcess.value = missionDetails.value[missionName.value].target
}

function onValueChanged(value) {

  missionCurrentProcess.value = 0;
  userControlsDisable.value = !(value !== '');
  valueUpdate();
  console.log('onValueChanged completed!')
}

function valueUpdate() {
  emit('updateUserInputValues', [prop.id, 'selectedValue', missionName.value])
}

function formatter(value) {
  if (value > missionDetails.value[missionName.value].target) {
    return missionDetails.value[missionName.value].target
  } else if (value === '0') {
    return '0'
  } else {
    return value.replace(/\b(0+)/g, "")
  }
}

</script>

<template>
  <el-card class="box-card" shadow="never" body-style="padding: 16px">
<!--    <div class="handle card-handle"></div>-->
    <el-button @click="emit('closeCard')" :icon="CloseBold" class="card-close-button" plain/>
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
    </div>
    <div>
      <!-- 任务简介 -->
      <p>{{ missionDetails[missionName].desc }}</p>
    </div>
    <div style="position: absolute;bottom: 0;width: 438px;margin-bottom: 16px">
      <!-- 用户控件 -->
      <div style="text-align: right; margin-bottom: 10px">
        <div style="display: inline; margin-right: 10px">
          <el-input style="width: 125px"
                    input-style="text-align: right"
                    v-model="missionCurrentProcess"
                    :disabled="userControlsDisable"
                    :formatter="formatter"
                    :parser="(value) => value.replace(/\D*/g, '')"
          >
            <template #append>
              <div style="display: inline; margin: 0 -15px">/ {{ missionDetails[missionName].target }}</div>
            </template>
          </el-input>
        </div>
        <el-button-group>
          <el-button @click="processMinus" class="card-button" :disabled="userControlsDisable">-</el-button>
          <el-button @click="processPlus" class="card-button" :disabled="userControlsDisable">+</el-button>
          <el-button @click="processComplete" class="card-button" :icon="CircleCheck" :disabled="userControlsDisable"/>
        </el-button-group>
      </div>
      <div>
        <el-progress :percentage="processPercentage" :show-text="false" :stroke-width="15" style="margin-bottom: -8px"/>
      </div>
    </div>
  </el-card>
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

.card-close-button {
  position: absolute;
  right: 0;
  top: 0;
  width: 35px;
  height: 35px;
  font-size: 15px;
  border-color: transparent;
  --el-button-hover-text-color: #E81123 !important;
  --el-button-hover-border-color: transparent !important;
  --el-button-active-border-color: transparent !important;
  --el-button-active-text-color: #DC5C66;
}

.card-button {
  width: 30px;
  height: 30px;
  font-size: 20px;
}

.box-card {
  position: relative;
  width: 470px;
  height: 240px;
  /*margin: 20px;*/
  --el-card-border-color: #d2d2d2
}

.card-handle{
  width: 470px;
  height: 240px;
  position: absolute;
  top:0;
  left:0
}
/*.el-button + .el-button {*/
/*  margin-left: 5px;*/
/*}*/
</style>