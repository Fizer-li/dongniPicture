<template>
  <view>5</view>
</template>

<script>
export default {
  props: {
    url:String
  },
  data() {
    return {
      catoryList: []
    }
  },
  mounted() {
    console.log(this.url);
    this.getCatoryList();
  },
  methods: {
    async getCatoryList() {
      const res = await this.request({url:this.url});
      console.log(res);
    }
  }
}
</script>

<style>

</style>