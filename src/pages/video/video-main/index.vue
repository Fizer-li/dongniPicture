<template>
  <view>1-4</view>
</template>

<script>
export default {
  props:{
    urlObj:Object
  },
  data() {
    return {
      videoList: []
    }
  },
  watch: {
    urlObj() {
      console.log(this.urlObj)
      this.getList();
    }
  },
  mounted() {
    console.log(this.urlObj);
    this.getList();
  },
  methods: {
    async getList() {
      const res = await this.request({
        url: this.urlObj.url,
        data: this.urlObj.params
      })
      console.log(res);
    }
  }
}
</script>

<style>

</style>