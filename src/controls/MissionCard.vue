<template>
  <div>
    <el-card class="box-card" shadow="never" body-style="padding: 16px">
      <div class="handle card-handle"></div>
      <el-button @click="emit('closeCard')" :icon="CloseBold" class="card-close-button" plain/>
      <div class="wrapper">
        <el-select
            v-model="selectedName"
            filterable
            remote
            reserve-keyword
            @change="onValueChanged"
            placeholder="任务名称"
            remote-show-suffix
            :filter-method="filterMethod">
          <el-option
              v-for="item in selectorOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            <span style="float: left">{{ item.label }}</span>
            <span style="margin-left: 10px;color: var(--el-text-color-secondary);font-size: 10px;">{{
                item.about
              }}</span>
          </el-option>
        </el-select>
      </div>
      <div class="mission-details-div">
        <!-- 任务简介 -->
        <p>{{ missionsDetail[selectedName].desc }}</p>
      </div>
      <div style="position: absolute;bottom: 0;width: calc(100% - 32px);margin-bottom: 16px">
        <!-- 用户控件 -->
        <div style="text-align: right; margin-bottom: 10px">
          <div style="display: inline; margin-right: 10px">
            <el-input style="width: 125px"
                      input-style="text-align: right"
                      v-model="currentProcess"
                      @change="onValueChanged"
                      :disabled="userControlsDisable"
                      :formatter="formatter"
                      :parser="(value) => value.replace(/\D*/g, '')">
              <template #append>
                <div style="display: inline; margin: 0 -15px">/ {{ missionsDetail[selectedName].target }}</div>
              </template>
            </el-input>
          </div>
          <el-button-group style="margin-right: 5px">
            <el-button @click="ProcessMinus" class="card-button" :disabled="userControlsDisable">-</el-button>
            <el-button @click="ProcessPlus" class="card-button" :disabled="userControlsDisable">+</el-button>
            <el-button @click="ProcessComplete" class="card-button" :icon="CircleCheck"
                       :disabled="userControlsDisable"/>
          </el-button-group>
        </div>
        <div>
          <el-progress :percentage="processPercentage" :show-text="false" :stroke-width="15"
                       style="margin-bottom: -8px"/>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script setup>
import {computed, onMounted, onBeforeUnmount, ref} from "vue";
import {CircleCheck, CloseBold} from "@element-plus/icons-vue";
import axios from "axios";

// 接收父级组件传来的参数
const prop = defineProps(['id', 'selectorOptions', 'missionsDetail', 'data'])
const emit = defineEmits(['closeCard', 'updateUserInputValues']);


onMounted(() => {
  console.log(`the component is now mounted.`)
})

onBeforeUnmount(() => {
  clearInterval(timer)
})

const timer = setInterval(getActions, 500);
const missionsDetail = ref(prop.missionsDetail)

const sourceSelectorOptions = ref(prop.selectorOptions);
const selectorOptions = ref(prop.selectorOptions);


let actionNumber = -1;

const selectedName = ref(prop.data ? prop.data["mission"] : 'no-mission');

const currentProcess = ref(prop.data ? prop.data["target"] : 0);  // 任务进度
const userControlsDisable = ref(selectedName.value === 'no-mission');  // 用户控件是否禁止


const processPercentage = computed(() => {  // 进度条百分比
  if (currentProcess.value > 0) {
    if (currentProcess.value > missionsDetail.value[selectedName.value].target) {
      return 100;
    }
    return Math.round((currentProcess.value / missionsDetail.value[selectedName.value].target) * 100)
  } else {
    return 0;
  }
})


async function getActions() {
  if (actionNumber === 0) {
    axios({
      withCredentials: true,
      url: "/api/cards/modify",
      method: "post",
      data: {
        id: prop.id,
        data: {
          mission: selectedName.value,
          target: currentProcess.value
        }
      }
    })
  }
  if (actionNumber >= 0) {
    actionNumber--;
  }
}

function ProcessPlus() {
  if (currentProcess.value < missionsDetail.value[selectedName.value].target) {
    currentProcess.value++;
  }
  onValueChanged();
}

