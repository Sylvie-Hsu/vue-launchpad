<template>
  <div class="index">
    <el-container style="height: 100%;">
      <el-header height="40px" style="overflow: hidden; background-color: #1A1A1A;">
        <img src="../assets/LaunchKeyboard.png" style="float: left; height: 30px; margin-top: 5px; margin-left: 10px;">
        <div style="float: right; line-height: 40px; margin-right: 10px; color: #fff;">
          <span style="color: #aaa; cursor: pointer;">Keyboard</span>
          &nbsp;|&nbsp;
          <span @click="routeTo('editor')" style="color: #fff; cursor: pointer;">Editor</span>
        </div>
      </el-header>
      <el-main style="height: calc(100% - 140px); background-color: #050608;">
        <keyboard></keyboard>
      </el-main>
      <el-footer height="140px" style="background-color: #1A1A1A; text-align: center;">
        <img src="../assets/LaunchKeyboard.png" style="height: 20px; margin-top: 20px;"><br>
        <span class="font">Open source code in github.</span><br>
        <span class="font">Contributors: @Zhang Fenxiang @Sylvie Hsu @Yuan Ting @Daniel Weber</span><br>
        <span class="font">Thanks @Daniel Weber for providing music support.</span><br>
        <span class="font">Contact us to develop the project.</span>
      </el-footer>
    </el-container>
  </div>
</template>

<script>
import keyboard from "./keyboard.vue"
export default {
  name: 'IndexPage',
  data () {
    return {
    }
  },
  methods: {
    routeTo: function(str){
      this.$router.push('/' + str)
    }
  },
  components:{
    keyboard
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.index{
  height: 100%;
}
.font{
  line-height: 20px;
  font-size: 12px;
  color: #fff;
}
</style>
