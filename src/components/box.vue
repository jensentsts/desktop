<script setup lang="ts">
import { ref } from 'vue'

const props = withDefaults(
  defineProps<{
    title?: string
    id?: string | number
    use_expand: boolean
    use_hide: boolean
  }>(),
  {
    title: '无标题',
    id: '',
    use_expand: false,
    use_hide: true,
  },
)

const emit = defineEmits(['close:id', 'expand:id'])

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
  this_style.value = { animation: 'to-close .3s' }
  read_to_close = true
}

// TODO: 不同配色
</script>

<script lang="ts"></script>

<template>
  <div v-if="alive" class="rt-box" @animationend="after_animation()" :style="this_style">
    <div class="top-bar">
      <span class="title">
        {{ title }}
      </span>
      <span class="blank-area"></span>
      <span class="buttons">
        <span v-if="use_expand" class="button expand" @click="before_expand()"></span>
        <span v-if="use_hide" class="button hide" @click="hide()"></span>
        <span class="button close" @click="box_close()"></span>
      </span>
    </div>
    <div class="content" v-if="!is_hiding">
      <slot></slot>
    </div>
  </div>
</template>

<style>
.rt-box {
  --color: '';
  --border-radius: 8px;
}

.rt-box {
  backdrop-filter: blur(10px);
  border-radius: var(--border-radius);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-size: 12px;
  user-select: none;
  width: 280px;
  margin: 5px auto;
  transition: all 0.4s;

  animation: to-join 1s ease-out;
}

.rt-box:hover {
  box-shadow: 0px 0px 14px #666a;
}

.rt-box:hover .top-bar {
  background-color: #0cff;
}

.rt-box:hover .content {
  background-color: #fff;
}

.rt-box:hover .close {
  background-color: #ffa500;
}

.rt-box:hover .expand {
  background-color: #0fa;
}

.rt-box:hover .hide {
  background-color: #0ff;
}

.top-bar {
  background-color: #01c3;
  border-bottom: none;
  display: flex;
}

.title {
  color: white;
}

.blank-area {
  flex-grow: 1;
  min-width: 0px;
}

.buttons {
  margin-left: auto;
  color: white;
  display: flex;
}

.button {
  margin-left: 5px;
  margin-right: 0px;
  border-radius: 50%;
  background-color: #0ff9;
  width: 12px;
  height: 12px;
  cursor: pointer;
  transition: all 0.3s;
}

.top-bar,
.content {
  padding: 9px;
  transition: all 0.2s;
}

.content {
  background-color: #fff6;
}

.expand {
  background-color: #0fa6;
}

.hide {
  background-color: #0ff6;
}

.close {
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
