<template>
  <view class="flex flex-direction align-center justify-center" >
	  <view class="padding-top-xl">
	  		  <text class="text-xl text-bold">长按保存图片，欢迎打赏</text>
	  </view>
    <image class="imgs" src="../../static/images/jiaoliu.png" mode="aspectFit" @longpress="logoTime" ></image>
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
				filePath: "../../static/images/jiaoliu.png",
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
	padding-top: 50upx;
}
</style>
