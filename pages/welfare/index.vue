<template>
	<view class="content">
		<view class="cu-list grid" :class="['col-' + 3,true?'':'no-border']">
			<view class="cu-item" v-for="(item,index) in cuIconList" :key="index" @click="gotype(index)">
				<view :class="['cuIcon-' + item.cuIcon,'text-' + item.color]">
					<view class="cu-tag badge" v-if="item.badge!=0">
						<block v-if="item.badge!=1">{{item.badge>99?'99+':item.badge}}</block>
					</view>
				</view>
				<text>{{item.name}}</text>
			</view>
		</view>
	</view>
	
</template>

<script>
	
	export default {
		data() {
			return {
				cuIconList: [{
					cuIcon: 'musicfill',
					color: 'red',
					badge: 120,
					name: '音乐电台',
					url:'./more',
					data:'https://www.5ifxw.com/yinyue/'
				}, {
					cuIcon: 'picfill',
					color: 'orange',
					badge: 1,
					name: '微信聊天生成',
					url:'./more',
					data:'https://www.5ifxw.com/jietu/'
				}, {
					cuIcon: 'picfill',
					color: 'yellow',
					badge: 0,
					name: '支付宝生成器',
					url:'./more',
					data:'https://www.5ifxw.com/jietu/alipay.html'
				}, {
					cuIcon: 'emoji',
					color: 'olive',
					badge: 22,
					name: '搜索表情',
					url:'./more',
					data:'https://www.5ifxw.com/biaoqing/search.php'
				},  {
					cuIcon: 'redpacket',
					color: 'purple',
					badge: 0,
					name: '羊毛党',
					// url:'./welfare',
					url:'',
					data:''
				}],
			}
		},
		onLoad() {
         this.getlist();
		},
		methods: {
			gotype(index){
				
				uni.navigateTo({
					url:this.cuIconList[index].url+'?data='+encodeURIComponent(this.cuIconList[index].data)
				})
			},
			
			},
			
		
	}
</script>

<style lang="scss" scoped>
	.content {
		
	}

	
</style>
