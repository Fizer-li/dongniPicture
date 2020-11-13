<template>
  <view>
    <!-- 用户信息 -->
    <view class="user_info" v-if="imgDetail.user">
      <view class="user_icon">
        <image :src="imgDetail.user.avatar" mode="widthFix" />
      </view>
      <view class="user_desc">
        <view class="user_name">{{ imgDetail.user.name }}</view>
        <view class="user_time">{{ imgDetail.cnTime }}</view>
      </view>
    </view>
    <!-- 高清大图 -->
    <view class="high_img">
      <swiper-action @swiperAction="handleSwiperAction">
        <image :src="imgDetail.thumb" mode="widthFix" />
      </swiper-action>
    </view>
    <!-- 点赞 -->
    <view class="user_rank">
      <view class="rank">
        <text class="iconfont icondianzan">{{ imgDetail.rank }}</text>
      </view>
      <view class="user_collect">
        <text class="iconfont iconshoucang">收藏</text>
      </view>
    </view>

    <!-- 热门评论 -->
    <view class="comment hot" v-if="hot.length">
      <view class="comment_title">
        <text class="iconfont iconhot1"></text>
        <text class="comment_text">最热评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in hot" :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon"
              ><image :src="item.user.avatar" mode="widthFix"
            /></view>
            <!-- 用户名称 -->
            <view class="user_name">
              <view class="user_nickname">{{ item.user.name }}</view>
              <view class="user_time">{{ item.cnTime }}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for="item2 in item.user.title"
                :key="item2.icon"
                :src="item2.icon"
                mode="widthFix"
              ></image>
            </view>
          </view>
          <!-- 评论内容 -->
          <view class="comment_desc">
            <view class="comment_content">{{ item.content }}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">{{ item.size }}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最新评论 -->
    <view class="comment new" v-if="comment.length">
      <view class="comment_title">
        <text class="iconfont iconpinglun"></text>
        <text class="comment_text">最新评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in comment" :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon"
              ><image :src="item.user.avatar" mode="widthFix"
            /></view>
            <!-- 用户名称 -->
            <view class="user_name">
              <view class="user_nickname">{{ item.user.name }}</view>
              <view class="user_time">{{ item.cnTime }}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for="item2 in item.user.title"
                :key="item2.icon"
                :src="item2.icon"
                mode="widthFix"
              ></image>
            </view>
          </view>
          <!-- 评论内容 -->
          <view class="comment_desc">
            <view class="comment_content">{{ item.content }}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">{{ item.size }}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- 下载 -->
    <view class="download">
      <view class="download_btn" @click="handleDownload">下载图片</view>
    </view>
  </view>
</template>

<script>
import moment from "moment";
//设置语言为中文
moment.locale("zh-cn");
//导入swiperAction组件
import swiperAction from "@/components/swiperAction";
export default {
  components: {
    swiperAction,
  },
  data() {
    return {
      imgDetail: {},
      //专辑 数据
      album: [],
      //热门评论
      hot: [],
      //最新评论
      comment: [],
      //图片索引
      imgIndex: 0,
    };
  },
  onLoad() {
    const { imgIndex } = getApp().globalData;
    this.imgIndex = imgIndex;
    this.getData();
  },
  methods: {
    //给当前页面赋值
    getData() {
      const { imgList } = getApp().globalData;
      this.imgDetail = imgList[this.imgIndex];
      this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow();
      //发送请求，获取热门评论
      this.getComments(this.imgDetail.id);
    },
    getComments(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`,
      }).then((result) => {
        console.log(result);
        result.res.hot.forEach(
          (v) => (v.cnTime = moment(v.atime * 1000).fromNow())
        );
        result.res.comment.forEach(
          (v) => (v.cnTime = moment(v.atime * 1000).fromNow())
        );
        this.hot = result.res.hot;
        this.comment = result.res.comment;
        //给时间指定格式为 xxx年前
      });
    },
    //滑动事件
    handleSwiperAction(e) {
      const { imgList } = getApp().globalData;
      if (e.direction === "left" && this.imgIndex < imgList.length - 1) {
        this.imgIndex++;
        this.getData();
      } else if (e.direction === "right" && this.imgIndex > 0) {
        this.imgIndex--;
        this.getData();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none",
        });
      }

      console.log(e);
    },
    //点击下载图片
    async handleDownload() {
      // 1.将图片从远程下载到小程序缓存
      // 2.从缓存中下载到本地
      await uni.showLoading({
        title: '下载中'
      });
      const result1 = await uni.downloadFile({ url: this.imgDetail.img });
      const { tempFilePath } = result1[1];

      const result2 = await uni.saveImageToPhotosAlbum({
        filePath: tempFilePath,
      });

      uni.hideLoading();
      await uni.showToast({
        title: '下载成功',
        icon: 'success'
      });
    },
  },
};
</script>

<style lang="scss" scoped>
//用户信息
.user_info {
  padding: 20rpx;
  display: flex;
  .user_icon {
    padding: 0 20rpx;
    image {
      width: 88rpx;

      border-radius: 50%;
    }
  }
  .user_desc {
    .user_name {
      // font-size: 40rpx;
      color: #72757a;
    }
    .user_time {
      font-size: 24rpx;
      color: #e7e5e5;
      padding: 10rpx 0;
    }
  }
}
.high_img {
  image {
    border-bottom: 1rpx solid #eee;
  }
}
//点赞
.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eee;
  .rank {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .user_collect {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
//热门评论
.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: red;
      font-size: 40rpx;
    }

    .comment_text {
      margin-left: 10rpx;
      font-size: 28rpx;
      color: #666;
      font-weight: 600;
    }
  }

  .comment_list {
    .comment_item {
      border-bottom: 15rpx solid #eee;
      .comment_user {
        display: flex;
        padding: 20rpx;
        .user_icon {
          width: 15%;
          image {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            padding: 5rpx 0;
            color: #ccc;
            font-size: 24rpx;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }

      .comment_desc {
        display: flex;
        padding: 30rpx;
        .comment_content {
          flex: 1;
          padding-left: 15%;
          color: #000;
          font-weight: 600;
        }
        .comment_like {
          text-align: right;
        }
      }
    }
  }
}

//最新评论
.new {
  .iconpinglun {
    color: aqua !important;
  }
}

//下载图片
.download {
  height: 120rpx;
  display: flex;
  justify-content: center;
  align-items: center;
  .download_btn {
    width: 90%;
    height: 80%;
    background-color: $color;
    color: #fff;
    font-size: 50rpx;
    font-weight: 600;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>