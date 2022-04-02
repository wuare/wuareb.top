<template>
  <div>
    <canvas ref="element" class="canvas" :style="`width:${WIDTH/2};height:${HEIGHT/2}px;`" />
    <div class="content-wrapper">
      <div class="content">
        <div class="download">
          <a class="download-button download-button_white" href="https://blog.wuareb.top">文章</a>
          <a class="download-button download-button_white" href="https://demo.wuareb.top">关于我</a>
        </div>
      </div>
    </div>
    <footer class="desktop-footer">
      <p class="law-files">
        <a href="https://beian.miit.gov.cn">京ICP备2022003889号</a>
      </p>
    </footer>
  </div>
</template>

<script lang="ts" setup>
const element = $ref<HTMLCanvasElement>()
const ctx = $computed(() => element!.getContext('2d')!)
const WIDTH = document.body.clientWidth * 2
const HEIGHT = document.body.clientHeight * 2
interface Branch {
  start: Point
  length: number
  angle: number
}

interface Point {
  x: number
  y: number
}

const startPointArray: Array<Point> = [
  { x: 0, y: HEIGHT * Math.random() },
  { x: WIDTH, y: HEIGHT * Math.random() },
  { x: WIDTH * Math.random(), y: 0 },
  { x: WIDTH * Math.random(), y: HEIGHT },
]

onMounted(() => {
  element.width = WIDTH
  element.height = HEIGHT
  init()
})

function init() {
  startPointArray.forEach((item) => {
    step(getStartPoint(item))
  })
}

function getStartPoint({ x, y }: Point): Branch {
  let _angel = Math.PI

  if (x === 0) {
    switch (y) {
      case 0:
        _angel = _angel / (3 + Math.random() * 3)
        break
      case HEIGHT:
        _angel = -_angel / (3 + Math.random() * 3)
        break
      default:
        _angel = 0
        break
    }
  }
  else if (x === WIDTH) {
    switch (y) {
      case 0:
        _angel = _angel / (1 + Math.random())
        break
      case HEIGHT:
        _angel = -_angel / (1 + Math.random())
        break
      default:
        break
    }
  }
  else {
    switch (y) {
      case 0:
        _angel /= 2
        break
      case HEIGHT:
        _angel /= -2
        break
      default:
        break
    }
  }

  return {
    start: { x, y },
    length: Math.random() * 6,
    angle: _angel,
  }
}

const pendingTask: Array<Function> = []
function step(b: Branch, depth = 0) {
  drawBranch(b)
  const end = getEndPoint(b)

  if (depth < 5 || Math.random() < 0.5) {
    pendingTask.push(() => {
      step({
        start: end,
        length: Math.random() * 10 + Math.random() * 10 - 5,
        angle: b.angle + Math.random() * 0.3,
      }, depth + 1)
    })
  }
  if (depth < 5 || Math.random() < 0.5) {
    pendingTask.push(() => {
      step({
        start: end,
        length: Math.random() * 10 + Math.random() * 10 - 5,
        angle: b.angle - Math.random() * 0.3,
      }, depth + 1)
    })
  }
}

function frame() {
  const tasks = [...pendingTask]
  pendingTask.length = 0
  tasks.forEach(fn => fn())
}

let frameCount = 0
function loopFrame() {
  requestAnimationFrame(() => {
    frameCount += 2
    if (frameCount % 6 === 0)
      frame()

    loopFrame()
  })
}
loopFrame()

function getEndPoint(b: Branch): Point {
  const { start, length, angle } = b
  return {
    x: start.x + length * Math.cos(angle),
    y: start.y + length * Math.sin(angle),
  }
}

function drawBranch(b: Branch) {
  lineTo(b.start, getEndPoint(b))
}

function lineTo(p1: Point, p2: Point) {
  ctx.strokeStyle = 'rgba(107,114,128,.3)'
  ctx.beginPath()
  ctx.moveTo(p1.x + 0.1, p1.y + 0.1)
  ctx.lineTo(p2.x + 0.1, p2.y + 0.1)
  ctx.stroke()
}

