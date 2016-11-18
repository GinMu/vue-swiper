<template lang="html">
  <div class="vue-swiper" @touchstart.stop="touchStart" @touchend.prevent.stop="touchEnd" @touchmove.prevent="touchMove">
    <div class="vue-swiper-group" :style="transform">
      <div class="vue-swiper-item" v-for="item in swiperList">
        <a href="javascript:void(0);">
          <img :src="item.src" :alt="item.alt">
        </a>
      </div>
    </div>
    <div class="vue-swiper-indicator">
      <div v-for="(value, index) in swiperList" class="vue-indicator" :class="{'vue-active': index == initActived}"></div>
    </div>
  </div>
</template>
<script>
import { bus } from './event-bus'
export default {
  data () {
    return {
      initActived: this.actived,
      startX: 0,
      endX: 0,
      startY: 0,
      endY: 0,
      translate3d_X: 0,
      translate3d_Y: 0,
      NO_MOVE: 0,
      MOVE_UP: 1,
      MOVE_DOWN: 2,
      MOVE_LEFT: 3,
      MOVE_RIGHT: 4
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
        'transition-duration': '300ms',
        'transition-timing-function': 'linear'
      }
      translate3d.transform = value
      return translate3d
    }
  },
  mounted () {},
  methods: {
    touchStart: function (e) {
      this.startX = e.changedTouches[0].pageX
      this.startY = e.changedTouches[0].pageY
    },
    touchMove: function (e) {
      let move = e.changedTouches[0].pageX - this.startX
      let x = this.initActived * e.target.clientWidth
      this.translate3d_X = move - x
    },
    touchEnd: function (e) {
      this.endX = e.changedTouches[0].pageX
      this.endY = e.changedTouches[0].pageY
      let swiperType = this._getDirection()
      let clientWidth = e.target.clientWidth
      if (swiperType === this.MOVE_LEFT) {
        bus.$emit('swiperLeft', clientWidth)
      } else if (swiperType === this.MOVE_RIGHT) {
        bus.$emit('swiperRight', clientWidth)
      } else {
        this.translate3d_X = -this.initActived * clientWidth
      }
    },
    swiperLeft: function (width) {
      if (this.initActived < this.swiperList.length - 1) {
        this.initActived = this.initActived + 1
      } else {
        if (this.cicle) {
          this.initActived = 0
        }
      }
      this.translate3d_X = -this.initActived * width
    },
    swiperRight: function (width) {
      if (this.initActived > 0) {
        this.initActived = this.initActived - 1
      } else {
        if (this.cicle) {
          this.initActived = this.swiperList.length - 1
        }
      }
      this.translate3d_X = -this.initActived * width
    },
    _getDirection: function () {
      let moveX = this.endX - this.startX
      let moveY = this.endY - this.startY
      if (Math.abs(moveX) < 30 && Math.abs(moveY) < 30) {
        return this.NO_MOVE
      }
      let angle = this._getAngle(moveX, moveY)
      if (angle > 135 && angle <= 180 || angle >= -180 && angle < -135) {
        return this.MOVE_LEFT
      }
      if (angle >= -135 && angle <= -45) {
        return this.MOVE_UP
      }
      if (angle >= 45 && angle <= 135) {
        return this.MOVE_DOWN
      }
      return this.MOVE_RIGHT
    },
    _getAngle: function (x, y) {
      return Math.atan2(y, x) * 180 / Math.PI
    }
  },
  components: {}
}
</script>

<style lang="css" scoped>

.vue-swiper {
	position: relative;
	z-index: 1;
	overflow: hidden;
	width: 100%;
}

.vue-swiper .vue-swiper-group {
	font-size: 0;
	position: relative;
	-webkit-transition: all 0s linear;
	transition: all 0s linear;
	white-space: nowrap;
}

.vue-swiper .vue-swiper-group .vue-swiper-item {
	font-size: 14px;
	position: relative;
	display: inline-block;
	width: 100%;
	height: 100%;
	vertical-align: top;
	white-space: normal;
}

.vue-swiper .vue-swiper-group .vue-swiper-item > a:not(.vue-control-item) {
	line-height: 0;
	position: relative;
	display: block;
}

.vue-swiper .vue-swiper-group .vue-swiper-item img {
	width: 100%;
}

.vue-swiper-indicator {
	position: absolute;
	bottom: 8px;
	width: 100%;
	text-align: center;
	background: none;
}

.vue-swiper-indicator .vue-indicator {
	display: inline-block;
	width: 8px;
	height: 8px;
	margin: 1px 6px;
	cursor: pointer;
	border-radius: 50%;
	background: #fff;
  opacity: .3;
	-webkit-box-shadow: 0 0 1px 1px rgba(130, 130, 130, .7);
	box-shadow: 0 0 1px 1px rgba(130, 130, 130, .7);
}

.vue-swiper-indicator .vue-active {
	background: #007aff;
  opacity: 1;
}

</style>
