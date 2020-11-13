<template>
  <view class="home_category">
    <navigator :url="`/pages/imgCategory/index?id=${item.id}`"  class="category_item" v-for="item in categoryList" :key="item.id" >
      <image :src="item.cover" mode="aspectFill" />
      <view class="cotegory_name">{{ item.name }}</view>
    </navigator>
  </view>
</template>

<script>
export default {
  
  mounted() {
    //修改页面标题
    uni.setNavigationBarTitle({
      title: "分类",
    });
    this.getList();
  },
  data() {
    return {
      categoryList: []
     
    };
  },
  methods: {
    //获取图片分类列表
    async getList() {
      const result = await this.request({
        url: "http://157.122.54.189:9088/image/v1/vertical/category",
      });
      console.log(result);
      this.categoryList = result.res.category;
    },
    
  },
};
</script>

<style lang="scss" scoped>
.home_category {
  display: flex;
  flex-wrap: wrap;

  .category_item {
    border: 5rpx solid #fff;
    position: relative;
    width: 33.33%;

    image {
      height: 240rpx;
    }

    .cotegory_name {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 50rpx;

      // css3 渐变
      background-image: linear-gradient(
        to right top,
        rgba(0, 0, 0, 0.2),
        rgba(0, 0, 0, 0)
      );
      color: #fff;
      font-size: 40rpx;
      display: flex;
      align-items: center;
      padding-left: 20rpx;
    }
  }
}
</style>