<template>
  <div class="mainPage">
    <div class="row">
      <div class="col-6">
        <video ref="video1" />
        <canvas ref="canvas5" class="overlay"></canvas>
      </div>
      <div class="col-6">
        <button class="btn btn-secondary" @click="setVideo1">Set video 1</button>
        <button class="btn btn-secondary" @click="setVideo6">Set video 2</button>
        <button class="btn btn-secondary" @click="setVideo8">Set video 3</button>
        <button class="btn btn-secondary" @click="setVideo10">Set video 4</button>
        <button class="btn btn-secondary" @click="setVideo11">Set video 5</button>
        <hr>
        <button class="btn btn-primary" @click="play">Play</button>
        <button class="btn btn-danger" @click="pause">Pause</button>
        <hr>
        <button class="btn btn-warning" @click="$refs.video1.playbackRate = 0.2">0.2x</button>
        <button class="btn btn-warning" @click="$refs.video1.playbackRate = 0.5">0.5x</button>
        <button class="btn btn-warning" @click="$refs.video1.playbackRate = 1">1x</button>
        <button class="btn btn-warning" @click="$refs.video1.playbackRate = 1.5">1.5x</button>
        <button class="btn btn-warning" @click="$refs.video1.playbackRate = 2">2x</button>
        <hr>
        <div class="form-group">
          <label for="range">Signed difference threshold: {{diffT}}</label>
          <input type="range" min="1" max="255" class="form-control-range" id="range" v-model="diffT">
        </div>
        <div class="form-group">
          <label for="range">Magnitude threshold: {{magnT}}</label>
          <input type="range" min="1" max="20000" class="form-control-range" id="range" v-model="magnT">
        </div>
        <div class="form-group">
          <label for="range">Block combine threshold: {{blockT}}</label>
          <input type="range" min="1" :max="maxBlockT" class="form-control-range" id="range" v-model="blockT">
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-6">
        <canvas ref="canvas1" width="300" height="300"></canvas>
      </div>
      <div class="col-6">
        <canvas ref="canvas2" width="300" height="300"></canvas>
      </div>
      <div class="col-6">
        <canvas ref="canvas3" width="300" height="300"></canvas>
      </div>
      <div class="col-6">
        <canvas ref="canvas4" width="300" height="300"></canvas>
      </div>
    </div>
  </div>
</template>

<script>

const Sx = (m1, m2) => m1[0] * m2[0] + m1[1] * m2[1]
const Sy = (m1, m2) => m1[2] * m2[2] + m1[3] * m2[3]

let ctx1 = null
let ctx2 = null
let ctx3 = null
let ctx4 = null
let ctx5 = null
let prevFrame = null
console.log(ctx3, ctx4)

