<script setup lang="ts">
import { reactive, ref } from 'vue'
import CircleProgress from './circle_progress.vue'

var h = ref(0)
var m = ref(0)
var s = ref(0)

var bg = ref({
  '--bg-url': "url('https://t.alcy.cc/fj')",
})

setInterval(() => {
  var date = new Date()
  h.value = date.getHours()
  m.value = date.getMinutes()
  s.value = date.getSeconds()
}, 1000)
</script>

<template>
  <div class="underlay" :style="bg">
    <circle-progress id="hour" :value="h" :valueMax="24" />
    <circle-progress id="minute" :value="m" :valueMax="60" />
    <circle-progress id="second" :value="s" :valueMax="60" />
  </div>
</template>

<style scoped>
.underlay {
  --bg-url: '';

  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #000030;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  background-image: var(--bg-url);
  overflow: hidden;
  z-index: -9999;
}
</style>
