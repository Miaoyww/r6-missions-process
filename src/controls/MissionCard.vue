<script setup>
import {ref} from "vue";

const missionNames = [
  {
    value: "A",
    label: "a"
  },
  {
    value: "B",
    label: "b"
  }
]
const missionDesc = ref("在您选择任务后自动补全")
const missionName = ref("")
const missionTargetProcess = ref(10)
const missionCurrentProcess = ref(0)

function processPlus() {
  if (missionCurrentProcess.value < missionTargetProcess.value) {
    missionCurrentProcess.value++;
  }
}

function processMinus() {
  if (missionCurrentProcess.value > 0) {
    missionCurrentProcess.value--;
  }
}
</script>

<template>
  <div class="card-div">
    <el-card class="box-card" shadow="never">
      <el-select
          v-model="missionName"
          filterable
          remote
          reserve-keyword
          placeholder="任务名称"
          remote-show-suffix
      >
        <el-option
            v-for="item in missionNames"
            :key="item.value"
            :label="item.label"
            :value="item.value"
        />
      </el-select>
      <p>{{ missionDesc }}</p>
      <div style="text-align: right">
        <el-progress :percentage="Math.round((missionCurrentProcess / missionTargetProcess) * 100)" :show-text="false"/>
        <div style="margin-top: 2px">{{ missionCurrentProcess }}/{{ missionTargetProcess }}</div>
      </div>
      <div style="margin: 10px 0; text-align: right">
        <el-button @click="processMinus" class="card-button">-</el-button>
        <el-button @click="processPlus" class="card-button">+</el-button>
        <el-button class="card-button">
          <el-icon>
            <Document/>
          </el-icon>
        </el-button>
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
.card-div {
  margin: 20px;
}
.card-button{
  width: 30px;
  height: 30px;
  font-size: 20px;
}
.el-card {
  width: 400px;
  height: 200px;
  --el-card-border-color: #c1c1c1
}
.el-button+.el-button{
  margin-left: 5px;
}
</style>