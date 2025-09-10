<script setup lang="ts">
import { reactive, ref } from 'vue'
import CircleProgress from './circle_progress.vue'
import Barrage from './barrage.vue'

// 时间
var time = ref({
  h: 0,
  m: 0,
  s: 0,
})

// 背景图片
// var bg = ref("url('https://t.alcy.cc/fj')")
var bg = ref("url('')")

// 时间更新
setInterval(() => {
  var date = new Date()
  time.value.h = date.getHours()
  time.value.m = date.getMinutes()
  time.value.s = date.getSeconds()
}, 1000)
</script>

<template>
  <div class="underlay bg" :style="{ '--bg-url': bg }">
    <circle-progress id="hour" :value="time.h" :valueMax="24" />
    <circle-progress id="minute" :value="time.m" :valueMax="60" />
    <circle-progress id="second" :value="time.s" :valueMax="60" />
  </div>
  <barrage class="underlay" />
</template>

<style scoped>
.underlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.bg {
  --bg-url: '';
  background-color: #000030;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  background-image: var(--bg-url);
  z-index: -9999;
}

barrage {
  z-index: 0;
}

circle-progress {
  z-index: 1;
}
</style>
