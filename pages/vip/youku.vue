<template>
  <view class="detail">
    <view class="status_bar"></view>
    <view class="header">
      <view class="header-icon" @click="BackEvent">
        <u-icon class="icon" name="arrow-left"></u-icon>
      </view>
      <u-select
        class="moreSelect"
        v-model="moreShow"
        :list="moreList"
        @confirm="moreConfirm"
      ></u-select>
      <view class="header-icon" @click="moreShowEvent">
        <u-icon class="icon" name="more-dot-fill"></u-icon>
      </view>
    </view>
    <view class="bgImg">
      <img :src="detail.pic" />
    </view>
    <view class="boxImg">
      <view class="boxImgWrapper">
        <img :src="detail.pic" />
      </view>
    </view>
    <view class="box-info">
      <view class="star-btn" @click="starEvent">
        <u-icon v-if="!starShow" name="star" size="60"></u-icon>
        <u-icon v-if="starShow" name="star-fill" color="#ff4445" size="60"></u-icon>
      </view>
      <view class="play-btn" @click="playEvent">
        <u-icon name="play-circle" color="#6dd143" size="100"></u-icon>
      </view>
      <view class="name-box">
        <text class="name">{{ detail.name }}</text>
      </view>
      <view class="info-box">
        <rich-text :nodes="detail.type"></rich-text>
      </view>
	  <view class="name-box">
	    <text class="name">线路选择：</text>
	  </view>
	  <u-radio-group  size="50"   v-model="value"  activeColor="#2979ff">
	  	<u-radio @change="radioChange(index)" v-for="(item, index) in list" 
	  		:key="index" :name="item.name"
	  	>{{item.name}}</u-radio>
	  </u-radio-group>
	  <view class="info-box" style="color:#dd6161;">
	    <text>*影片无法放映或者卡顿可以尝试切换其它线路</text>
	  </view>
	  <view class="name-box">
	    <text class="name">影片：</text>
	  </view>
	   <view class="libe-index">
		  <block v-for="(item, index) in playList" :key="index">
			  <view class="libe-index-whint">
				  <u-tag :text="item.label" :type="type" :shape="shape" :closeable="closeable" :mode="mode"  @click="playEvent(item)"  :show="show"  />
				  <!-- <rich-text :nodes="item"></rich-text> -->
			  </view>
				
		  </block>
	  </view>
    </view>
    <view>
      <u-select
        v-model="playShow"
        :list="playList"
        confirm-text="播放"
        @confirm="playConfirm"
      ></u-select>
    </view>
    <u-toast ref="uToast" />
	
  </view>
</template>

