<template>
  <view class="flex flex-direction align-center justify-center" >
	  <view class="padding-top-xl">
		  <text class="text-xl text-bold">长按保存图片，欢迎打赏</text>
	  </view>
    <image class="imgs" src="../../static/images/weixin.png" mode="aspectFit" @longpress="logoTime" ></image>
	<image class="imgs" src="../../static/images/zhifubao.png" mode="aspectFit" @longpress="longTime" ></image>
  </view>
</template>

<script>

export default {
  data() {
    return {
     
    };
  },
  methods: {
	 logoTime(e){
		if(e.timeStamp>10000){
			
			uni.saveImageToPhotosAlbum({  // 然后调用这个方法
				filePath: "../../static/images/weixin.png",
				success : (res) => {
					uni.hideLoading();
					uni.showToast({title : '图片已保存'})
				}
			})
		}
	 },
	 longTime(){
		if(e.timeStamp>10000){
			uni.saveImageToPhotosAlbum({  // 然后调用这个方法
				filePath: "../../static/images/zhifubao.png",
				success : (res) => {
					uni.hideLoading();
					uni.showToast({title : '图片已保存'})
				}
			})
		} 
	 }
  }, 
};
</script>
<style lang="scss" scoped>
.imgs{
	padding-top: 30upx;
}
</style>
