<template>
  <view>
    <view class="category_tab">
      <view class="category_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map((v) => v.title)"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d43c6f"
          ></uni-segmented-control>
        </view>
        <view class="iconfont iconsearch"></view>
      </view>
      <scroll-view
        @scrolltolower="handleScrolltolower"
        enable-flex
        scroll-y
        class="category_tab_content"
      >
        <view class="cate_item" v-for="(item,index) in imgList" :key="item.id" @click="handleClick(index)">
          <image :src="item.thumb" mode="widthFix" />
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
import { uniSegmentedControl } from "@dcloudio/uni-ui";
export default {
  components: {
    uniSegmentedControl,
  },
  onLoad(option) {
    this.id = option.id;
    // console.log(option);
    this.getList();
  },
  data() {
    return {
      items: [
        { title: "最新", order: "new" },
        { title: "热门", order: "hot" },
      ],
      current: 0,
      //分类id
      id: 0,
      imgList: [],
      //请求参数
      params: {
        limit: 30,
        skip: 0,
        order: "new",
      },
      hasMore: true,
    };
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
        this.params.order = this.items[e.currentIndex].order;
        this.params.skip = 0;
        this.imgList = [];
        this.getList();
      }
    },
    async getList() {
      const result = await this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params,
      });
      console.log(result);
      if (result.res.vertical.length === 0) {
        this.hasMore = false;
        uni.showToast({
          title: "没有更多数据了",
          icon: "none",
        });
      }
      this.imgList = [...this.imgList, ...result.res.vertical];
    },
    //滚动条触底
    handleScrolltolower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有更多数据了",
          icon: "none",
        });
      }
    },
    //点击图片跳转到大图
    handleClick(index) {
      //保存图片信息和索引到全局
      getApp().globalData.imgList = this.imgList;
      getApp().globalData.imgIndex = index;
      //跳转页面
      uni.navigateTo({
        url: "/pages/imgDetail/index",
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.category_tab {
  .category_tab_title {
    position: relative;
    .title_inner {
      width: 60%;
      margin: 0 auto;
    }
    .iconsearch {
      position: absolute;
      top: 50%;
      right: 5%;
      transform: translateY(-50%);
    }
  }
  .category_tab_content {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);
    .cate_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {
      }
    }
  }
}
</style>