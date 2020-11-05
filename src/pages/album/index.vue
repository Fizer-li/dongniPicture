<template>
  <view>
    <!-- 专辑背景 开始-->
    <view class="album_bg">
      <image mode="widthFix" :src="album.cover" />
      <view class="album_info">
        <view class="album_name">{{ album.name }}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>
    <!-- 专辑背景 结束-->
    <!-- 专辑作者 开始-->
    <view class="album_author">
      <view class="album_author_info">
        <image :src="album.user.avatar" mode="widthFix" />
        <view class="author_name">{{ album.user.name }}</view>
      </view>
      <view class="album_author_desc"
        ><text>{{ album.desc }}</text></view
      >
    </view>
    <!-- 专辑作者 结束-->
    <!-- 列表 开始 -->
    <view class="album_list">
      <view class="album_item" v-for="(item,index) in wallpaper" :key="item.id">
        <go-detail :list="wallpaper" :index="index">
          <image
            :src="item.thumb + item.rule.replace('$<Height>', 360)"
            mode="aspectFill"
          />
        </go-detail>
      </view>
    </view>
    <!-- 列表 结束 -->
  </view>
</template>

<script>
import goDetail from "@/components/goDetail";
export default {
  components: {
    goDetail
  },
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
        //first 1表示第一次请求，返回对象中album有值
        // first 0 表示不是第一次请求，返回对象中album值为空
        first: 1,
      },
      id: -1,
      //专辑信息
      album: {},
      //专辑列表
      wallpaper: [],
      hasMore: true,
    };
  },
  onLoad(options) {
    console.log(options);
    this.id = options.id;
    // this.id = "5d5f8e45e7bce75ae7fb8278";
    // this.id = "5dea515de7bce73981faf1a9"
    this.getList();
  },
  onReachBottom() {
    if (this.hasMore) {
      this.params.first = 0;
      this.params.skip += this.params.limit;
      this.getList();
    } else {
      uni.showToast({
        title: "没有更多数据了",
        icon: "none",
      });
    }
  },
  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params,
      }).then((result) => {
        console.log("jieguo", result);
        // album为空的时候才需要赋值，也就是只赋值一次
        if (Object.keys(this.album).length === 0) {
          this.album = result.res.album;
        }
        //没有返回值了就不用发送请求
        if (result.res.wallpaper.length === 0) {
          this.hasMore = false;
          //为了不让第一次请求返回值为空时弹出提示框
          if(this.params.first === 0) {
            uni.showToast({
            title: "没有更多数据了",
            icon: "none",
          });
          }
          return;
        }
        this.wallpaper = [...this.wallpaper, ...result.res.wallpaper];
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.album_bg {
  position: relative;
  .album_info {
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    display: flex;
    justify-content: space-between;
    height: 80rpx;
    align-items: center;
    color: #fff;
    padding: 0 15rpx;
    .album_name {
      font-size: 40rpx;
    }

    .album_btn {
      background-color: $color;
      width: 150rpx;
      height: 50rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 10rpx;
    }
  }
}

.album_author {
  padding: 10rpx;
  .album_author_info {
    padding: 10rpx 0;
    display: flex;
    image {
      width: 50rpx;
    }

    .author_name {
      color: #000;
      margin-left: 15rpx;
    }
  }

  .album_author_desc {
  }
}

.album_list {
  display: flex;
  flex-wrap: wrap;
  .album_item {
    width: 33.33%;
    border: 3rpx solid #fff;
    image {
      height: 160rpx;
    }
  }
}
</style>