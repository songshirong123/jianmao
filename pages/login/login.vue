<template>
	<view class="wrap">
		<view class="top"></view>
		<view class="content">
			<view class="title">邀请码验证</view>
			<input class="u-border-bottom" type="text" v-model="tel" placeholder="请输入邀请码" />
			<!-- <view class="tips">未注册的手机号验证后自动创建美团账号</view> -->
			<button @tap="submit" :style="[inputStyle]" class="getCaptcha">验证</button>
			<view class="alternative">
				
			</view>
		</view>
		<view class="buttom">
			<view class="loginType">
				<view class="wechat item">
					<view class="icon"><u-icon size="70" name="weixin-fill" color="rgb(83,194,64)"></u-icon></view>
					微信
				</view>
				<view class="QQ item">
					<view class="icon"><u-icon size="70" name="qq-fill" color="rgb(17,183,233)"></u-icon></view>
					QQ
				</view>
			</view>
			<view class="hint">
				登录代表同意
				<text class="link">用户协议、隐私政策，</text>
				所有资源来自网上, 该软件不参与任何制作, 上传, 储存等内容,
				禁止传播违法资源. 该软件仅供学习参考,该软件只对部分人开放学习使用,请于安装后24小时内删除,禁止用于任何商业用途,
				若本站收录的节目无意侵犯了贵司版权，请给网页底部邮箱地址来信,我们会及时处理和回复,谢谢。
						管理员邮箱：songshiyun123@gmail.com
			</view>
		</view>
		<u-toast ref="uToast" />
	</view>
</template>

<script>
	import db from "../../utils/database.js";
export default {
	data() {
		return {
			tel: '',
			config:''
		}
	},
	computed: {
		inputStyle() {
			let style = {};
			if(this.tel) {
				style.color = "#fff";
				style.backgroundColor = this.$u.color['warning'];
			}
			return style;
		}
	},
	methods: {
	async	submit() {
			if(this.tel==6312){
				this.config.login = true;
				const res = await db.update('setting', this.config);
				if (res.flag) {
				  this.$refs.uToast.show({ title: '验证成功', type: 'success', duration: '2300' })
				  uni.switchTab({
				  	url:'../film/film'
				  })
				}
			}else if(this.tel=='s6312'){
				this.config.login = true;
				this.config.R18 = false;
				const res = await db.update('setting', this.config);
				if (res.flag) {
				  this.$refs.uToast.show({ title: '验证成功', type: 'success', duration: '2300' })
				  uni.switchTab({
				  	url:'../film/film'
				  })
				}
			}else{
				this.$refs.uToast.show({ title: '验证错误', type: 'error', duration: '2300' })
			}
			
		},
		async getSettingConfig () {
		  const res = await db.get('setting', 'config')
		   this.config=res.data
		}
		
	},
	onLoad(){
		this.getSettingConfig()
	}
};
</script>

<style lang="scss" scoped>
.wrap {
	font-size: 28rpx;
	.top{
		padding-top: 200upx;
	}
	.content {
		width: 600rpx;
		margin: 80rpx auto 0;

		.title {
			text-align: left;
			font-size: 60rpx;
			font-weight: 500;
			margin-bottom: 100rpx;
		}
		input {
			text-align: left;
			margin-bottom: 10rpx;
			padding-bottom: 6rpx;
		}
		.tips {
			color: $u-type-info;
			margin-bottom: 60rpx;
			margin-top: 8rpx;
		}
		.getCaptcha {
			background-color: rgb(253, 243, 208);
			color: $u-tips-color;
			border: none;
			font-size: 30rpx;
			padding: 12rpx 0;
			
			&::after {
				border: none;
			}
		}
		.alternative {
			color: $u-tips-color;
			display: flex;
			justify-content: space-between;
			margin-top: 30rpx;
		}
	}
	.buttom {
		.loginType {
			display: flex;
			padding: 150rpx 150rpx 150rpx 150rpx;
			justify-content:space-between;
			
			.item {
				display: flex;
				flex-direction: column;
				align-items: center;
				color: $u-content-color;
				font-size: 28rpx;
			}
		}
		
		.hint {
			padding: 20rpx 40rpx;
			font-size: 20rpx;
			color: $u-tips-color;
			
			.link {
				color: $u-type-warning;
			}
		}
	}
}
</style>
