<template>
<div id="app">
  <swiper :swiper-list="list" :cicle="cicle" :indicator-right="indicatorRight"></swiper>
</div>
</template>

<script>
import swiper from './components/swiper/swiper'

export default {
  data () {
    return {
      list: [{
        src: 'http://www.sinaimg.cn/dy/slidenews/2_img/2016_46/61364_1990254_843200.jpg',
        alt: '足球',
        title: '足球'
      }, {
        src: 'http://www.sinaimg.cn/dy/slidenews/2_img/2016_46/61364_1990256_648981.jpg',
        alt: '乔哈特',
        title: '乔哈特'
      }, {
        src: 'http://www.sinaimg.cn/dy/slidenews/2_img/2016_46/61364_1990268_670581.jpg',
        alt: '贝克汉姆',
        title: '贝克汉姆'
      }],
      cicle: false,
      indicatorRight: true
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
  font-size: 20px;
}
</style>
