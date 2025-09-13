<script setup lang="ts">
import { ref, reactive } from 'vue'
import Box from './box.vue'
import Timetable from './Timetable.vue'
import weather from './weather.vue'

class Msg {
  title: string = '空标题'
  text: string = '请输入内容'
  constructor(title: string = '空标题', text: string = '请输入内容') {
    this.title = title
    this.text = text
  }
}

var s1 = ref(true)
var s2 = ref(true)

var msg_list = reactive<Array<Msg>>([])

function switcher(id: String | undefined) {
  if (id == 's1') s1.value = false
  else s2.value = false
}

function append(title: string, text: string) {
  msg_list.push(new Msg(title, text))
}

setTimeout(() => {
  append('每日祝福', '今天也是好日子哇~~~')
}, 2000)
</script>

<template>
  <div class="components-container">
    <weather @report="append" />
    <Timetable />
    <Box v-for="msg in msg_list" :title="msg.title">
      {{ msg.text }}
    </Box>
  </div>
</template>

<style scoped>
.components-container {
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  padding: 4px 8px;
  box-shadow: 0px 0px 0px #666a;
  transition: all 0.3s;
  overflow: hidden;
}
</style>
