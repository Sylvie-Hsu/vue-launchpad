<template>
  <div class="keyboard" style="height: 100%;"
    v-loading="checkSatatus"
    element-loading-background="rgba(0, 0, 0, 0)"
    :element-loading-text="statusMessage"
    >
    <div style="display: none;">
      <img src="../assets/blue.png" class="water-img water-img-blue">
      <img src="../assets/green.png" class="water-img water-img-green">
      <img src="../assets/purple.png" class="water-img water-img-purple">
      <img src="../assets/red.png" class="water-img water-img-red">
    </div>
    <div v-if="status == 4" style="display: flex; flex-direction: column; justify-content: center; height: 100%; position: relative;">
      <div style="display: flex; flex-direction: column;">
        <div v-for="i in 4" :key="i" style="display: flex; flex-direction: row; justify-content: center; margin-bottom: 10px; margin-left: 10px;">
          <div v-for="j in i" :key="j" class="button-hide"></div>
          <div v-for="j in 12" :key="j + i" 
            :class="classObject(i-1, j-1)">
            <div style="width: 50%; height: 50%; cursor: default;">{{ button[i - 1][j - 1][0] }}</div>
            <div style="width: 50%; height: 50%; cursor: default;">{{ button[i - 1][j - 1][1] }}</div>
            <div style="width: 50%; height: 50%; cursor: default;">{{ button[i - 1][j - 1][2] }}</div>
            <div style="width: 50%; height: 50%; cursor: default;">{{ button[i - 1][j - 1][3] }}</div>
          </div>
          <div v-for="j in (5-i)" :key="j + i + 12" class="button-hide"></div>
        </div>
        <div style="margin-top: 20px; color: #fff;">
          <div class="button-dir-block">
            <div 
            :class='{
              "up": true,
              "button-dir": true,
              "button-color-purple": dir==2
            }'
            >&uarr;</div>
          </div>
          <div class="button-dir-block">
            <div
            :class='{
              "up": true,
              "button-dir": true,
              "button-color-purple": dir==1
            }'>&larr;</div>
            <div
            :class='{
              "up": true,
              "button-dir": true,
              "button-color-purple": dir==4
            }'>&darr;</div>
            <div
            :class='{
              "up": true,
              "button-dir": true,
              "button-color-purple": dir==3
            }'>&rarr;</div>
          </div>
        </div>
      </div>
      <div class="canvas">
          <canvas></canvas>
      </div>
    </div>
    <div v-if="status == -1" style="display: flex; flex-direction: column; justify-content: center; height: 100%; text-align: center;">
      <div style="display: flex; flex-direction: row; justify-content: center;"><img src="../assets/error-sign.svg" width="200px"></div>
      <p style="color: #fff;">web server error</p>
    </div>
  </div>
</template>

