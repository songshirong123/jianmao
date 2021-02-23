<template>
	<view class="">
		<view class="content">
			<view class="player-videodisc">
				<image src="../../static/record.png" class=""></image>
				<image :src="audioData[0].view_image" class="rotateImg" :style="styleObj"></image>
			</view>
			<view class="player-box">
				<view class="player-slider">
					<view class="player-currentTime">
						{{audioCurTime[0]}}:{{audioCurTime[1]}}
					</view>
					<slider class="slider" min="0" :max="duration" :value="currentTime" activeColor="#ccc" backgroundColor="#eee"
					 block-size="16" @change="changeProgress" />
					<view class="player-duration">
						<!-- 音频总时长用的是后端的数据，如果用innerAudioContext对象的duration在切换歌曲的时候会出现计算错误的情况 -->
						{{longth}}
						<!-- {{audioDuration[0]}}:{{audioDuration[1]}} -->
					</view>
				</view>
				<view class="play-bar">
					<view class="" @click='pre'>
						<text class="cuIcon-backwardfill" style="color: #ccc;"></text>
					</view>
					<view class="play-menu">
						<text class="cuIcon-playfill" style="color: #ccc;" :class="isPlay?'cuIcon-stop':'cuIcon-playfill'" @tap="playMusic"></text>
					</view>
					<view class="" @click='next'>
						<text class="cuIcon-play_forward_fill" style="color: #ccc;"></text>
					</view>
				</view>
			</view>
		</view>
		<view class="play-list">
			<view class="" style="position: relative;">
				播放列表
				<view class="text-underline">
				</view>
			</view>
		</view>
		<view class="play-list-content" v-for="(item,index) in audioData" @tap="changeAudio(item)">
			<view class="play-list-content-title">
				{{item.name}}
			</view>
			<view class="play-list-content-desc">
				<text>{{item.longth}}</text><text>|</text><text>{{item.num}}人学过</text>
			</view>
		</view>
	</view>
</template>

