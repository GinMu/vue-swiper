<template lang="html">
  <div class="swiper-container">
    <img :src="currentItem.src" :alt="currentItem.alt" @touchstart.prevent.stop="moveStart" @touchend.stop="moveEnd"/>
    <div class="swiper-pagination">
      <span v-for="(value, index) in swiperList" class="swiper-dot" :class="{'active': index == initActived}" @click="switchImage(index)"></span>
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
      if (distance > 20) {
        bus.$emit('swiperLeft', distance)
      } else if (distance < -20) {
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
