<template>
  <div style="margin-right: 10px; display: inline">
    <el-input v-model="tokenStr" placeholder="token" style="width: 200px; --el-input-border-radius: 5px 0 0 5px"
              clearable/>
    <el-button plain type="primary" :icon="Select"
               style="margin-left: -1px; --el-border-radius-base: 0 5px 5px 0; --el-button-border-color: var(--el-button-bg-color)"
               @click="saveToken"/>
  </div>
</template>

<script setup>
import {ref} from "vue";
import {Select} from "@element-plus/icons-vue";

const tokenStr = ref($cookies.get('token'))

function saveToken() {
  $cookies.set('token', tokenStr.value, -1)
  location.reload()
}
</script>
<script>
export default {
  name: "Login"
}
</script>

<style scoped>

@media (prefers-color-scheme: dark) {
  .el-input {
    --el-input-bg-color: #1C1C1E;
    --el-input-border-color: #1C1C1E;
    --el-input-text-color: white;
    --el-input-hover-border-color: #3f3f3f;
    --el-input-focus-border-color: #337ecc;
  }

  .el-button--primary.is-plain {
    --el-button-text-color: white;
    --el-button-bg-color: #2a598a;
    --el-button-hover-bg-color: #3375b9;
    --el-button-hover-border-color: #3375b9
  }
}

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

</style>