<script>
	import musicApi from "@/common/src/app.js"
	const audioContext = uni.createInnerAudioContext();
	audioContext.autoplay = false;
	export default {
		data() {
			return {
				audioData: [
					
				],
				duration: '100',
				currentTime: '0',
				audioDuration: ['0', '00'],
				audioCurTime: ['0', '00'],
				longth: '',
				isPlay: false,
				timer: null,
				system: '',
				styleObj: {
					borderRadius: '50%',
					height: '80rpx',
					width: '80rpx',
					position: 'absolute',
					top: '50%',
					left: '50%',
					transform: 'translate(-50%,-50% )',
					transformOrigin: 'center'
				},
				rotateTimer: null,
				rotateDeg: 0
			}
		},
		onUnload() {
			this.isPlay = false
			audioContext.destroy()
		},
		onLoad(e) {
			// audioContext.src = this.audioData[0].file
			// this.longth = this.audioData[0].longth
			// this.system = uni.getSystemInfoSync().platform
			audioContext.onEnded((e) => {
				clearInterval(a.timer)
				clearInterval(a.rotateTimer)
				this.timer = null
				this.rotateTimer = null
				this.isPlay = false
				audioContext.seek(0);
				this.audioCurTime = ['0', '00']
				this.currentTime = '0'
			})
			this.getlist()
		},
		onShow() {},
		onReady() {
			
		},
		methods: {
			getlist(){
				musicApi.netease.searchSong({keyword: '周杰伦'}).then(
					data=>{
						
						data.data.songs.forEach(e=>{
							var list={
								file: "http://app.tiantai.com.cn/uploads/20200819/a25100936ec5d372c6805e5b476dbd59.mp3",
								id: 3,
								longth: "02:49",
								music_id: 'netease',
								name: "歌曲1",
								num: 12,
								artists:'',//作者
								view_image: "http://app.tiantai.com.cn/uploads/20200818/7f62a7cc3ca42c3e0fb130e79aa8cb9f.jpg"
							}
							list.id=e.id
							list.name=e.name
							if(e.artists.length>0){
								list.artists=e.artists.length>1?e.artists[0].name+'|'+e.artists[1].name:e.artists[0].name
							}
							
							list.view_image= e.album.cover
							this.audioData.push(list)
						})
					}
				)
			},
			// audio player part : in this part we'd like to show the similar layouts and functions as wangyi music. created by Hsi (1563792476@qq.com)
			// in order to avoid syntax error of playing time , use backend data to show duration time instead of calculating by yourself
			changeAudio(info) {
				musicApi.netease.getSongUrl(info.id).then(
					data=>{
						console.log(data)
						this.currentTime = '0'
						this.isPlay = false
						clearInterval(this.timer)
						this.timer = null
						clearInterval(this.rotateTimer)
						this.rotateTimer = null
						this.longth = info.longth
						this.audioCurTime = ['0', '00']
						this.duration = info.longth.slice(0, 2) * 60 + parseInt(info.longth.slice(3, 6))
						audioContext.seek(0);
						uni.setNavigationBarTitle({
							title: info.name
						});
						this.playMusic(data.data.url)
						
						
					}
				)
				
			},
			next() {
				// next song function, the main thought of next song function is that we should find out the index of song which is playing. 
				// first of all , clear the timer and stop rotating cover, and then find out the index 
				clearInterval(this.rotateTimer)
				this.rotateTimer = null
				let src = audioContext.src
				//tips: (complex array may cause performance issues)
				this.audioData.filter((currentValue, index, arr) => {
					if (currentValue.file == src) {
						if (index + 1 >= arr.length) {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							// once click next button , pause and reset playingtime 
							audioContext.seek(0);
							this.currentTime = '0'
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[0].file
							this.longth = this.audioData[0].longth
							uni.setNavigationBarTitle({
								title: this.audioData[0].name
							})
						} else {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							audioContext.seek(0);
							this.currentTime = '0'
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[index + 1].file
							this.longth = this.audioData[index + 1].longth
							uni.setNavigationBarTitle({
								title: this.audioData[index + 1].name
							})
						}
					} else {}
				});
			},
			pre() {
				clearInterval(this.rotateTimer)
				this.rotateTimer = null
				let src = audioContext.src
				this.audioData.filter((currentValue, index, arr) => {
					if (currentValue.file == src) {
						if (index == 0) {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							audioContext.seek(0);
							this.currentTime = '0'
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[arr.length - 1].file
							this.longth = this.audioData[arr.length - 1].longth
							uni.setNavigationBarTitle({
								title: this.audioData[arr.length - 1].name
							});
						} else {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							audioContext.seek(0);
							this.currentTime = '0'
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[index - 1].file
							this.longth = this.audioData[index - 1].longth
							uni.setNavigationBarTitle({
								title: this.audioData[index - 1].name
							});
						}
					} else {}
				});
			},
			playMusic(mp) {
				audioContext.src = mp
				// reset duration time by clicking midbutton only 
				let duration = audioContext.duration;
				let currentTime = audioContext.currentTime;
				this.duration = duration;
				this.currentTime = currentTime;
				this.audioCurTime[0] = Math.floor(Math.floor(currentTime) / 60);
				this.audioCurTime[1] = Math.floor(currentTime) % 60;
				if (this.isPlay) {
					this.isPlay = false;
					clearInterval(this.timer)
					clearInterval(this.rotateTimer)
					this.timer = null
					this.rotateTimer = null
					audioContext.pause();
				} else {
					this.isPlay = true;
					this.rotateTimer = setInterval(() => {
						this.rotateDeg++
						this.styleObj.transform = `translate(-50%,-50%) rotate(${this.rotateDeg}deg)`
					}, 50)
					audioContext.play();
					this.timer = setInterval(() => {
						this.currentTime++
						if (this.audioCurTime[1] > 58) {
							this.audioCurTime[0]++
							this.audioCurTime[1] = 0
							this.audioCurTime[1]++
						} else {
							this.audioCurTime[1]++
						}
					}, 1000)
				}
				let a = this
				uni.setNavigationBarTitle({
					title: a.audioData[0].name
				});
			},
			changeProgress(e) {
				// ios 拖动slider 会出现漂移，定位不准，苹果暂时用拖动时暂停播放来规避
				// ios pause music when dragging , Android could still play
				audioContext.seek(e.detail.value);
				// pause music when dragging , in order to load timer repeatly
				if (this.system == 'ios') {
					this.audioCurTime[0] = Math.floor(Math.floor(e.detail.value) / 60);
					this.audioCurTime[1] = Math.floor(e.detail.value) % 60;
					clearInterval(this.timer)
					clearInterval(this.rotateTimer)
					this.timer = null
					this.rotateTimer = null
					this.isPlay = false
					audioContext.pause();
				} else {
					clearInterval(this.timer)
					clearInterval(this.rotateTimer)
					this.timer = null
					this.rotateTimer = null
					this.isPlay = false;
					this.playMusic()
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	page {
		background-color: #F8F8F8;
	}
	
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	
	.play-list-content {
		width: 100%;
		background-color: #fff;
		padding: 36rpx 52rpx 28rpx 52rpx;
		border-bottom: 2rpx solid #EDEDED;
	}
	
	.play-list-content-title {
		font-size: 28rpx;
		margin-bottom: 16rpx;
	}
	
	.play-list-content-desc {
		font-size: 24rpx;
		color: #9599A2;
	}
	
	.play-list-content-desc text:nth-child(2) {
		margin: 0 10rpx;
	}
	
	.text-underline {
		width: 140rpx;
		height: 6rpx;
		background-color: #9ECAC0;
		border-radius: 10rpx;
		position: absolute;
		bottom: 2rpx;
	}
	
	.play-list {
		margin: 20rpx 10rpx;
		font-size: 36rpx;
		color: #343434;
		padding: 20rpx;
	}
	
	.player-box {
		height: 280rpx;
		margin: 30rpx;
		width: 100%;
		background-color: #fff;
		width: 95%;
		padding: 20rpx;
		box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
	}
	
	.player-videodisc {
		position: relative;
		width: 590rpx;
		height: 590rpx;
	}
	
	.player-videodisc image {
		width: 590rpx;
		height: 590rpx;
	}
	
	.player-slider {
		width: 650rpx;
		height: 50rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	
	.slider {
		flex: 1;
	}
	
	.play-bar {
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0 70rpx;
		font-size: 30px;
		margin-top: 40rpx;
	}
	
	.play-menu {
		width: 100rpx;
		height: 100rpx;
		border: 1px solid #FFFFFF;
		border-radius: 50%;
		text-align: center;
		line-height: 100rpx;
	}
	
</style>
