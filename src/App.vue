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
        src: './static/images/eagle.jpg',
        alt: 'eagle',
        title: 'eagle'
      }, {
        src: './static/images/red_panda.jpg',
        alt: 'red panda',
        title: 'red panda'
      }, {
        src: './static/images/white_bengal_tiger.jpg',
        alt: 'white bengal tiger',
        title: 'white bengal tiger'
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