<script>
import jszip from 'jszip'
import {Howl ,Howler} from 'howler'
const map = require('../assets/map.json')
const mapURL = {}
const zip = new jszip()
export default {
  name: 'Keyboard',
  data () {
    return {
      status: 0,
      addMusic: 1,
      button: [
        [
          ['!','','1',''],
          ['@','','2',''],
          ['#','','3',''],
          ['$','','4',''],
          ['%','','5',''],
          ['^','','6',''],
          ['&','','7',''],
          ['*','','8',''],
          ['(','','9',''],
          [')','','0',''],
          ['_','','-',''],
          ['+','','=','']
        ],
        [
          ['Q','','',''],
          ['W','','',''],
          ['E','','',''],
          ['R','','',''],
          ['T','','',''],
          ['Y','','',''],
          ['U','','',''],
          ['I','','',''],
          ['O','','',''],
          ['P','','',''],
          ['{','','[',''],
          ['{','',']','']
        ],
        [
          ['A','','',''],
          ['S','','',''],
          ['D','','',''],
          ['F','','',''],
          ['G','','',''],
          ['H','','',''],
          ['J','','',''],
          ['K','','',''],
          ['L','','',''],
          [':','',';',''],
          ['"','','\'',''],
          ['','','','En']
        ],
        [
          ['Z','','',''],
          ['X','','',''],
          ['C','','',''],
          ['V','','',''],
          ['B','','',''],
          ['N','','',''],
          ['M','','',''],
          ['<','',',',''],
          ['>','','.',''],
          ['?','','/',''],
          ['','','','Sh'],
          ['','','','NA']
        ]
      ],
      clickID: [49,50,51,52,53,54,55,56,57,48,189,187,
                81,87,69,82,84,89,85,73,79,80,219,221,
                65,83,68,70,71,72,74,75,76,186,222,13,
                90,88,67,86,66,78,77,188,190,191,16,-1],
      buttonStatus: [
        [0,0,0,0,0,0,0,0,0,0,0,0], 
        [0,0,0,0,0,0,0,0,0,0,0,0], 
        [0,0,0,0,0,0,0,0,0,0,0,0], 
        [0,0,0,0,0,0,0,0,0,0,0,0]
      ],
      buttonMusic: [
        [], [], [], []
      ],
      dir: 1
    }
  },
  computed: {
      statusMessage: function(){
        switch(this.status){
            case 0: return 'loading music...'
            case 1: return 'unzip music...'
            case 2: return 'check music, ' + this.addMusic + ' overage'
            case 3: return 'bind event...'
        }
      },
      checkSatatus: function(){
          if(this.status == 4 || this.status == -1){
              return false
          }
          else{
              return true
          }
      }
  },
  created: function(){
    console.log(this)
    const ts = this
    fetch('static/animals.zip')
    .then(function(raw){
      return raw.blob()
    })
    .then(function(blob){
      ts.status = 1
      zip.loadAsync(blob)
      .then(function(fzip){
        ts.status = 2
        ts.fileToblob(zip.files)
      })
      .catch(function(err){
        ts.status = -1
        console.error(err)
      })
    })
    .catch(function(err){
      console.error(err)
      ts.status = -1
    })
  },
  methods: {
    classObject: function(i, j){
      const data = {
        'button': true
      }
      data['button-' + i + '-' + j] = true
      return data
    },
    fileToblob: function(files){
      this.addMusic = 0
      for(let key in map){
        this.addMusic += map[key].length
      }
      this.status = 3
      const ts = this
      for(let key in map){
        mapURL[key] = []
        for(let i = 0; i < map[key].length; ++i){
          if(map[key][i] != ""){
            files[key + '/' + map[key][i] + '.mp3'].async('blob').then(function(blob){
              mapURL[key][i] = URL.createObjectURL(blob)
              ts.buttonMusic[parseInt(key[key.length - 1]) - 1][i] = new Howl({
                src: [mapURL[key][i]],
                format: ['mp3'],
                onload: function(){
                  ts.addMusic--
                  if(ts.addMusic == 0){
                    ts.bind()
                  }
                },
                onloaderror: function(){
                  ts.status = -1
                }
              })
            })
          }
          else{
            this.addMusic--
          }
        }
      }
    },
    bind: function(){
      this.status = 4
      const ts = this
      let currentPlay = null
      const playMusic = function(id, dir){
        const music = ts.buttonMusic[dir - 1][id]
        if(!music){
          return
        }
        music.stop()
        music.play()
        if(currentPlay != null && currentPlay != music){
          currentPlay.stop()
        }
        currentPlay = music
      }
      const addWaterAnimation = function(div, row){
        if(row == 0){
          const greenDiv = document.createElement('div')
          const greenNew = document.createElement('div')
          greenNew.className = 'water-div-animation-green'
          greenDiv.className = 'water-div'
          greenDiv.appendChild(greenNew)
          div.appendChild(greenDiv)
          setTimeout(function(){
            div.removeChild(greenDiv)
          }, 1000)
        }
        else if(row == 1){
          const blueDiv = document.createElement('div')
          const blueNew = document.createElement('div')
          blueNew.className = 'water-div-animation-blue'
          blueDiv.className = 'water-div'
          blueDiv.appendChild(blueNew)
          div.appendChild(blueDiv)
          setTimeout(function(){
            div.removeChild(blueDiv)
          }, 1000)
        }
        else if(row == 2){
          const purpleDiv = document.createElement('div')
          const purpleNew = document.createElement('div')
          purpleNew.className = 'water-div-animation-purple'
          purpleDiv.className = 'water-div'
          purpleDiv.appendChild(purpleNew)
          div.appendChild(purpleDiv)
          setTimeout(function(){
            div.removeChild(purpleDiv)
          }, 1000)
        }
        else if(row == 3){
          const redDiv = document.createElement('div')
          const redNew = document.createElement('div')
          redNew.className = 'water-div-animation-red'
          redDiv.className = 'water-div'
          redDiv.appendChild(redNew)
          div.appendChild(redDiv)
          setTimeout(function(){
            div.removeChild(redDiv)
          }, 1000)
        }
      }
      const keyStatus = []
      document.onkeydown = function(e){
        const code = e.keyCode
        if(!keyStatus[code]){
          keyStatus[code] = true
        }
        else{
          return
        }
        const id = ts.clickID.indexOf(code)
        console.log(code + ' is down')
        if(id != -1){
          const row = Math.floor(id / 12)
          const col = id % 12
          const div = document.getElementsByClassName('button-' + row + '-' + col)[0]
          addWaterAnimation(div, row)
          ts.buttonStatus[row][col] = 1
          if(row == 0){
            div.className += ' button-color-green'
          }
          else if(row == 1){
            div.className += ' button-color-blue'
          }
          else if(row == 2){
            div.className += ' button-color-purple'
          }
          else if(row == 3){
            div.className += ' button-color-red'
          }
          playMusic(id, ts.dir)
        }
        else if(code == 37){
          ts.dir = 1
        }
        else if(code == 38){
          ts.dir = 2
        }
        else if(code == 39){
          ts.dir = 3
        }
        else if(code == 40){
          ts.dir = 4
        }
      }
      document.onkeyup = function(e){
        const code = e.keyCode
        keyStatus[code] = false
        console.log(code + ' is up')
        const id = ts.clickID.indexOf(code)
        if(id != -1){
          const row = Math.floor(id / 12)
          const col = id % 12
          const div = document.getElementsByClassName('button-' + row + '-' + col)[0]
          ts.buttonStatus[row][col] = 0
          div.className = 'button ' + 'button-' + row + '-' + col
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.keyboard{
  position: relative;
  z-index: 1;
}
.button, .button-dir{
  background-color: #16171C;
  border-radius: 5px;
  border: 1px solid rgba(100,100,100,0.1);
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  text-align: center;
  color: #fff;
  line-height: 30px;
  font-size: 18px;
}
.button-dir{
  width: 60px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  margin-bottom: 5px;
  margin-right: 10px;
  display: flex;
  justify-content: center;
}
.button-dir-block{
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin-left: 10px;
}
.button{
  width: 60px;
  height: 60px;
  margin-right: 10px;
  position: relative;
}
.keyboard .button-color-green{
  background-color: #00AF7C
}
.keyboard .button-color-blue{
  background-color: #00BCC9;
}
.keyboard .button-color-purple{
  background-color: #7300C4
}
.keyboard .button-color-red{
  background-color: #D1004E
}
.button-hide{
  width: 30px;
  height: 30px;
  margin-right: 0px;
}
.canvas{
  position: absolute;
  width: 100%;
  height: calc(100% + 20px);
  top: -20px;
  left: 0;
}
.canvas canvas{
  width: 100%;
  height: 100%;
}
</style>

<style>
.keyboard .el-loading-spinner .path{
    stroke: #fff;
}
.keyboard .el-loading-spinner .el-loading-text{
    color: #fff;
}
.keyboard .water-img-green{
  position: absolute;
  width: 180px;
  height: 180px;
  top: -60px;
  left: -60px;
}
.keyboard .water-div{
  position: absolute;
  width: 60px;
  height: 60px;
  top: 0;
  left: 0;
  z-index: -1;
}
.keyboard .water-div-animation-green{
  position: absolute;
  width: 90px;
  height: 90px;
  left: -15px;
  top: -15px;
  border-radius: 50%;
  background-color: #17AD35;
  animation: flow-green 1s;
  animation-iteration-count: 1;
  animation-timing-function: ease-out;
}
@keyframes flow-green {
  0%{
    width: 90px;
    height: 90px;
    left: -15px;
    top: -15px;
    background-color: #17AD35;
    opacity: 1;
  }
  100%{
    width: 150px;
    height: 150px;
    left: -45px;
    top: -45px;
    background-color: #2EE0FE;
    opacity: 0;
  }
}
.keyboard .water-div-animation-purple{
  position: absolute;
  width: 90px;
  height: 90px;
  left: -15px;
  top: -15px;
  border-radius: 50%;
  background-color: #A149C7;
  animation: flow-purple 1s;
  animation-iteration-count: 1;
  animation-timing-function: ease-out;
}
@keyframes flow-purple {
  0%{
    width: 90px;
    height: 90px;
    left: -15px;
    top: -15px;
    background-color: #A149C7;
    opacity: 1;
  }
  100%{
    width: 150px;
    height: 150px;
    left: -45px;
    top: -45px;
    background-color: #FF2430;
    opacity: 0;
  }
}
.keyboard .water-div-animation-red{
  position: absolute;
  width: 90px;
  height: 90px;
  left: -15px;
  top: -15px;
  border-radius: 50%;
  background-color: #FE3B7D;
  animation: flow-red 1s;
  animation-iteration-count: 1;
  animation-timing-function: ease-out;
}
@keyframes flow-red {
  0%{
    width: 90px;
    height: 90px;
    left: -15px;
    top: -15px;
    background-color: #FE3B7D;
    opacity: 1;
  }
  100%{
    width: 150px;
    height: 150px;
    left: -45px;
    top: -45px;
    background-color: #222CAC;
    opacity: 0;
  }
}
.keyboard .water-div-animation-blue{
  position: absolute;
  width: 90px;
  height: 90px;
  left: -15px;
  top: -15px;
  border-radius: 50%;
  background-color: #11DFFA;
  animation: flow-blue 1s;
  animation-iteration-count: 1;
  animation-timing-function: ease-out;
}
@keyframes flow-blue {
  0%{
    width: 90px;
    height: 90px;
    left: -15px;
    top: -15px;
    background-color: #11DFFA;
    opacity: 1;
  }
  100%{
    width: 150px;
    height: 150px;
    left: -45px;
    top: -45px;
    background-color: #514AD3;
    opacity: 0;
  }
}
</style>