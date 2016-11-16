<template lang="html">
  <div class="swiper-container">
    <img :src="currentItem.src" :alt="currentItem.alt" @touchstart="moveStart" @touchend="moveEnd"/>
    <div class="swiper-pagination">
      <span v-for="(value, index) in swiperList" class="swiper-dot" :class="{'active': index == initActived}" @click="switchImage(index)"></span>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      initActived: this.actived,
      start: 0,
      end: 0
    }
  },
  props: {
    swiperList: {
      type: Array,
      required: true,
      validator: function (value) {
        return value.length > 1
      }
    },
    actived: {
      type: Number,
      default: 0
    }
  },
  computed: {
    currentItem: function () {
      return this.swiperList[this.initActived]
    }
  },
  mounted () {},
  methods: {
    switchImage: function (index) {
      this.initActived = index
    },
    moveStart: function (e) {
      this.start = e.changedTouches[0].pageX
    },
    moveEnd: function (e) {
      this.end = e.changedTouches[0].pageX
      let distance = this.end - this.start
      if (distance > 20 && this.initActived > 0) {
        this.initActived = this.initActived - 1
        return
      }

      if (distance < -20 && this.initActived < this.swiperList.length - 1) {
        this.initActived = this.initActived + 1
      }
    },
    swiperLeft: function () {

    },
    swiperRight: function () {

    }
  },
  components: {}
}
</script>

<style lang="css" scoped>
.swiper-container {
  position: relative;
  overflow: hidden;
}

.swiper-container img {
  width: 100%;
  max-height: 500px;
}

.swiper-container .swiper-pagination {
  position: absolute;
  bottom: 20px;
  width: 100%;
}

.swiper-container .swiper-pagination .swiper-dot {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #000;
  opacity: .3;
  display: inline-block;
  margin: 0 10px;
  cursor: pointer;
}

.swiper-container .swiper-pagination .swiper-dot.active {
  background-color: #007aff;
  opacity: 1;
}
</style>
