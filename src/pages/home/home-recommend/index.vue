<template>
  <scroll-view
    @scrolltolower="handleToLoawer"
    class="recommends_view"
    scroll-y
    v-if="recommends.length > 0"
  >
    <!-- 推荐 开始-->
    <view class="recommend_wrap">
      <navigator :url="`/pages/album/index?id=${item.target}`" class="recommend_item" v-for="item in recommends" :key="item.id">
        <image mode="widthFix" :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐 结束 -->
    <!-- 月份 开始 -->
    <view class="month_wrap">
      <view class="month_title">
        <view class="months_title_info">
          <view class="months_info">
            <text>{{ months.DD }} /</text>
            {{ months.MM }} 月
          </view>
          <view class="months_text">{{ months.title }}</view>
        </view>
        <view class="months_title_more">更多</view>
      </view>
      <view class="month_content">
        <view class="months_item" v-for="(item,index) in months.items" :key="item.id">
          <go-detail :list="months.items" :index="index">
            <image
            mode="aspectFill"
            :src="item.thumb + item.rule.replace('$<Height>', 360)"
          ></image>
          </go-detail>
          
        </view>
      </view>
    </view>
    <!-- 月份 结束 -->
    <!-- 热门 开始 -->
    <view class="hots_wrap">
      <view class="hots_title"><text>热门</text></view>
      <view class="hots_content">
        <view class="hots_item" v-for="(item,index) in hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image :src="item.thumb" mode="widthFix" />
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 热门 结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from "@/components/goDetail"
export default {
  components: {
    goDetail
  },
  data() {
    return {
      //推荐列表
      recommends: [],
      //月份
      months: {},
      //热门
      hots: [],
      prams: {
        //获取几条
        limit: 30,
        //关键字
        order: "hot",
        skip: 0,
      },
      //判断是否还有更多数据获取
      hasMore: true
    };
  },
  mounted() {
    //修改页面标题
        uni.setNavigationBarTitle({
            title: '首页'
        });
    this.getList();

  },
  methods: {
    //获取数据
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.prams,
      }).then((result) => {
        console.log(result);
        if(result.res.vertical.length === 0) {
          this.hasMore = false;
          return;
        }
        //第一次请求才需要赋值的内容
        if (this.recommends.length === 0) {
          this.recommends = result.res.homepage[1].items;
          this.months = result.res.homepage[2];
          //将时间戳改成 18 / 1月格式 moment.js
          this.months.MM = moment(this.months.stime).format("MM");
          this.months.DD = moment(this.months.stime).format("DD");
        }

        this.hots = [...this.hots, ...result.res.vertical];
      });
    },
    handleToLoawer() {
      if(this.hasMore) {
        this.prams.skip += this.prams.limit;
        this.getList();
      } else {
        uni.showToast({
          title: '没有更多数据了',
          icon: "none"
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.recommends_view {
  //高度=屏幕宽度-头部标题
  height: calc(100vh - 36rpx);
}
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width: 50%;
    border: 5rpx solid #fff;
  }
}
//月份
.month_wrap {
  .month_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .months_title_info {
      color: $color;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .months_info {
        text {
          font-size: 36rpx;
        }
      }

      .months_text {
        font-size: 34rpx;
        margin-left: 30rpx;
        color: #666;
      }
    }

    .months_title_more {
      font-size: 34rpx;
      color: $color;
    }
  }

  .month_content {
    display: flex;
    flex-wrap: wrap;
    .months_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
//热门
.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      padding-left: 10rpx;
      border-left: 20rpx solid $color;
      font-size: 24rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hots_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {
      }
    }
  }
}
</style>