export default {
  name: 'mainPage',
  data () {
    return {
      interval: null,
      speed: 1000,
      blocksX: 10,
      blocksY: 10,
      diffT: 30,
      magnT: 100,
      blockT: 100,
      maxBlockT: 10
    }
  },
  mounted () {
    const canvas1 = this.$refs.canvas1
    const canvas2 = this.$refs.canvas2
    const canvas3 = this.$refs.canvas3
    const canvas4 = this.$refs.canvas4
    const canvas5 = this.$refs.canvas5
    ctx1 = canvas1.getContext('2d')
    ctx2 = canvas2.getContext('2d')
    ctx3 = canvas3.getContext('2d')
    ctx4 = canvas4.getContext('2d')
    ctx5 = canvas5.getContext('2d')
    this.$refs.video1.volume = 0
  },
  methods: {
    setVideo1 () {
      this.speed = 800
      this.blocksX = 30
      this.blocksY = 15
      this.$refs.video1.src = 'public/main.mp4'
    },
    setVideo2 () {
      this.speed = 40
      this.blocksX = 8
      this.blocksY = 30
      this.$refs.video1.src = 'public/main2.mp4'
    },
    setVideo3 () {
      this.speed = 40
      this.blocksX = 8
      this.blocksY = 8
      this.$refs.video1.src = 'public/main3.mp4'
    },
    setVideo4 () {
      this.speed = 40
      this.blocksX = 8
      this.blocksY = 8
      this.$refs.video1.src = 'public/main4.mp4'
    },
    setVideo5 () {
      this.speed = 40
      this.blocksX = 4
      this.blocksY = 4
      this.$refs.video1.src = 'public/main5.mp4'
    },
    setVideo6 () {
      this.speed = 100
      this.blocksX = 10
      this.blocksY = 10
      this.$refs.video1.src = 'public/main6.mp4'
    },
    setVideo7 () {
      this.speed = 100
      this.blocksX = 10
      this.blocksY = 10
      this.$refs.video1.src = 'public/main7.mp4'
    },
    setVideo8 () {
      this.speed = 100
      this.blocksX = 28
      this.blocksY = 12
      this.$refs.video1.src = 'public/main8.mp4'
    },
    setVideo9 () {
      this.speed = 500
      this.blocksX = 18
      this.blocksY = 12
      this.$refs.video1.src = 'public/main9.mp4'
    },
    setVideo10 () {
      this.speed = 50
      this.blocksX = 64
      this.blocksY = 24
      this.$refs.video1.src = 'public/main10.mp4'
    },
    setVideo11 () {
      this.speed = 50
      this.blocksX = 64
      this.blocksY = 24
      this.$refs.video1.src = 'public/main11.mp4'
    },
    play () {
      this.$refs.video1.play()
      if (this.interval) return
      const video = this.$refs.video1
      this.$refs.canvas1.width = video.videoWidth
      this.$refs.canvas1.height = video.videoHeight
      this.$refs.canvas2.width = video.videoWidth
      this.$refs.canvas2.height = video.videoHeight
      this.$refs.canvas3.width = video.videoWidth
      this.$refs.canvas3.height = video.videoHeight
      this.$refs.canvas4.width = video.videoWidth
      this.$refs.canvas4.height = video.videoHeight
      this.$refs.canvas5.width = video.videoWidth
      this.$refs.canvas5.height = video.videoHeight
      const gridX = video.videoWidth / this.blocksX
      const gridY = video.videoHeight / this.blocksY
      ctx5.strokeStyle = '#f61'
      for (let i = 0; i < video.videoWidth; i += gridX) {
        ctx5.beginPath()
        ctx5.moveTo(i, 0)
        ctx5.lineTo(i, video.videoHeight)
        ctx5.stroke()
      }
      for (let i = 0; i < video.videoHeight; i += gridY) {
        ctx5.beginPath()
        ctx5.moveTo(0, i)
        ctx5.lineTo(video.videoWidth, i)
        ctx5.stroke()
      }
      this.interval = setInterval(() => this.motionDetection(), this.speed)
    },
    pause () {
      this.$refs.video1.pause()
      if (!this.interval) return
      clearInterval(this.interval)
      this.interval = null
    },
    motionDetection () {
      const video = this.$refs.video1
      const width = video.videoWidth
      const height = video.videoHeight
      ctx1.drawImage(video, 0, 0)
      const frameNow = ctx1.getImageData(0, 0, width, height)
      const diffFrame = ctx1.getImageData(0, 0, width, height)
      if (!prevFrame) { prevFrame = frameNow; return }

      const EMDgraph = {
        x: new Array(height).fill(0).map(el => new Array(width).fill(0)),
        y: new Array(height).fill(0).map(el => new Array(width).fill(0))
      }
      for (let i = 0; i < frameNow.data.length; i += 4) {
        const x = (i / 4) % width
        const y = Math.trunc((i / 4) / width)
        const intensityNow = parseInt(0.299 * frameNow.data[i] + 0.587 * frameNow.data[i + 1] + 0.114 * frameNow.data[i + 2])
        const intensityPrev = parseInt(0.299 * prevFrame.data[i] + 0.587 * prevFrame.data[i + 1] + 0.114 * prevFrame.data[i + 2])
        const diff = intensityNow - intensityPrev
        if (diff > this.diffT) {
          diffFrame.data[i] = 0
          diffFrame.data[i + 1] = 0
          diffFrame.data[i + 2] = 255
          diffFrame.data[i + 3] = 255
          EMDgraph.x[y][x] = diff
        } else if (diff < -this.diffT) {
          diffFrame.data[i] = 255
          diffFrame.data[i + 1] = 0
          diffFrame.data[i + 2] = 0
          diffFrame.data[i + 3] = 255
          EMDgraph.y[y][x] = Math.abs(diff)
        } else {
          diffFrame.data[i] = 0
          diffFrame.data[i + 1] = 0
          diffFrame.data[i + 2] = 0
          diffFrame.data[i + 3] = 255
        }
      }
      ctx2.putImageData(diffFrame, 0, 0)

      const gridX = video.videoWidth / this.blocksX
      const gridY = video.videoHeight / this.blocksY

      // const CBlocks = new Array(this.blocks).fill(0).map(el => new Array(this.blocks).fill(0))
      ctx3.fillRect(0, 0, width, height)
      ctx4.putImageData(frameNow, 0, 0)

      const cArray = new Array(this.blocksY).fill(0).map(el => new Array(this.blocksX).fill(0))

      for (let y = 0; y < video.videoHeight; y += gridY) {
        for (let x = 0; x < video.videoWidth; x += gridX) {
          const blockX = x / gridX
          const blockY = y / gridY
          // CBlocks[y][x]
          let c = [0, 0, 0, 0] // Cx+ Cx- Cy+ Cy-
          for (let i = 0; i < gridY; i++) {
            const lineX = EMDgraph.x[y + i].slice(x, x + gridX)
            const lineY = EMDgraph.y[y + i].slice(x, x + gridX)
            const findI = lineX.findIndex(el => el !== 0)
            const findJ = lineY.findIndex(el => el !== 0)
            if (findI !== -1 && findJ !== -1 && findI < findJ) {
              const min = Math.min(lineX[findI], lineY[findJ])
              c[0] += Math.abs(findJ - findI) * min
            }
          }
          if (c[0] > 0) {
            ctx3.beginPath()
            ctx3.strokeStyle = '#f00'
            ctx3.moveTo(x + gridX / 2, y + gridY / 2)
            ctx3.lineTo(x + gridX / 2 - c[0] / 500, y + gridY / 2)
            ctx3.stroke()
          }

          for (let i = 0; i < gridY; i++) {
            const lineX = EMDgraph.x[y + i].slice(x, x + gridX)
            const lineY = EMDgraph.y[y + i].slice(x, x + gridX)
            const findI1 = lineX.reverse().findIndex(el => el !== 0)
            lineX.reverse()
            const findI = findI1 === -1 ? -1 : lineX.length - 1 - findI1
            const findJ1 = lineY.reverse().findIndex(el => el !== 0)
            lineY.reverse()
            const findJ = findJ1 === -1 ? -1 : lineY.length - 1 - findJ1
            if (findI !== -1 && findJ !== -1 && findI > findJ) {
              const min = Math.min(lineX[findI], lineY[findJ])
              c[1] += Math.abs(findI - findJ) * min
            }
          }
          if (c[1] > 0) {
            ctx3.beginPath()
            ctx3.strokeStyle = '#0f0'
            ctx3.moveTo(x + gridX / 2, y + gridY / 2)
            ctx3.lineTo(x + gridX / 2 + c[1] / 500, y + gridY / 2)
            ctx3.stroke()
          }

          for (let i = 0; i < gridX; i++) {
            let findI = -1
            let findJ = -1
            for (let j = 0; j < gridY; j++) {
              if (EMDgraph.x[y + j][x + i] > 0) {
                findI = j
                break
              }
            }
            for (let j = 0; j < gridY; j++) {
              if (EMDgraph.y[y + j][x + i] > 0) {
                findJ = j
                break
              }
            }
            if (findI !== -1 && findJ !== -1 && findI > findJ) {
              const min = Math.min(EMDgraph.x[y + findI][x + i], EMDgraph.y[y + findJ][x + i])
              c[2] += Math.abs(findI - findJ) * min
            }
          }
          if (c[2] > 0) {
            ctx3.beginPath()
            ctx3.strokeStyle = '#00f'
            ctx3.moveTo(x + gridX / 2, y + gridY / 2)
            ctx3.lineTo(x + gridX / 2, y + gridY / 2 + c[2] / 500)
            ctx3.stroke()
          }

          for (let i = 0; i < gridX; i++) {
            let findI = -1
            let findJ = -1
            for (let j = gridY - 1; j >= 0; j--) {
              if (EMDgraph.x[y + j][x + i] > 0) {
                findI = j
                break
              }
            }
            for (let j = gridY - 1; j >= 0; j--) {
              if (EMDgraph.y[y + j][x + i] > 0) {
                findJ = j
                break
              }
            }
            if (findI !== -1 && findJ !== -1 && findI < findJ) {
              const min = Math.min(EMDgraph.x[y + findI][x + i], EMDgraph.y[y + findJ][x + i])
              c[3] += Math.abs(findI - findJ) * min
            }
          }
          if (c[3] > 0) {
            ctx3.beginPath()
            ctx3.strokeStyle = '#f0f'
            ctx3.moveTo(x + gridX / 2, y + gridY / 2)
            ctx3.lineTo(x + gridX / 2, y + gridY / 2 - c[3] / 500)
            ctx3.stroke()
          }
          const m = (Math.max(c[0], c[1]) + Math.max(c[2] + c[3])) / 2
          cArray[Math.round(blockY)][Math.round(blockX)] = [...c, m]
        }
      }
      let resultObjects = []

      for (let y = 0; y < cArray.length; y++) {
        for (let x = 0; x < cArray[y].length; x++) {
          if (x === 0 || y === 0) continue
          if (cArray[y][x] === 0 || cArray[y][x][4] < this.magnT) continue

          let index = resultObjects.findIndex(el => el.includes(`${x},${y - 1}`))
          if (index !== -1) {
            const sx = Sx(cArray[y][x], cArray[y - 1][x])
            this.maxBlockT = Math.max(this.maxBlockT, sx)
            if (sx > this.blockT) {
              resultObjects[index].push(`${x},${y}`)
              continue
            }
          }

          index = resultObjects.findIndex(el => el.includes(`${x - 1},${y}`))
          if (index !== -1) {
            const sy = Sy(cArray[y][x - 1], cArray[y][x])
            this.maxBlockT = Math.max(this.maxBlockT, sy)
            if (sy > this.blockT) {
              resultObjects[index].push(`${x},${y}`)
              continue
            }
          }

          resultObjects.push([`${x},${y}`])
        }
      }

      resultObjects = resultObjects.map(el =>
        el.map(xy => xy.split(',').map(num => parseInt(num)))
      )
      // console.log(resultObjects)
      resultObjects.forEach(arr => {
        const X = arr.map(el => el[0])
        const Y = arr.map(el => el[1])
        const minX = Math.min(...X) * gridX
        const maxX = Math.max(...X) * gridX
        const minY = Math.min(...Y) * gridY
        const maxY = Math.max(...Y) * gridY

        ctx4.beginPath()
        ctx4.moveTo(minX, minY)
        ctx4.lineTo(maxX + gridX, minY)
        ctx4.lineTo(maxX + gridX, maxY + gridY)
        ctx4.lineTo(minX, maxY + gridY)
        ctx4.lineTo(minX, minY)
        ctx4.strokeStyle = '#ff0'
        ctx4.lineWidth = 3
        ctx4.stroke()
      })

      prevFrame = frameNow
    }
  }
}
</script>

<style scoped>
  canvas, video {
    border: 1px solid black;
    width: 100%;
    box-shadow: none;
    border-radius: 3px;
  }
  .overlay {
    top: 0;
    position: absolute;
    left: 15px;
    width: calc(100% - 30px);
  }
  .btn {
    display: inline-block;
    margin: 0.3em;
  }
</style>