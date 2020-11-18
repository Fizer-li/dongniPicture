<template>
  <view class="video_play">
    <image :src="videoObj.img" mode="" />
    <view class="video_tool">
      <view @click="handleClick" :class="['iconfont',muted?'iconjingyin':'iconshengyin']"></view>
      <view class="iconfont iconzhuanfa">
        <button open-type="share"></button>
      </view>
    </view>
    <view class="videw_wrap">
      <video :muted="muted" :src="videoObj.video" objectFit="fill"></video>
    </view>
    <view class="download">
      <view class="download_btn" @click="clickDownload">下载高清</view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoObj: {},
      //是否静音
      muted:false
    }
  },
    onLoad() {
        this.videoObj = getApp().globalData.video;
    },
    methods: {
      handleClick() {
        this.muted = !this.muted;
      },
      async clickDownload() {
        await uni.showLoading({title:'下载中'});
        const {tempFilePath} = (await uni.downloadFile({url:this.videoObj.video}))[1];
        await uni.saveVideoToPhotosAlbum({
          filePath: tempFilePath
        }); 
        uni.hideLoading();
        uni.showToast({
          title: '下载成功',
          
        });
      }
      
    }
}
</script>

<style lang="scss" scoped>
.video_play {
  position: relative;
  // display: flex;
  image {
    position: absolute;
    width: 100vw;
    height: 100vh;
    filter: blur(15px);
    z-index: -1;
  }
  .video_tool {
   height: 80rpx;
    display: flex;
    justify-content:flex-end;
    .iconfont {
      width: 80rpx;
      border-radius: 40rpx;
      background-color:rgba(0,0,0,.2);
      color: #fff;
      font-size: 50rpx;
      display: flex;
      justify-content: center;
      align-items:center;
      margin-right:20rpx
    }
    .iconzhuanfa {
      position: relative;
      button {
        position: absolute;
        width: 100%;
        height: 100%;
        opacity: 0;
      }
    }
  }
  .videw_wrap {
    display: flex;
    justify-content: center;
    video {
      width: 360rpx;
      height: 600rpx;
    }
  }
  .download {
    display: flex;
    justify-content: center;
    margin-top: 20rpx;
    .download_btn {
      width: 360rpx;
      height: 80rpx;
      border-radius: 40rpx;
      background-color: rgba(0,0,0,.2);
      display: flex;
      justify-content: center;
      align-items: center;
      border: 1rpx solid #fff;
      color: #fff;
    }
  }
}
</style>