<template>
  <section>
    <div class="container">
      <h1>お絵かきアプリ</h1>
      <div class="canvasArea">
        <canvas id="canvas"
          @mousedown="handleMouseDown"
          @mouseup="handleMouseUp"
          @mousemove="handleMouseMove"
          @touchstart="handleTouchDown"
          @touchmove="handleTouchMove"
          width="300px" height="300px">
        </canvas>
      <div>
      </div>
      <div class="edit">
          <img src="~/assets/img/icon1.png" alt="" width="20">
          <span @click="handlePencileMode">鉛筆</span>
          <input class="form-control" type="color" v-model="lineColor" />
          <img src="~/assets/img/icon2.png" alt="" width="20">
          <span @click="handleEraseMode">消しゴム</span>
          <span>太さ</span>
          <select v-model="lineWidth">
            <option v-for="i in 10" v-bind:value="i" :key="i">{{ i }}</option>
          </select>
      </div>
      <div class="tool">
        <p  class="redoBtn" @click="resetImage">やり直し</p>
        <input class="fileName" type="text" v-model="fileTitle">
        <span  class="saveBtn" @click="saveImage">保存</span>
      </div>
      </div>
    </div>
  </section>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  data () {
    return {
      mouse: {
        position: {
          x: 0,
          y: 0
        },
        down: false
      },
      canvas: null,
      context: null,
      lineColor: "#000000",
      lineWidth: 1,
      fileTitle: null,
      eraseMode: false
    }
  },
  mounted () {
    this.canvas = document.getElementById("canvas")
    this.context = this.canvas.getContext("2d")
  },
  computed: {
    currentMouse: function () {
      const rect = this.canvas.getBoundingClientRect()
      return {
        x: this.mouse.position.x - rect.left,
        y: this.mouse.position.y - rect.top
      }
    }
  },
  methods: {
    handlePencileMode () {
      this.eraseMode = false
    },
    handleEraseMode () {
      this.eraseMode = true
    },
    draw (event) {
      if (this.mouse.down) {
        if (this.mouse.down　&& !this.eraseMode) {
          this.context.strokeStyle = this.lineColor
           console.log('色変わる')
        } else {
          this.context.strokeStyle = "#ffffff"
          console.log('消しゴム')
      }
        
        this.context.lineTo(this.currentMouse.x, this.currentMouse.y)
        this.context.stroke()
    }

    },
    handleMouseDown (event) {
      this.context.lineWidth = this.lineWidth
      
      this.mouse.down = true
      this.mouse.position = {
        x: event.pageX,
        y: event.pageY
      }
      this.context.beginPath()
      this.context.moveTo(this.currentMouse.x, this.currentMouse.y)
    },
    handleMouseUp () {
      this.mouse.down = false
      this.context.closePath()
    },
    handleMouseMove (event) {
      this.mouse.position = {
        x: event.pageX,
        y: event.pageY
      }
      this.draw(event)
    },
    handleTouchUp: function(event) {
      this.mouse.down = false
      this.context.closePath()
    },
    handleTouchDown (event) {
      this.context.lineWidth = this.lineWidth
      const t = event.changedTouches[0]
      this.mouse.down = true
      this.mouse.position = {
        x: t.pageX,
        y: t.pageY
      }
      this.context.beginPath()
      this.context.moveTo(this.currentMouse.x, this.currentMouse.y)
    },
    handleTouchMove: function(event) {
      console.log('2')
      const t = event.changedTouches[0]
      this.mouse.position = {
        x: t.pageX,
        y: t.pageY
      }
      this.draw(event)
    },
    handleTouchUp: function(event) {
      console.log('3')
      this.mouse.down = false
      this.context.closePath()
    },
    saveImage (event) {
      var png = this.canvas.toDataURL('image/png')
      png = png.replace(/^.*,/, '')

      var bin = atob(png)
      var buffer = new Uint8Array(bin.length)
      for (var i = 0; i < bin.length; i++) {
        buffer[i] = bin.charCodeAt(i)
      }
      var blob = new Blob([buffer.buffer], {
        type: 'image/png'
      })

      var url = (window.URL || window.webkitURL)
      var dataURL = url.createObjectURL(blob)
      var event = document.createEvent("MouseEvents")
      event.initMouseEvent("click", true, false, window,
        0, 0, 0, 0, 0, false, false, false, false, 0, null)
      var a = document.createElementNS("http://www.w3.org/1999/xhtml", "a")
      a.href = dataURL
      var title = this.fileTitle ? this.fileTitle : 'result'
      a.download = `${title}.png`
      a.dispatchEvent(event)
    },
    resetImage (event) {
      this.context.clearRect(0, 0, 300, 300)
    }
  }
}
</script>


<style>
body {
  font-family: 'Hiragino Kaku Gothic Pro', 'ヒラギノ角ゴ Pro W3', メイリオ, Meiryo, 'ＭＳ Ｐゴシック', sans-serif;
  background: #f5f5f5;
  color:#666;
}
.container {
  margin: 1rem 0;
  text-align: center;
}
h1 {
  font-weight: normal;
}
.canvasArea {
  background: white;
  display: inline-block;
  padding: 1rem;
}

.redoBtn {
  border: 1px solid #eb6a7a;
  display: inline-block;
  padding: 4px 12px;
  font-size: 14px;
  color: #eb6a7a;
}
.fileName {
  border: 1px solid gray;
  padding: 8px 10px;
}
.saveBtn {
  padding: 7px 9px;
  background: #eb6a7a;
  color: white;
  font-weight: bold;
}
.edit, .tool  {
  margin: 6px 0;
}
select {
  border: 1px solid #666;
}
canvas {
  border-style: solid;
  border: 1px solid #eee;
}
</style>
