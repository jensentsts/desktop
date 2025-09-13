<script setup lang="ts">
import box from './box.vue'

function expand_timetable(id: String) {
  alert(id)
}
</script>

<template>
  <box title="课程表" :use_expand="true" id="课程表"> 暂无课表 </box>
</template>

<style></style>
