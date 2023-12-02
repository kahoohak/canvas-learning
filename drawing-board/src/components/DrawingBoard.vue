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

const clear = () => {}
const save = () => {}

// canvas
const canvasDom = ref<HTMLCanvasElement | null>(null)
const ctx = ref<CanvasRenderingContext2D | null>(null)
const isDraw = ref<boolean>(false)

const drawPen = () => {}
const drawRect = () => {}
const drawArc = () => {}

const mousedown = () => isDraw.value = true
const mousemove = () => {
  if(!isDraw.value) return
  if(type.value === 'pen') drawPen()
  if(type.value === 'rect') drawRect()
  if(type.value === 'arc') drawArc()
}
const mouseup = () => isDraw.value = false

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
