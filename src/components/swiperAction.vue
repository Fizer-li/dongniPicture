<template>
  <view @touchstart="handleTouchstart" @touchend="handleTouchend">
    <slot></slot>
  </view>
</template>

<script>
export default {
  data() {
    return {
      startTime: 0,
      startX: 0
      
    };
  },
  methods: {
    handleTouchstart(event) {
        this.startTime = Date.now();
        this.startX = event.changedTouches[0].clientX;
    },
    handleTouchend(event) {
        const endTime = Date.now();
        const endX = event.changedTouches[0].clientX;
        //限定滑动时常
        if(endTime - this.startTime > 2000) {
            return;
        }
        let direction = "";
        if(Math.abs(endX - this.startX) > 10) {
            direction = endX - this.startX > 0 ? "right":"left";
        }else {
            return
        }
        //都通过表示用户做了合法的操作
        this.$emit("swiperAction",{direction})

    },
  },
};
</script>

<style>
</style>