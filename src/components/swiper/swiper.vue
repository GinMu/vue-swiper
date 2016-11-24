<template lang="html">
  <div class="vue-swiper" @touchstart.prevent.stop="touchStart" @touchend.prevent.stop="touchEnd" @touchmove.prevent="touchMove">
    <div class="vue-swiper-group" :style="transform">
      <div class="vue-swiper-item" v-for="(item,index) in swiperList">
        <a href="javascript:void(0);">
          <img :src="loadingImage" :lazy-src="item.src" :alt="item.alt" @load="loaded(index,$event)">
          <p class="vue-swiper-title" v-text="item.title"></p>
        </a>
      </div>
    </div>
    <div class="vue-swiper-indicator" :class="{'vue-text-right': indicatorRight}">
      <div v-for="(value, index) in swiperList" class="vue-indicator" :class="{'vue-active': index == actived}"></div>
    </div>
  </div>
</template>
<script>
import { bus } from './event-bus'
import * as constant from './constant'
let imgs = []
export default {
  data () {
    return {
      actived: 0,
      startX: 0,
      endX: 0,
      startY: 0,
      endY: 0,
      translate3d_X: 0,
      translate3d_Y: 0,
      loadingImage: constant.LOADING_IMAGE
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
    cicle: {
      type: Boolean,
      default: false
    },
    indicatorRight: {
      type: Boolean,
      default: false
    }
  },
  created () {
    bus.$on('swiperLeft', this.swiperLeft)
    bus.$on('swiperRight', this.swiperRight)
    bus.$on('lazyLoad', this._lazyLoad)
  },
  beforeDestroy () {
    bus.$off('swiperLeft', this.swiperLeft)
    bus.$off('swiperRight', this.swiperRight)
  },
  computed: {
    transform: function () {
      let value = 'translate3d(' + this.translate3d_X + 'px,' + this.translate3d_Y + 'px,0)'
      let translate3d = {
        'transition-duration': '500ms',
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
      if (Math.abs(move) > constant.MIN_DISTANCE) {
        let x = this.actived * e.target.clientWidth
        this.translate3d_X = move - x
      }
    },
    touchEnd: function (e) {
      this.endX = e.changedTouches[0].pageX
      this.endY = e.changedTouches[0].pageY
      let swiperType = this._getDirection()
      let clientWidth = e.target.clientWidth
      if (swiperType === constant.MOVE_LEFT) {
        bus.$emit('swiperLeft', clientWidth)
      } else if (swiperType === constant.MOVE_RIGHT) {
        bus.$emit('swiperRight', clientWidth)
      } else {
        this.translate3d_X = -this.actived * clientWidth
      }
    },
    swiperLeft: function (width) {
      if (this.actived < this.swiperList.length - 1) {
        this.actived = this.actived + 1
        this._emitLazyLoad()
      } else {
        if (this.cicle) {
          this.actived = 0
        }
      }
      this.translate3d_X = -this.actived * width
    },
    swiperRight: function (width) {
      if (this.actived > 0) {
        this.actived = this.actived - 1
        this._emitLazyLoad()
      } else {
        if (this.cicle) {
          this.actived = this.swiperList.length - 1
        }
      }
      this.translate3d_X = -this.actived * width
    },
    _getDirection: function () {
      let moveX = this.endX - this.startX
      let moveY = this.endY - this.startY
      if (Math.abs(moveX) < constant.MIN_DISTANCE && Math.abs(moveY) < constant.MIN_DISTANCE) {
        return constant.NO_MOVE
      }
      let angle = this._getAngle(moveX, moveY)
      if (angle > 135 && angle <= 180 || angle >= -180 && angle < -135) {
        return constant.MOVE_LEFT
      }
      if (angle >= -135 && angle <= -45) {
        return constant.MOVE_UP
      }
      if (angle >= 45 && angle <= 135) {
        return constant.MOVE_DOWN
      }
      return constant.MOVE_RIGHT
    },
    _getAngle: function (x, y) {
      return Math.atan2(y, x) * 180 / Math.PI
    },
    loaded: function (index, e) {
      let complete = this._lazyComplete(index, e.target)
      if (index === this.actived && complete) {
        let target = e.target
        let lazy = target.getAttribute('lazy-src')
        target.src = lazy
      }
    },
    _lazyLoad: function () {
      let images = this._getImgs()
      images[this.actived].src = this.swiperList[this.actived].src
    },
    _lazyComplete: function (index, target) {
      let currentSrc = target.src
      let completed = target.complete
      if (currentSrc !== this.swiperList[index].src || !completed) {
        return true
      }
      return false
    },
    _getImgs: function () {
      if (imgs.length === 0) {
        let swiperItems = document.getElementsByClassName('vue-swiper-item')
        for (let item of swiperItems) {
          let img = item.getElementsByTagName('img')[0]
          imgs.push(img)
        }
      }
      return imgs
    },
    _emitLazyLoad: function () {
      let imgs = this._getImgs()
      let target = imgs[this.actived]
      if (this._lazyComplete(this.actived, target)) {
        setTimeout(function () {
          bus.$emit('lazyLoad')
        }, 1000)
      }
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
  max-height: 180px;
}

@media (min-width: 321px) and (max-width: 375px) {
  .vue-swiper .vue-swiper-group .vue-swiper-item img {
    max-height: 215px;
  }
}

@media (min-width: 376px) and (max-width: 414px) {
  .vue-swiper .vue-swiper-group .vue-swiper-item img {
    max-height: 240px;
  }
}

@media (min-width: 768px) {
  .vue-swiper .vue-swiper-group .vue-swiper-item img {
    max-height: 401px;
  }
}

.vue-swiper-indicator {
	position: absolute;
	bottom: 0;
  height: 30px;
	width: 100%;
	text-align: center;
	background: none;
}

.vue-swiper-indicator .vue-indicator {
	display: inline-block;
	width: 8px;
	height: 8px;
  margin: 11px 6px;
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

.vue-swiper-title {
  line-height: 30px;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 30px;
  margin: 0;
  text-align: left;
  text-indent: 12px;
  opacity: .6;
  background-color: #000;
}

p {
  font-size: 14px;
  margin-top: 0;
  margin-bottom: 10px;
  color: #fff;
}

.vue-text-right {
  text-align: right!important;
}

</style>
