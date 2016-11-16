<template>
<div id="app">
  <label>循环: <input type="checkbox" name="DofB" v-model="cicle" style="width:30px;height:30px;"/></label>
  <swiper :swiper-list="list" :actived="actived" :cicle="cicle"></swiper>
</div>
</template>

<script>
import swiper from './components/swiper/swiper'

export default {
  data () {
    return {
      list: [{
        src: 'http://www.sinaimg.cn/dy/slidenews/2_img/2016_46/61364_1990254_843200.jpg',
        alt: '足球'
      }, {
        src: 'http://www.sinaimg.cn/dy/slidenews/2_img/2016_46/61364_1990256_648981.jpg',
        alt: '乔哈特'
      }, {
        src: 'http://www.sinaimg.cn/dy/slidenews/2_img/2016_46/61364_1990268_670581.jpg',
        alt: '贝克汉姆'
      }],
      actived: 0,
      cicle: false
    }
  },
  components: {
    swiper
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  font-size: 50px;
}
</style>
