<script setup lang="ts">
import { reactive, ref } from 'vue'

class Barrage {
  text = ''
  onShow = false
  constructor(text = '', onShow = false) {
    this.text = text
    this.onShow = onShow
  }
}

class BarrageTrack {
  id = ''
  barrages = reactive<Array<number>>([])
  barrage_waiting = 0
  constructor(id = '') {
    this.id = id
    this.barrages = reactive<Array<number>>([])
    this.barrage_waiting = 0
  }

  sendBarrage(barrageIndex: number) {
    this.barrage_waiting--
    if (barrageList[barrageIndex].onShow) {
      return
    }
    this.barrages.push(barrageIndex)
    barrageList[barrageIndex].onShow = true
  }

  removeBarrage(barrageIndex: number) {
    var index = barrageIndex
    this.barrages.splice(index, 1)
    barrageList[barrageIndex].onShow = false
  }
}

// TODO:实现弹幕效果
// TODO:修正typescript错误

var barrageList: Array<Barrage> = []
var barrageTracks = reactive<Array<BarrageTrack>>([])
var barrageAmount = barrageList.length // 弹幕总数
var onTrackAmount = 0 // 当前正在播放的弹幕数

/* 获取一条弹幕 */
async function fetchBarrage() {
  return fetch('https://v1.hitokoto.cn')
    .then((response) => response.json())
    .then((data) => data.hitokoto)
}

/* 初始化弹幕列表 */
async function loadBarrageList() {
  for (let i = 0; i < 10; i++) {
    barrageList.push(new Barrage('这是第' + i + '次喵喵', false))
    barrageList.push(new Barrage('这是第' + i + '次汪汪', false))
    barrageList.push(new Barrage('这是第' + i + '次咕咕', false))
    barrageList.push(new Barrage('这是第' + i + '次嘎嘎', false))
  }
  for (let i = 0; i < 0; i++) {
    fetchBarrage().then((text) => {
      barrageList.push(new Barrage(text, false))
    })
  }
  console.log('加载完毕')
}

loadBarrageList().then(() => {
  barrageAmount = barrageList.length
  addSomeBarrage()
  setInterval(() => {
    setTimeout(
      () => {
        addSomeBarrage()
      },
      5000 * Math.random() + 200,
    )
  }, 5000)
})

function findRandomTrack() {
  if (barrageTracks.length == 0) {
    return null
  }
  var trackIndex = Math.floor(Math.random() * barrageTracks.length)
  return barrageTracks[trackIndex]
}

function createTrack() {
  if (barrageTracks.length >= barrageAmount) {
    return findRandomTrack()
  }
  var trackId = 'track-' + Math.floor(Math.random() * 1000000)
  var track = new BarrageTrack(trackId)
  barrageTracks.push(reactive(track))
  return track
}

function findTrack(trackId: string): BarrageTrack | undefined {
  return barrageTracks.find((track) => track.id === trackId)
}

function removeTrack(trackId: string) {
  var trackIndex = barrageTracks.findIndex((track) => track.id === trackId)
  var track = findTrack(trackId)
  if (track == undefined) {
    return
  }
  if (track.barrages.length > 0 && track.barrage_waiting > 0) {
    return
  }
  if (trackIndex > -1) {
    barrageTracks.splice(trackIndex, 1)
  }
}

function createBarrage(barrageId: number) {
  if (onTrackAmount >= barrageAmount) return
  var track: BarrageTrack | null = null
  if (barrageTracks.length == 0 || Math.random() <= 0.6) {
    track = createTrack()
  } else {
    // 随机选择一个已有的轨道
    track = findRandomTrack()
  }
  // 将弹幕加入轨道
  if (track == null) {
    return
  }
  track.barrage_waiting++
  onTrackAmount++
  // 延时加入，避免弹幕重叠
  setTimeout(
    () => {
      if (track == null) return
      track.sendBarrage(barrageId)
    },
    3000 * Math.random() + 100,
  )
}

/* 将一些弹幕重新加入轨道 */
function addSomeBarrage() {
  // 生成随机的数量
  var sendAmount = Math.floor(Math.random() * (barrageAmount - onTrackAmount)) + 1
  // 不多于40%的弹幕
  if (sendAmount > Math.floor(barrageAmount * 0.3)) {
    sendAmount = Math.floor(barrageAmount * 0.3)
  }
  for (let i = 0; i < sendAmount; i++) {
    // 抽一条未显示的弹幕
    var barrageId = -1
    for (let j = 0; barrageId == -1; j++) {
      // 防溢出
      if (j >= barrageAmount) {
        j = 0
      }
      if (!barrageList[j].onShow) {
        // 70%的可能性抽中此弹幕
        if (Math.random() >= 0.3) continue
        barrageId = j
        break
      }
    }
    if (barrageId == -1) {
      break
    }
    createBarrage(barrageId)
  }
}

/* 弹幕显示结束之后的处理 */
function animationend(trackId: string, barrageId: number) {
  var track: BarrageTrack | undefined = findTrack(trackId)
  // 将弹幕从trackId的轨道中移除
  if (track == null) return
  track.removeBarrage(barrageId)
  onTrackAmount--
  // 如果轨道中没有弹幕了，就删除这个轨道
  removeTrack(trackId)

  addSomeBarrage()
}

// GC
setInterval(() => {
  barrageTracks.forEach((track) => {
    if (track.barrages.length == 0 && track.barrage_waiting == 0) {
      removeTrack(track.id)
    }
  })
}, 10000)
</script>

<template>
  <div v-for="track in barrageTracks" :key="track.id" class="track">
    <div
      v-for="barrageId in track.barrages"
      :key="barrageId"
      class="barrage"
      @animationend="animationend(track.id, barrageId)"
    >
      {{ barrageList[barrageId].text }}
    </div>
  </div>
</template>

<style scoped>
template {
  /* track应当自动地纵向排列 */
  display: flex;
  flex-direction: column;
}

.track {
  position: relative;
  height: 24px;
  overflow: hidden;
  transition: all 0.3s;
}

.barrage {
  position: absolute;
  white-space: nowrap;
  color: white;
  font-size: 18px;
  /* font-weight: bold; */
  text-shadow: 0px 0px 4px #000000aa;
  animation: move-r2l linear 10s;
  user-select: none;
}

@keyframes move-r2l {
  from {
    left: 100%;
    transform: translateX(0);
  }
  to {
    left: 0;
    transform: translateX(-100%);
  }
}
</style>