</script>

<style scoped>
* {
  box-sizing: content-box;
}
html {
  height: 100%;
}
body {
  margin: 0;
  height: 100%;
  background: #fff;
  font-family: -apple-system-font, BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB, Microsoft YaHei UI, Microsoft YaHei, Arial, sans-serif;
  line-height: 1.4;
}
body, h1, h2, h3, li, ul {
  padding: 0;
  margin: 0
}
li, ul {
  list-style: none;
  margin: 0
}
p {
  margin: .33em 0
}
h2 {
  font-size: 14px;
  font-weight: 700
}
img {
  border: 0
}
a {
  text-decoration: none;
  cursor: pointer;
  outline: none
}
.canvas {
  position: fixed;
  top: 0;
  left: 0;
  z-index: -1;
}
.content-wrapper {
    text-align: center;
    display: flex;
    align-items: center;
    height: calc(70vh - 80px);
    min-height: 375px;
    padding: 0 36px;
    animation: a 1s;
    max-width: 400px;
    margin: 0 auto
}
.content {
    flex: 1;
    max-width: 100%
}
@media (min-width: 1440px) {
  .content-wrapper {
      height: 90vh
  }
}

@media (min-width: 1023px) and (min-height: 900px) {
  .content {
      transform: scale(1.15)
  }

  .content-wrapper {
      min-height: 425px
  }
}

@media (min-width: 1439px) and (min-height: 850px) {
  .content {
      transform: scale(1.2)
  }
}

@media (min-width: 1439px) and (min-height: 900px) {
  .content {
      transform: scale(1.3)
  }

  .content-wrapper {
      height: 90vh;
      min-height: 460px
  }
}

@media (max-width: 768px) {
  .content-wrapper {
      min-height: 425px;
      height: calc(80vh - 100px)
  }
}

@media (max-width: 450px) {
  .content-wrapper {
      min-height: 350px;
      height: calc(70vh - 100px)
  }
}
@media (min-width: 768px) and (max-width: 1440px) and (min-height: 950px) {
  .content-wrapper {
      height: calc(76vh - 80px)
  }
}
.download {
  display: flex;
  padding: 0 16px;
  justify-content: center
}

.download-button {
  height: 48px;
  flex: 1;
  margin: 56px auto 0;
  background: #07c160;
  border-radius: 4px;
  font-size: 12px;
  color: #fff;
  display: inline-flex;
  align-items: center;
  box-sizing: border-box;
  padding: 0 10px
}

.download-button:hover {
  background: #06b259
}

.download-button:active {
  background: #06a351
}

.download-button + .download-button {
  margin-left: 24px
}

.download-button_white {
  width: 1;
  background-color: #f2f2f2;
  color: #000
}

.download-button_white:hover {
  background: #eaeaea
}

.download-button_white:active {
  background: #e3e3e3
}
footer {
  position: absolute;
  bottom: 8px;
  -webkit-text-size-adjust: none;
  font-size: 10px;
  text-align: center;
  left: 0;
  right: 0
}

footer, footer a {
  /* color: rgba(0, 0, 0, .5); */
  color: #000;
}

footer a {
  position: relative;
  left: -3px
}

footer a:hover:after {
  content: "";
  color: rgba(0, 0, 0, .5);
  position: absolute;
  bottom: -2px;
  left: 5px;
  right: 5px;
  height: 1px;
  border-bottom: 1px solid rgba(0, 0, 0, .5)
}

footer .law-files {
  margin-bottom: 4px;
}

.desktop-footer {
  text-align: left;
  margin-left: 48px
}

@media (min-width: 440px) {
  .desktop-footer {
      display: block
  }
}
@keyframes a {
  0% {
      transform: translateY(100px);
      opacity: 0
  }

  to {
      transform: translateY(0);
      opacity: 1
  }
}
</style>