function ProcessMinus() {
  if (currentProcess.value > 0) {
    currentProcess.value--;
  }
  onValueChanged();
}

function ProcessComplete() {
  currentProcess.value = missionsDetail.value[selectedName.value].target
  onValueChanged();
}

// 用户修改值后调用
async function onValueChanged() {
  console.log('onValueChanged');
  currentProcess.value =
      currentProcess.value < missionsDetail.value[selectedName.value].target ?
          parseInt(currentProcess.value ? currentProcess.value : 0) : missionsDetail.value[selectedName.value].target;
  userControlsDisable.value = selectedName.value === 'no-mission';
  actionNumber = 2;
}

// formatter needed
function formatter(value) {
  return value;
}

function filterMethod(val) {
  selectorOptions.value = sourceSelectorOptions.value.filter((item) => {
    if (item.about) {
      return item.label.toLowerCase().includes(val.toLowerCase()) || item.about.toLowerCase().includes(val.toLowerCase());
    } else {
      return item.label.toLowerCase().includes(val.toLowerCase())
    }
  })
}

</script>


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
  margin-top: -8px;
  margin-left: -8px;
}

.card-close-button {
  position: absolute;
  right: 0;
  top: 0;
  width: 35px;
  height: 35px;
  font-size: 15px;
  border-color: #f1f1f1;
  background-color: #f1f1f1;
  --el-button-hover-text-color: #E81123 !important;
  --el-button-hover-border-color: #f1f1f1 !important;
  --el-button-hover-bg-color: #f1f1f1 !important;
  --el-button-active-border-color: #f1f1f1 !important;
  --el-button-active-text-color: #DC5C66;
  --el-button-active-bg-color: #f1f1f1;
}

.mission-details-div {
  margin-top: 20px;
}

.card-button {
  width: 30px;
  height: 30px;
  font-size: 20px;
}

.box-card {
  position: relative;
  /*width: 470px;*/
  min-width: 314px;
  max-width: 470px;
  height: 240px;
  --el-card-border-color: #d2d2d2;
  --el-card-border-radius: 10px;
}

.card-handle {
  cursor: move;
  background: #f1f1f1;
  width: 100%;
  height: 48px;
  position: absolute;
  top: 0;
  left: 0;
}

@media (prefers-color-scheme: dark) {
  .box-card {
    --el-card-border-color: #1C1C1E;
    --el-card-bg-color: #1C1C1E;
    --el-fill-color-blank: rgba(0, 0, 0, 0);
    --el-text-color-regular: white;
    --el-border-color: #29292C !important;
    --el-fill-color-light: #1C1C1E;
    --el-border-color-lighter: #29292C;
    --el-color-primary: #2A598A;
    --el-disabled-bg-color: #1C1C1E;
    --el-disabled-border-color: #29292C;
  }

  .el-button {
    --el-button-hover-text-color: white;
    --el-button-hover-bg-color: #29292C;
    --el-button-hover-border-color: #3f3f3f;
    --el-button-disabled-border-color:#29292C;
  }

  .el-popper {
    --el-bg-color-overlay: black;
  }

  .card-handle {
    background: #1C1C1E;
  }

  .card-close-button {
    border-color: #1C1C1E;
    background-color: #1C1C1E;
    --el-button-hover-text-color: #810b17 !important;
    --el-button-hover-border-color: #1C1C1E !important;
    --el-button-hover-bg-color: #1C1C1E !important;
    --el-button-active-border-color: #1C1C1E !important;
    --el-button-active-text-color: #DC5C66;
    --el-button-active-bg-color: #1C1C1E;
  }

  .el-input {
    --el-input-bg-color: #29292c;
    --el-input-border-color: #29292c;
    --el-input-text-color: white;
    --el-input-hover-border-color: #3f3f3f;
    --el-input-focus-border-color: #337ecc;
  }

  .mission-details-div {
    color: white;
  }

}

/*.el-button + .el-button {*/
/*  margin-left: 5px;*/
/*}*/
</style>