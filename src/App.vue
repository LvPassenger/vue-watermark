<template>
  <div id="app">
    <ul class="operate">
      <li @click="reset">重置</li>
      <li @click="update">更新</li>
      <li @click="remove">删除</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    // 重置水印
    reset() {
      watermark.load({ watermark_txt: `测试水印，1021002301，测试水印，100101010111101` })
    },
    // 更新水印
    update() {
      watermark.load({ watermark_txt: `EB1DBDvlQHkV1653898831963@EB1_CSYSU9xMut_bNaHOOH7b_bCOvzFW6N_c4KDS061VmwH` })
    },
    // 删除
    remove() {
      try {
        watermark.remove()
      } catch (error) {}
    },
  },
  mounted() {
    watermark.init({ watermark_txt: `测试水印，1021002301，测试水印，100101010111101` })
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

html, body, #app {
  width: 100%;
  height: 100%;
  margin: 0;
}

.operate li {
  display: inline-block;
  height: 30px;
  line-height: 30px;
  margin-right: 20px;
  padding: 0 20px;
  color: #fff;
  background-color: #409eff;
  border-radius: 10px;
  /* cursor: pointer; */
}

</style>
