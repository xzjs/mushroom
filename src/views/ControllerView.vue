<script lang="ts" setup>
import { reactive, ref } from "vue";
import axios from "axios";

const data = reactive({
  size: [3, 7],
});

function size() {
  axios
    .post("/api/size", { min: data.size[0], max: data.size[1] })
    .then((resp) => {
      console.log("ok");
    });
}

function signal(action: string): void {
  axios.post("/api/signal", { action: action }).then((resp) => {
    console.log("ok");
  });
}
</script>

<template>
  <div class="container">
    <el-space direction="vertical" size="large">
      <p>适合采摘的蘑菇尺寸</p>

      <div class="slider-demo-block">
        <el-slider v-model="data.size" range show-stops :max="8" :min="2" />
      </div>
      <el-button type="primary" @click="size">提交</el-button>

      <el-space spacer="|">
        <el-button type="primary" @click="signal('up')">升</el-button>
        <el-button type="primary" @click="signal('down')">降</el-button>
        <el-button type="primary" @click="signal('start')">开</el-button>
        <el-button type="primary" @click="signal('stop')">关</el-button>
      </el-space>
    </el-space>
  </div>
</template>

<style scoped>
.container {
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.slider-demo-block {
  width: 300px;
  display: flex;
  align-items: center;
}
.slider-demo-block .el-slider {
  margin-top: 0;
  margin-left: 12px;
}
</style>
