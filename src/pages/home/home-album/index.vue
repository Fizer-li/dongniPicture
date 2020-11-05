<template>
  <scroll-view class="album_scroll_view" scroll-y @scrolltolower="handleToLower">
    <view class="album_banner">
      <!-- 轮播 开始 -->
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" mode="" />
        </swiper-item>
      </swiper>
      <!-- 轮播 结束 -->
      <!-- 列表 开始 -->
      <view class="album_list">
        <navigator :url="`/pages/album/index?id=${item.id}`" class="album_item" v-for="item in album" :key="item.id">
          <view class="album_img"><image mode="aspectFill" :src="item.cover" /></view>
          <view class="album_info">
            <view class="album_name">{{ item.name }}</view>
            <view class="album_text">{{ item.desc }}</view>
            <view class="album_btn">
              <view class="album_attention">关注</view>
            </view>
          </view>
        </navigator>
      </view>
      <!-- 列表 结束 -->
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
      },
      //轮播图数组
      banner: [],
      //列表数组
      album: [],
      hasMore: true,
    };
  },
  mounted() {
    //修改页面标题
    uni.setNavigationBarTitle({
      title: "专辑",
    });
    this.getList();
  },
  methods: {
    //获取数据
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params,
      }).then((result) => {
        console.log(result);
        if (result.res.album.length === 0) {
          this.hasMore = false;
          return;
        }
        if (this.banner.length === 0) {
          this.banner = result.res.banner;
        }
        this.album = [...this.album, ...result.res.album];
      });
    },
    //滚动条触底事件
    handleToLower() {
        if(this.hasMore) {
            this.params.skip += this.params.limit;
            this.getList()
        }else {
            uni.showToast({
                title: '没有更多数据了',
                icon: 'none'
            });
        }
    }
  },
};
</script>

<style lang="scss" scoped>
.album_scroll_view {
    height: calc(100vh - 36px);
}

swiper {
  // 轮播图和image都有有默认高度，所以需要动态计算
  height: calc(750rpx / 2.3);
  image {
    height: 100%;
  }
}

.album_list {
  padding: 10rpx;
  .album_item {
    display: flex;
    padding: 10rpx 0;
    .album_img {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      overflow: hidden;
      padding: 0 10rpx;
      .album_name {
        padding: 10rpx 0;
        font-size: 3orpx;
        color: #000;
      }

      .album_text {
        padding: 10rpx 0;
        font-size: 24rpx;

        // 不换行，超出部分省略
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>