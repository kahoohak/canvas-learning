<template>
  <div>
    <el-space>
      <el-button @click="changeType('pen')">画笔</el-button>
      <el-button @click="changeType('rect')">正方形</el-button>
      <el-button @click="changeType('arc')">圆形</el-button>
      <span>颜色：</span>
      <el-color-picker v-model="color"></el-color-picker>
      <el-button @click="clear">清空</el-button>
      <el-button @click="save">保存</el-button>
    </el-space>
    <canvas
      id="canvas"
      width="800"
      height="400"
      @mousedown="mousedown"
      @mousemove="mousemove"
      @mouseup="mouseup"
    >
    </canvas>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';

// 选择区域
const type = ref<string>('pen')
const changeType = (val: string): string => type.value = val

const color = ref<string>('#000')

const clear = () => {
  imageData.value = null
  ctx.value?.clearRect(0, 0, 800, 400)
}
const save = () => {
  const url = canvasDom.value!.toDataURL()
  const a = document.createElement('a')
  a.download = 'kaho'
  a.href = url
  document.body.appendChild(a)
  a.click()
  document.body.removeChild(a)
}

// canvas
const canvasDom = ref<HTMLCanvasElement | null>(null)
const ctx = ref<CanvasRenderingContext2D | null>(null)
const isDraw = ref<boolean>(false)
const beginX = ref(0)
const beginY = ref(0)
const imageData = ref<ImageData | null>(null)

const drawPen = (x: number, y: number) => {
  if(!ctx.value) return
  ctx.value.beginPath()
  ctx.value.arc(x, y, 5, 0, 2 * Math.PI)
  ctx.value.fillStyle = color.value
  ctx.value.fill()
  ctx.value.closePath()
}
const drawRect = (x: number, y: number) => {
  if(!ctx.value) return
  ctx.value.clearRect(0, 0, 800, 400)
  if(imageData.value) ctx.value.putImageData(imageData.value, 0, 0, 0, 0, 800, 400)
  ctx.value.beginPath()
  ctx.value.strokeStyle = color.value
  ctx.value.rect(beginX.value, beginY.value, x - beginX.value, y - beginY.value)
  ctx.value.stroke()
  ctx.value.closePath()
}
const drawArc = (x: number, y: number) => {
  if(!ctx.value) return
  ctx.value.clearRect(0, 0, 800, 400)
  if(imageData.value) ctx.value.putImageData(imageData.value, 0, 0, 0, 0, 800, 400)
  ctx.value.beginPath()
  ctx.value.strokeStyle = color.value
  ctx.value.arc(
    beginX.value, 
    beginY.value, 
    Math.round(
      Math.sqrt(
        (x - beginX.value) * (x - beginX.value) + (y - beginY.value) * (y - beginY.value)
      )
    ), 
    0, 2 * Math.PI
  )
  ctx.value.stroke()
  ctx.value.closePath()
}

const mousedown = (e) => {
  isDraw.value = true
  beginX.value = e.pageX - canvasDom.value!.offsetLeft
  beginY.value = e.pageY - canvasDom.value!.offsetTop
}
const mousemove = (e: any) => {
  if(!isDraw.value) return
  const x = e.pageX - canvasDom.value!.offsetLeft
  const y = e.pageY - canvasDom.value!.offsetTop
  if(type.value === 'pen') drawPen(x, y)
  if(type.value === 'rect') drawRect(x, y)
  if(type.value === 'arc') drawArc(x, y)
}
const mouseup = () => {
  if(!ctx.value) return
  isDraw.value = false
  imageData.value = ctx.value.getImageData(0, 0, 800, 400)
}

// 初始化
onMounted(() => {
  canvasDom.value = document.getElementById('canvas') as HTMLCanvasElement
  ctx.value = canvasDom.value.getContext('2d') as CanvasRenderingContext2D
})
</script>

<style scoped>
#canvas {
  border: 1px solid black;
  margin-top: 20px;
}
</style>
