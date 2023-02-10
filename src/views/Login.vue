<template>
  <div style="margin: 10px; display: inline; width: 245px; height: 32px">
    <el-input v-model="tokenStr" placeholder="token" style="width: 200px; --el-input-border-radius: 5px 0 0 5px" clearable/>
    <el-button plain type="primary" :icon="Select" style="margin-left: -1px; --el-border-radius-base: 0 5px 5px 0; --el-button-border-color: #dcdfe6" @click="saveToken"/>
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