<script setup>
defineProps(['value', 'valueMax'])
</script>

<template>
  <div class="progress">
    <div class="progress-wrapper" :style="{ '--value': value, '--value-max': valueMax }">
      <svg class="progress-circle r-n90">
        <circle stroke="var(--inactive-color)" />
        <circle stroke="var(--color)" class="progress-value" />
      </svg>
    </div>
  </div>
</template>

<style scoped>
/* 旋转-90deg */
.r-n90 {
  transform: rotate(-90deg);
}

.progress {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 32px;
}
</style>

<style>
.progress-wrapper {
  --value: 0;
  --value-max: 100;
  --text-color: white;
  --font-size: 36px;
  --before-font-opacity: 0.15;
  --before-font-size-times: 2.4;
  --after-text-shadow-size: 20px;
}

.progress-circle {
  --size: 240px;
  --border-width: 4px;
  /*--color: #5050B3;*/
  --color: #66ccff;
  /*--inactive-color: #000066;*/
  --inactive-color: #00006600;
  --shadow-size: 16px;
}

.progress-wrapper,
.progress-circle {
  width: var(--size);
  height: var(--size);
  border-radius: 50%;
}

.progress-wrapper::before,
.progress-wrapper::after {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  white-space: nowrap;
  color: var(--text-color);
  z-index: 1000;
  -webkit-text-stroke: 1px rgba(0, 0, 0, 0.5);
}

/* value-max打底 */
.progress-wrapper::before {
  font-size: calc(var(--font-size) * var(--before-font-size-times));
  counter-reset: progress var(--value-max);
  content: counter(progress);
  opacity: var(--before-font-opacity);
}

/* value顶部显示 */
.progress-wrapper::after {
  font-size: var(--font-size);
  counter-reset: progress var(--value);
  content: counter(progress);
  text-shadow: 0 0 var(--after-text-shadow-size) var(--text-color);
}

.progress-circle {
  backdrop-filter: blur(20px);
}

.progress-circle > circle {
  cx: calc(var(--size) / 2);
  cy: calc(var(--size) / 2);
  r: calc((var(--size) - var(--border-width)) / 2);
  fill: none;
  stroke-width: var(--border-width);
  stroke-linecap: round;
  transition: all 0.4s;
}

.progress-circle > circle:nth-child(1) {
  filter: drop-shadow(0 0 var(--shadow-size) var(--inactive-color));
}

/* 弧长计算 */
.progress-circle > circle:nth-child(2) {
  stroke-dasharray:
    calc(2 * 3.1415 * (var(--size) - var(--border-width)) / 2 * (var(--value) / var(--value-max))),
    1000;
}

.progress-value {
  opacity: var(--value);
}
</style>
