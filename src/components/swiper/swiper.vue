<template lang="html">
  <div class="swiper-container">
    <img :style="transform" :src="currentItem.src" :alt="currentItem.alt" @touchstart.prevent.stop="touchStart" @touchend.stop="touchEnd" @touchmove="touchMove"/>
    <div class="swiper-pagination">
      <span v-for="(value, index) in swiperList" class="swiper-dot" :class="{'active': index == initActived}"></span>
    </div>
  </div>
</template>
<script>
import { bus } from './event-bus'
export default {
  data () {
    return {
      initActived: this.actived,
      start: 0,
      end: 0,
      translate3d_X: 0,
      translate3d_Y: 0
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
    },
    cicle: {
      type: Boolean,
      default: false
    }
  },
  created () {
    bus.$on('swiperLeft', this.swiperLeft)
    bus.$on('swiperRight', this.swiperRight)
  },
  beforeDestroy () {
    bus.$off('swiperLeft', this.swiperLeft)
    bus.$off('swiperRight', this.swiperRight)
  },
  computed: {
    currentItem: function () {
      return this.swiperList[this.initActived]
    },
    transform: function () {
      let value = 'translate3d(' + this.translate3d_X + 'px,' + this.translate3d_Y + 'px,0)'
      let translate3d = {
        'transition-duration': '500ms'
      }
      translate3d.transform = value
      return translate3d
    }
  },
  mounted () {},
  methods: {
    touchStart: function (e) {
      this.start = e.changedTouches[0].pageX
    },
    touchMove: function (e) {
      let move = e.changedTouches[0].pageX - this.start
      this.translate3d_X = move
    },
    touchEnd: function (e) {
      this.translate3d_X = 0
      this.end = e.changedTouches[0].pageX
      let distance = this.end - this.start
      if (distance > 30) {
        bus.$emit('swiperLeft', distance)
      } else if (distance < -30) {
        bus.$emit('swiperRight', distance)
      }
    },
    swiperLeft: function () {
      if (this.initActived > 0) {
        this.initActived = this.initActived - 1
      } else {
        if (this.cicle) {
          this.initActived = this.swiperList.length - 1
        }
      }
    },
    swiperRight: function () {
      if (this.initActived < this.swiperList.length - 1) {
        this.initActived = this.initActived + 1
      } else {
        if (this.cicle) {
          this.initActived = 0
        }
      }
    }
  },
  components: {}
}
</script>

<style lang="css" scoped>
.swiper-container {
  position: relative;
  overflow: hidden;
  transition: all 0ms ease;
}

.swiper-container img {
  width: 100%;
  max-height: 500px;
}

.swiper-container .swiper-pagination {
  position: absolute;
  bottom: 10px;
  width: 100%;
}

.swiper-container .swiper-pagination .swiper-dot {
  width: 10px;
  height: 10px;
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
