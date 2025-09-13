<script setup lang="ts">
import { ref } from 'vue'
import box from './box.vue'
import weather_key from '../private/weather_key.json'

const emit = defineEmits<{ (e: 'report', title: string, text: string): void }>()

const select = 'tianqiapi'

var temperature = ref(99)
var city_name = ref('北京 昌平')
var temperature_min = ref(99)
var temperature_max = ref(99)
var air_condition = ref('')
var weather = ref('晴')

var k_data = weather_key[select]

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
      console.log(data)
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
</script>

<template>
  <box
    title="天气"
    :content_rid_padding_top="true"
    style="--content-bgcolor: #01c3; --content-bgcolor-hover: #0cf; color: white"
  >
    <div class="wther-content">
      <div class="temperature">{{ temperature }}°</div>
      <span class="wther-text">
        <span class="wther-city">{{ city_name }} {{ weather }}</span>
        <span class="wther-area"></span>
        <span class="wther-details">{{ temperature_min }}°-{{ temperature_max }}°</span>
      </span>
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

.wther-text {
  display: flex;
  flex-direction: column;
}

.wther-city {
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
</style>
