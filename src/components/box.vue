<script setup lang="ts">
import { ref } from 'vue'

const props = withDefaults(
  defineProps<{
    title?: string
    id?: string | number
    use_expand?: boolean
    use_hide?: boolean
    content_rid_padding_top?: boolean
  }>(),
  {
    title: '无标题',
    id: '',
    use_expand: false,
    use_hide: true,
    content_rid_padding_top: false,
  },
)

const emit = defineEmits<{
  (e: 'close', id: string | number): void
  (e: 'expand', id: string | number): void
  (e: 'click', id: string | number): void
}>()

var alive = ref(true)
var this_style = ref({})
var is_hiding = ref(false)

var read_to_close = false

function after_animation() {
  if (read_to_close) {
    before_close()
  }
}

function before_expand() {
  emit('expand', props.id)
}

function before_close() {
  alive.value = false
  emit('close', props.id)
}

function hide() {
  is_hiding.value = !is_hiding.value
}

function box_close() {
  this_style.value = { animation: 'to-close .3s ease-in' }
  read_to_close = true
}

// TODO: 不同主题配色
</script>

<script lang="ts"></script>

<template>
  <div
    class="rt-box"
    @animationend="after_animation()"
    @click="emit('click', props.id)"
    :style="this_style"
  >
    <div class="box-top-bar">
      <span class="box-title">
        {{ title }}
      </span>
      <span class="box-blank-area"></span>
      <span class="box-buttons">
        <span v-if="use_expand" class="box-button box-expand" @click="before_expand()"></span>
        <span v-if="use_hide" class="box-button box-hide" @click="hide()"></span>
        <span class="box-button box-close" @click="box_close()"></span>
      </span>
    </div>
    <div
      class="box-content"
      v-if="!is_hiding"
      :class="['box-content', content_rid_padding_top ? 'rid-padding-top' : '']"
    >
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.rid-padding-top {
  padding-top: 0px;
}
</style>

<style>
.rt-box {
  --border-radius: 8px;
  --topbar-bgcolor: #01c3;
  --topbar-bgcolor-hover: #0cf;
  --content-bgcolor: #fff6;
  --content-bgcolor-hover: #fff;
  --font-name: 'JetBrains Mono';
}

.rt-box {
  backdrop-filter: blur(10px);
  border-radius: var(--border-radius);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-size: 14px;
  user-select: none;
  width: 350px;
  margin: 5px auto;
  transition: all 0.4s;
  font-family: var(--font-name);

  animation: to-join 1s ease-out;
}

.rt-box:hover {
  box-shadow: 0px 0px 14px #666a;
}

.rt-box:hover .box-top-bar {
  background-color: var(--topbar-bgcolor-hover);
}

.rt-box:hover .box-content {
  background-color: var(--content-bgcolor-hover);
}

.rt-box:hover .box-close {
  background-color: #ffa500;
}

.rt-box:hover .box-expand {
  background-color: #0fa;
}

.rt-box:hover .box-hide {
  background-color: #0ff;
}

.box-top-bar {
  background-color: var(--topbar-bgcolor);
  display: flex;
  font-weight: bold;
}

.box-title {
  color: white;
}

.box-blank-area {
  flex-grow: 1;
  min-width: 0px;
}

.box-buttons {
  margin-left: auto;
  color: white;
  display: flex;
}

.box-button {
  margin-left: 5px;
  margin-right: 0px;
  border-radius: 50%;
  background-color: #0ff9;
  width: 12px;
  height: 12px;
  cursor: pointer;
  transition: all 0.3s;
}

.box-top-bar,
.box-content {
  padding: 9px;
  transition: all 0.2s;
}

.box-content {
  background-color: var(--content-bgcolor);
}

.box-expand {
  background-color: #0fa6;
}

.box-hide {
  background-color: #0ff6;
}

.box-close {
  background-color: #ffa50066;
}

@keyframes to-close {
  from {
    opacity: 100%;
    transform: initial;
  }
  to {
    opacity: 0%;
    transform: translateX(100%);
  }
}

@keyframes to-join {
  from {
    opacity: 0%;
    transform: translateX(100%);
  }
  to {
    opacity: 100%;
    transform: initial;
  }
}
</style>
