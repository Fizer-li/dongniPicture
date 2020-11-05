<template>
  <view @click="handleClick">
      <slot></slot>
  </view>
</template>

<script>
export default {
    props:  {
        list: Array,
        index: Number
    },
    methods: {
        handleClick() {
            //保存图片信息和索引到全局
            getApp().globalData.imgList = this.list;
            getApp().globalData.imgIndex = this.index;
            //跳转页面
            uni.navigateTo({
                 url: '/pages/imgDetail/index'
            });
        }
    }
}
</script>

<style lang='scss' scoped>

</style>