<template>
	<view class="content">
		<web-view :src="url"></web-view>
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				url:''
			}
		},
		onLoad(opt) {
		  this.url = decodeURIComponent(opt.data);
			
			
		},
		methods: {

		}
	}
</script>

<style lang="scss" scoped>
	.content {
		
	}

	
</style>
