<template>
  <div class="keyboard" style="height: 100%;"
    v-loading="checkSatatus"
    element-loading-background="rgba(0, 0, 0, 0)"
    :element-loading-text="statusMessage"
    >
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
    fetch('static/eq.zip')
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
      document.onkeydown = function(e){
        const code = e.keyCode
        const id = ts.clickID.indexOf(code)
        console.log(code)
        if(id != -1){
          const row = Math.floor(id / 12)
          const col = id % 12
          const div = document.getElementsByClassName('button-' + row + '-' + col)[0]
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
.button, .button-dir{
  background-color: #16171C;
  border-radius: 5px;
  z-index: 10;
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
}
.keyboard .button-color-purple{
  background-color: #A149C7
}
.keyboard .button-color-green{
  background-color: #17AD35
}
.keyboard .button-color-blue{
  background-color: #11DFFA;
}
.keyboard .button-color-red{
  background-color: #FE3B7D
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
</style>