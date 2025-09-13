<script setup lang="ts">
import { reactive, ref } from 'vue'
import CircleProgress from './circle_progress.vue'
import Barrage from './barrage.vue'
import Components from './components/components.vue'

// 时间
var time = ref({
  h: 0,
  m: 0,
  s: 0,
})

// 背景图片
// var bg = ref("url('https://t.alcy.cc/fj')")
// var bg = ref("url('img/bg.webp')")
// var bg = ref("url('')")

// 时间更新
setInterval(() => {
  var date = new Date()
  time.value.h = date.getHours()
  time.value.m = date.getMinutes()
  time.value.s = date.getSeconds()
}, 1000)
</script>

<template class="full-screen">
  <div class="hang bg full-screen" style="background-image: url('./img/bg.jpg')"></div>
  <div class="hang z1 full-screen">
    <barrage />
  </div>
  <div class="hang z999 center-display full-screen">
    <circle-progress id="hour" :value="time.h" :valueMax="24" />
    <circle-progress id="minute" :value="time.m" :valueMax="60" />
    <circle-progress id="second" :value="time.s" :valueMax="60" />
  </div>
  <div class="rt z99999">
    <Components />
  </div>
</template>

<style scoped>
.center-display {
  display: flex;
  justify-content: center;
  align-items: center;
}

.full-screen {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

.hang {
  position: fixed;
  top: 0;
  left: 0;
}

.bg {
  background-color: #000030;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  z-index: -9999;
}

circle-progress {
  z-index: 1;
}

.rt {
  position: fixed;
  top: 0px;
  right: 0px;
}

.z999 {
  z-index: 999;
}

.z99999 {
  z-index: 99999;
}

.z1 {
  z-index: 1;
}
</style>

<style></style>