<script>
import db from "../../utils/database.js";
import http from "../../utils/request.js";
import HTMLParser from "@/common/HTMLParser/html-parser.js"
export default {
  data() {
    return {
		mode: 'dark',
		type: 'primary',
		shape: 'circle',
		closeable: false,
		show: true,
      siteKey: "",
      id: "",
      detail: {},
      moreShow: false,
	  line:0,
	  showid:'',
	  value:'线路1',
	  lineurl:[
	  'https://vip.52jiexi.top/?url=',
	  'https://okjx.cc/?url=',
	  'https://www.playm3u8.cn/jiexi.php?url=',
	  'https://jx.116kan.com/?url=',
	  'http://jsap.attakids.com/?url=',
	  'http://jx.618g.com/?url='
	  ],
	  list: [
	  	{
	  		name: '线路1',
	  		checked: true,
	  		disabled: false
	  	},
	  	{
	  		name: '线路2',
	  		checked: false,
	  		disabled: false
	  	},
	  	{
	  		name: '线路3',
	  		checked: false,
	  		disabled: false
	  	},
		{
			name: '线路4',
			checked: false,
			disabled: false
		},
	  	{
	  		name: '线路5',
	  		checked: false,
	  		disabled: false
	  	},
		{
			name: '线路6',
			checked: false,
			disabled: false
		}
	
	  ],
      moreList: [
        {
          value: "share",
          label: "分享",
        },
      ],
      playShow: false,
      playList: [],
      starShow: false
    };
  },
  methods: {
    BackEvent() {
      uni.navigateBack();
    },
    moreShowEvent() {
      this.moreShow = !this.moreShow;
    },
    async moreConfirm(e) {
      const val = e[0].value;
      if (val === "share") {
      }
      if (val === "star") {
        let s = {...this.detail}
        s.key = `${this.siteKey}-${this.id}`
        const res = await db.add('star', s)
        if (res.flag) {
          this.$refs.uToast.show({
            title: '收藏成功',
            type: 'success',
            duration: '2300'
          })
        }
      }
    },
    async playEvent(e) {
		
		  var urldata=this.lineurl[this.line]+'https://m.youku.com/video/'+e.value;
		 
		  const url = '/pages/play/webplay?url='+encodeURIComponent(urldata);
		  this.$u.route({ url: url });
	 
    },
    async playConfirm(e) {
      const d = e[0];
	  if(this.line==this.lineurl.length){
	  		  const url = `/pages/play/play?site=${d.extra.site}&id=${d.extra.id}&name=${d.label}&url=${d.value}`;
	  		  await this.addHistory(d.label, d.value)
	  		  this.$u.route({ url: url });
	  }else{
	  			  var urldata=this.lineurl[this.line]+'https://m.youku.com/video/'+d.value;
	  			  const url = `/pages/play/webplay?url=${urldata}`;
	  			  this.$u.route({ url: url });
	  		  }

    },
    async addHistory (label, url) {
      let h = {...this.detail}
      h.key = `${this.siteKey}-${this.id}`
      const res = await db.get('history', `${this.siteKey}-${this.id}`)
      if (!res.flag) {
        h.label = label
        h.url = url
        await db.add('history', h)
      }
    },
    async getDetail() {
		
     await http.getT('https://search.youku.com/api/search',{appScene:'show_episode',keyword:this.url,showIds:this.showid,appCaller: 'h5' }).then(res=>{
		
		  const arr = [];
		  for (const i of res.serisesList) {
		    let label = res.serisesList.length > 0 ? `第${i.displayName}集`: ''
		    if (res.serisesList.length > 1) {
		          let d = {
		            value: 'id_'+i.videoId,
		            label: label
		          };
		          arr.push(d);
		    } else {
		      let d = {
		       
		        value:'id_'+i.videoId,
		        label: label,
		        
		      };
		      arr.push(d);
		    }
		  
		  }
		  
		  this.playList = arr.reverse();
		 
	 })
      
    },
    async checkStar () {
      const res = await db.get('starvip', `${this.url}`)
      this.starShow = res.flag
    },
    async removeStar () {
      const res = await db.remove('starvip', `${this.url}`)
      if (res.flag) {
        this.$refs.uToast.show({ title: '移除收藏成功', type: 'success', duration: '1500' })
      } else {
        this.$refs.uToast.show({ title: '移除收藏失败', type: 'warning', duration: '1500' })
      }
      this.checkStar()
    },
    async addStar () {
      let s = {...this.detail}
      s.key = `${this.siteKey}-${this.id}`
      const res = await db.add('star', s)
      if (res.flag) {
        this.$refs.uToast.show({ title: '添加收藏成功', type: 'success', duration: '1500' })
      } else {
        this.$refs.uToast.show({ title: '添加收藏失败', type: 'warning', duration: '1500' })
      }
      this.checkStar()
    },
    starEvent() {
      if (this.starShow) {
        this.removeStar()
      } else {
        this.addStar()
      }
    },
	//播放线路选择
	radioChange(e){
		this.line=e;
		
	}
  },
  onLoad(opt) {
    this.url = decodeURIComponent(opt.url);
	this.showid = decodeURIComponent(opt.showid);
	console.log(this.url)
	console.log(this.showid)
	this.detail.pic=decodeURIComponent(opt.img)
    this.getDetail( );
    this.checkStar()
  },
};
</script>
<style lang="scss" scoped>
.detail {
  .status_bar {
    height: var(--status-bar-height);
    width: 100%;
  }
  .header {
    position: absolute;
    top: var(--status-bar-height);
    left: 0;
    padding: 0 20px;
    height: 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    z-index: 2;
    width: 100%;
    margin-top: 10px;
    .header-icon {
      width: 30px;
      height: 30px;
      background-color: rgba(255, 255, 255, 0.7);
      text-align: center;
      line-height: 30px;
      border-radius: 50%;
    }
  }
  .bgImg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 0;
    filter: blur(3px);
    img {
      width: 100%;
      height: auto;
    }
  }
  .boxImg{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 1;
    .boxImgWrapper{
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 55vh;
      img{
        width: auto;
        height: 40vh;
        margin: 0 auto;
        border: 3px solid #fff;
        box-shadow: 0px 0px 20px #acacac;
      }
    }
  }
  .box-info {
    width: 100%;
    height: auto;
    padding: 40px 30px;
    border-radius: 30px;
    background-color: #FFFFFF;
    position: absolute;
    margin-top: 50vh;
    z-index: 1;
    .star-btn {
      position: absolute;
      top: -60rpx;
      right: 240rpx;
      padding: 20rpx;
      border-radius: 50%;
      background-color: #FFFFFF;
      border: 1px solid #f8f8f8;
    }
    .play-btn {
      position: absolute;
      top: -80rpx;
      right: 100rpx;
      padding: 20rpx;
      border-radius: 50%;
      background-color: #FFFFFF;
      border: 1px solid #f8f8f8;
    }
    .name-box {
      width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      .name {
        font-size: 40rpx;
      }
    }
	
    .info-box {
      margin-top: 20rpx;
      .gap {
        margin: 0 10rpx;
      }
    }
  }
}
.libe-index{
		width: 100%;
		display: flex;
		flex-wrap: wrap;
		flex-direction: row;
		align-items: center;
		justify-content: space-around;
		order: 1;
	}
	.libe-index-whint{
		padding-top: 20rpx;
		padding-right: 20upx;
		padding-bottom: 10rpx;
	}
</style>
