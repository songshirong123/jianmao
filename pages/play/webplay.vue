<template>
  <view class="">

		<web-view class="player" :webview-styles="webstyl" :src="url"  ></web-view>
		
     
  </view>
</template>

<script>
	var wv;//计划创建的webview
export default {
  data() {
    return {
		webstyl:{
			progress: {
			  color: '#FF3333'
			 }
		},
     
      url: "",
      
    };
  },
  methods: {
	start() {
	            uni.onGyroscopeChange((res) => {
	                console.log('gyroData.rotationRate.x = ' + res.x)
	                console.log('gyroData.rotationRate.y = ' + res.y)
	                console.log('gyroData.rotationRate.z = ' + res.z)
	            });
	            uni.startGyroscope({
	                interval: "normal",
	                success() {
	                    console.log('success')
	                },
	                fail() {
	                    console.log('fail')
	                }
	            })
	        },
			stop(){
			            uni.stopGyroscope({
			                success() {
			                    console.log('stop success!')
			                },
			                fail() {
			                    console.log('stop fail!')
			                }
			            })
			        }
   
  },
 

  onLoad(opt) { 
    this.url = decodeURIComponent(opt.url);
	
	setTimeout(function(){
	        plus.screen.lockOrientation('landscape-primary');
	   },1000)
  },
  onUnload() {
  	  var that = this;
  	            // #ifdef APP-PLUS
  	                plus.screen.unlockOrientation(); //解除屏幕方向的锁定，但是不一定是竖屏；
  	                plus.screen.lockOrientation('portrait'); //锁死屏幕方向为竖屏
  	            // #endif
			
  },
  onReady() {
  	 var currentWebview = this.$scope.$getAppWebview() 
	 setTimeout(function() {
	             wv = currentWebview.children()[0]
	             
				 wv.overrideUrlLoading({mode:'reject'}, function(e){
				         console.log('reject url: '+e.url);
				     });
					 
	         }, 2000); //如果是页面初始化调用时，需要延时一下
  },
  
  
  
};
</script>

<style lang="scss" scoped>

</style>
