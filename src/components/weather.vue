<script setup lang="ts">
import { ref, reactive } from 'vue'
import box from './box.vue'
import weather_key from '../private/weather_key.json'

const emit = defineEmits<{ (e: 'report', title: string, text: string): void }>()

const select = 'tianqiapi'

const wind_map = {
  北风: '0deg',
  东北风: '45deg',
  东风: '90deg',
  东南风: '135deg',
  南风: '180deg',
  西北风: '-45deg',
  西风: '-90deg',
  西南风: '-135deg',
}

var temperature = ref(99)
var city_name = ref('北京 昌平')
var temperature_min = ref(99)
var temperature_max = ref(99)
var air_condition = ref('')
var weather = ref('晴')
var wind_rotate = ref({ rotate: '90deg' })

var k_data = weather_key[select]
var wther_data = {}

/*if (select == 'gaode') {
  let url = k_data['url'].replace('{key}', k_data['key']).replace('{city}', k_data['city_code'])

  fetch(url)
    .then((response) => response.json())
    .then((data) => {
      console.log(data)
      if (data.status != 1) return
      let wther_data = data.lives['0']
      city_name.value = wther_data['province'] + ' ' + wther_data['city']
      temperature.value = wther_data['temperature']
      weather.value = wther_data['weather']
    })
} else */
if (select == 'tianqiapi') {
  let url = k_data['url']
    .replace('{appid}', k_data['appid'])
    .replace('{appsecret}', k_data['appsecret'])
  console.log(url)
  fetch(url)
    .then((response) => response.json())
    .then((data) => {
      wther_data = data
      city_name.value = data.city
      weather.value = data['wea']
      temperature.value = data['tem'].split('.')[0]
      temperature_max.value = data['tem1']
      temperature_min.value = data['tem2']
      for (let i = 0; i < data['alarm'].length; i++) {
        console.log(data['alarm'][i]['alarm_title'])
        emit('report', data['alarm'][i]['alarm_title'], data['alarm'][i]['alarm_content'])
      }
    })
}

function air_condition_report(id: string | number) {
  emit('report', '空气质量：' + wther_data['air_level'], wther_data['air_tips'])
}
</script>

<template>
  <box
    title="天气"
    :content_rid_padding_top="true"
    :id="weather"
    @click="air_condition_report"
    style="--content-bgcolor: #01c3; --content-bgcolor-hover: #0cf; color: white"
  >
    <div class="wther-content">
      <div class="temperature">{{ temperature }}℃</div>
      <div class="wther-sep"></div>
      <div class="wther-text">
        <span class="wther-city">{{ city_name }} {{ weather }}</span>
        <span class="wther-area"></span>
        <span class="wther-details">{{ temperature_min }}℃-{{ temperature_max }}℃</span>
      </div>
      <div class="wther-sep2"></div>
      <div class="wind" :style="{ rotate: wind_map[wther_data['win']] }">
        <svg
          t="1757779516829"
          class="icon"
          viewBox="0 0 1024 1024"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          p-id="1642"
          xmlns:xlink="http://www.w3.org/1999/xlink"
        >
          <path
            d="M798.72921799 859.61408804a29.10541778 29.10541778 0 0 1-14.55270923-3.92013595L526.51170433 706.47411444a50.58885393 50.58885393 0 0 0-25.1398048 0L239.69600437 855.78490687a29.10541778 29.10541778 0 0 1-14.42537242 3.82918116 37.07302583 37.07302583 0 0 1-32.08872304-18.318222A38.34638789 38.34638789 0 0 1 191.83578343 804.70489908L477.93294294 185.7145253q0.38200841-0.83678092 0.82768522-1.6462748a37.29131606 37.29131606 0 0 1 65.71457612 0q0.42748614 0.80039888 0.80949456 1.61898844L832.15497085 804.70489908a38.34638789 38.34638789 0 0 1-1.33703047 36.60915765 37.07302583 37.07302583 0 0 1-32.08872238 18.3000313zM511.64065509 251.52915119L271.80291811 770.43326964l203.7379232-116.30342936a29.10541778 29.10541778 0 0 1 5.74832028-2.50124708 108.59958928 108.59958928 0 0 1 65.48718954 0.13643183 29.10541778 29.10541778 0 0 1 5.79379734 2.55581911l199.19020155 115.34840799z"
            p-id="1643"
          ></path>
          <path
            d="M513.05954397 569.17840214m-58.9202797 0a58.92027968 58.92027968 0 1 0 117.84055938 0 58.92027968 58.92027968 0 1 0-117.84055938 0Z"
            p-id="1644"
          ></path>
        </svg>
      </div>
    </div>
  </box>
</template>

<style scoped>
.wther-content {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
}

.temperature {
  font-size: 32px;
}

.wther-sep {
  width: 16px;
}

.wther-text {
  display: flex;
  flex-direction: column;
}

.wther-area {
  flex-grow: 1;
  min-height: 6px;
}

.wther-details {
  display: flex;
  margin-top: auto;
  text-wrap: no-wrap;
}

.wther-sep2 {
}

.wind {
  border-radius: 50%;
  margin-left: auto;
  margin-right: 0px;
  width: 42px;
  height: 42px;
  transition: all 1s;
}

.wind > svg {
  fill: white;
}
</style>
