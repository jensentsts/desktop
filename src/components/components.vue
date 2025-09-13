<script setup lang="ts">
import { ref } from 'vue'
import Box from './box.vue'
import Timetable from './Timetable.vue'
import weather from './weather.vue'

var s1 = ref(true)
var s2 = ref(true)

function switcher(id: String | undefined) {
  if (id == 's1') s1.value = false
  else s2.value = false
}
</script>

<template>
  <div class="components-container">
    <weather />
    <Timetable />
    <Box title="消息">Enjoy every moment<br />in every day!</Box>
  </div>
</template>

<style scoped>
.components-container {
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  padding: 4px 16px;
  box-shadow: 0px 0px 0px #666a;
  transition: all 0.3s;
  overflow: hidden;
}
</style>
