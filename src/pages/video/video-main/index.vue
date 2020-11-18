<template>
  <scroll-view scroll-y enable-flex class="video_main" @scrolltolower="handleScrolltolower">
    <view class="video_item"
    v-for="item in videowp"
    :key="item.id"
    @click="handleToVideo(item)"
    >
      <image :src="item.img" mode="widthFix">
    </view>
  </scroll-view>
</template>

<script>
export default {
  props:{
    urlObj:Object
  },
  data() {
    return {
      videowp: [],
      hasmore: true
    }
  },
  watch: {
    urlObj() {
      this.videowp = []
      this.getList();
    }
  },
  mounted() {
    console.log(this.urlObj);
    this.getList();
  },
  methods: {
    async getList() {
      const result = await this.request({
        url: this.urlObj.url,
        data: this.urlObj.params
      })
      if(result.res.videowp.length===0) {
        this.hasmore = false;
        uni.showToast({
          title:'没有更多数据了',
          icon: 'none'
        })
        return;
      }
      this.videowp = [...this.videowp,...result.res.videowp];
    },
    handleScrolltolower() {
      if(this.hasmore){
        this.urlObj.params.skip += this.urlObj.params.limit;
        this.getList();
      }else {
        uni.showToast({
          title:'没有更多数据了',
          icon: 'none'
        })
      }
    },
    //点击视频
    handleToVideo(item) {
      getApp().globalData.video = item;
      uni.navigateTo({
         url: '/pages/videoPlay/index'
      });
    }
  }
}
</script>

<style lang="scss" scoped>
.video_main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video_item {
    width: 33.33%;
    border: 5rpx solid #fff;
  }
}
</style>