<template>
  <view class="film">
	  <scroll-view scroll-x class="bg-white nav" scroll-with-animation :scroll-left="scrollLeft">
	  	<view 
	  	class="cu-item" 
	  	:class="index==TabCur?'text-green cur':''" 
	  	v-for="(item,index) in typeList" 
	  	:key="index" 
	  	:data-id="item.tid" :data-index="index"
	  	@tap="typeConfirm" 
	  	>
	  		{{item.name}}
	  	</view>
	  </scroll-view>
	  <!-- 影片列表 -->
	  <swiper class="card-swiper square-dot" :indicator-dots="true" :circular="true"
	   :autoplay="true" interval="5000" duration="500"  indicator-color="#8799a3"
	   indicator-active-color="#0081ff">
	  	<swiper-item v-for="(item,index) in swiperList" :key="index" :class="cardCur==index?'cur':''">
	  		<view class="swiper-item" >
	  			<image :src="item.url" mode="aspectFill" v-if="item.type=='image'" ></image>
	  			<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video>
	  		</view>
	  	</swiper-item>
	  </swiper>
	  <!-- 简闻 -->
	  <view class="simple-news-title">
	  	<view class="news-title">
	  		 简闻
	  	</view>
	  	<view class="news-content">
	  		<view class="news-type">
	  			 热议
	  		</view>
	  		<view class="news-msg">
	  			VIP视频免费观看
	  		</view>
	  	</view>
	  	<view class="more">
	  		<text class="text-gray cuIcon-right"></text>
	  	</view>
	  </view>
	  <view class="">
		  <u-search
		    v-model="search"
		    @search="searchEvent"
		    @custom="searchEvent"
		    :clearabled="true"
		    @clear="searchClearEvent"
		  ></u-search>
	  </view>
	  <view class="flex flex-wrap padding-sm justify-between">
		  <view class="">
			  <u-button @click="openSiteSelect" size="mini"
			    >线路: {{ site.name }}</u-button
			  >
		  </view>
		  
		  <text class="text-red">影片数量: {{recordcount}} 部</text>
		  <view class="" @tap="goStar"><text class="lg text-green cuIcon-favor">我的收藏</text></view>
	  </view>
	  <view class="">
		  <button class="cu-btn sm margin-right-sm" :class="buttonindex==0?'bg-green':''"  @click="updateIndex(0)" >全部</button>
		  <button class="cu-btn sm margin-right-sm" :class="buttonindex==24?'bg-green':''" @click="updateIndex(24)" >今日更新</button>
		  <button class="cu-btn sm margin-right-sm" :class="buttonindex==72?'bg-green':''" @click="updateIndex(72)" >三日更新</button>
		  <button class="cu-btn sm margin-right-sm" :class="buttonindex==168?'bg-green':''"  @click="updateIndex(168)">七日更新</button>
	  </view>
	   <view class="video-list">
	   	<view class="single-video" v-for="(item, index) in flowList" :key="index">
	   		<image :src="item.pic" mode="" @tap="openDetail(item)"></image>
	   		<view class="video-name">
	   			 {{item.name}}
	   		</view>
			<view class="box-info">
			  <view class="box-class" >{{ item.type }}</view>
			  <view class="text-red">{{ item.note }}</view>
			  <view class="box-year">{{ item.year }}</view>
			</view>
	   	</view>
	  	
	  </view>
 <!--  <view class="header">
      <u-search
        v-model="search"
        @search="searchEvent"
        @custom="searchEvent"
        :clearabled="true"
        @clear="searchClearEvent"
      ></u-search>
    </view> -->
   <!-- <view class="btns">
      <u-button @click="openSiteSelect" size="mini"
        >网站: {{ site.name }}</u-button
      >
      <u-button @click="openTypeSelect" size="mini"
        >分类: {{ type.name }}</u-button
      >
      <text>共: {{recordcount}} 资源</text>
    </view> -->
    <!-- <view class="body">
      <u-waterfall v-model="flowList" ref="uWaterfall">
        <template v-slot:left="{ leftList }">
          <view
            class="box-warter left-box-warter"
            v-for="(item, index) in leftList"
            :key="index"
            @click="openDetail(item)"
          >
            <u-lazy-load
              border-radius="4"
              :image="item.pic"
              :index="index"
            ></u-lazy-load>
            <view class="box-name">{{ item.name }}</view>
            <view class="box-info">
              <view class="box-class">{{ item.type }}</view>
			  <view class="box-class">{{ item.note }}</view>
              <view class="box-year">{{ item.year }}</view>
            </view>
          </view>
        </template>
        <template v-slot:right="{ rightList }">
          <view
            class="box-warter right-box-warter"
            v-for="(item, index) in rightList"
            :key="index"
            @click="openDetail(item)"
          >
            <u-lazy-load
              threshold="-450"
              border-radius="4"
              :image="item.pic"
              :index="index"
            ></u-lazy-load>
            <view class="box-name">{{ item.name }}</view>
            <view class="box-info">
              <view class="box-class">{{ item.type }}</view>
              <view class="box-year">{{ item.year }}</view>
            </view>
          </view>
        </template>
      </u-waterfall> -->
      <u-loadmore
        bg-color="#f8f8f8"
        :status="loadStatus"
        @loadmore="addData"
      ></u-loadmore>
   <!-- </view> -->
    <u-back-top :scroll-top="scrollTop" icon="search"></u-back-top>
   <u-select
      v-model="siteShow"
      :list="siteList"
      value-name="key"
      label-name="name"
      @confirm="siteConfirm"
    ></u-select>
    <u-top-tips ref="uTips"></u-top-tips>
    <u-mask :show="mask"  @tap.stop>
      <view class="mask">
        <u-loading mode="flower" size="80"></u-loading>
      </view>
    </u-mask>
    <u-toast ref="uToast" />
	<app-update></app-update>
  </view>
</template>

<script>
	import AppUpdate from '@/components/app-update/app-update.vue'
import db from "../../utils/database.js";
import http from "../../utils/request.js";
export default {
  data() {
    return {
		buttonindex:0,
		cardCur:'0',
      search: "",
      site: {},
      siteShow: false,
      siteList: [],
      type: {},
      typeShow: false,
      typeList: [],
      flowList: [],
      loadStatus: "nomore",
      scrollTop: 0,
      pageCount: 0,
      recordcount: 0,
      mask: false,
	  h:'',
      r18KeyWords: ['伦理', '论理', '倫理', '福利', '激情', '理论', '写真', '情色', '美女', '街拍', '赤足', '性感', '里番'],
	  
	  	TabCur: 0,
	  	scrollLeft: 0,
	  	swiperList: [{
	  			id: 0,
	  			type: 'image',
	  			url: 'https://1img.hitv.com/preview/cms_icon/2020/1/4/01/20200104155613840.jpg_2048x550.jpg'
	  		}, {
	  			id: 1,
	  			type: 'image',
	  			url: 'https://3img.hitv.com/preview/cms_icon/2020/1/26/01/20200126154228745.jpg_2048x550.jpg',
	  		}, {
	  			id: 2,
	  			type: 'image',
	  			url: 'https://3img.hitv.com/preview/cms_icon/2020/1/26/01/20200126154228745.jpg_2048x550.jpg'
	  		}
	  	],	
	  				
	  
    };
  },
  methods: {
	async  updateIndex(e){
		  if(e==0){
			this.h='' 
		  }else{
			 this.h=e 
		  }
		  
		  this.buttonindex=e
		 this.mask = true
		 this.flowList = []
		 await this.getPage(this.type.tid)
		 await this.addData(this.site.key, this.pageCount, this.type.tid)
		
		 this.mask = false
	  },
	  goStar(){
		 
		  const url = `/pages/star/star`;
		  this.$u.route({ url: url }); 
	  },
	  goDetail(){
		  
	  },
	 
    async searchEvent() {
      if (this.search === '') {
        return false
      }
      this.mask = true
      this.flowList = []
      const res = await http.search(this.site.key, this.search)
      if (res.length >= 1) {
        for (const i of res) {
          const data = await http.detail(this.site.key, i.id)
          this.flowList.push(data);
        }
      }
      this.mask = false
    },
    async searchClearEvent() {
      this.search = ''
      this.mask = true
      this.flowList = []
      await this.getPage(this.type.tid)
      await this.addData(this.site.key, this.pageCount, this.type.tid)
      // this.pageCount--
      // await this.addData(this.site.key, this.pageCount, this.type.tid)
      this.mask = false
    },
    async openSiteSelect() {
      this.siteShow = true;
      const site = await db.get("site", this.site.key);
    },
    async siteConfirm(e) {
      this.mask = true
	  this.pageCount = 0
	  this.recordcount = 0
	  this.type.tid=''
	  this.TabCur = 0
      this.flowList = []
      const site = await db.get("site", e[0].value);
      this.site = site.data;
      await this.getPage()
      await this.getClass(this.site.key)
      await this.addData(this.site.key, this.pageCount)
      // this.pageCount--
      // await this.addData(this.site.key, this.pageCount)
      this.mask = false
    },
    openTypeSelect () {
      this.typeShow = true
    },
    async typeConfirm(e) {
		
      this.mask = true
      this.flowList = []
      this.type.tid=e.target.dataset.id
	  this.TabCur = e.currentTarget.dataset.index;
	  this.scrollLeft = (e.currentTarget.dataset.index - 1) * 60
      await this.getPage(e.target.dataset.id)
      await this.addData(this.site.key, this.pageCount, e.target.dataset.id)
      // this.pageCount--
      // await this.addData(this.site.key, this.pageCount, e.target.dataset.id)
      this.mask = false
    },
    openDetail(item) {
      const url = `/pages/detail/detail?site=${this.site.key}&id=${item.id}`;
      this.$u.route({ url: url });
    },
    async getSite() {
      const res = await db.getAll("site");
      if (res.flag) {
        const arr = []
        for (const i of res.data) {
          if (i.isActive) {
            arr.push(i)
          }
        }
        this.siteList = arr
      } else {
        this.$refs.uToast.show({ title: '读取视频源出错', type: 'warning', duration: '2300' })
        return false
      }
      this.site = this.siteList[0]
      await this.getPage()
      await this.getClass(this.site.key)
      await this.addData(this.site.key, this.pageCount)
      // this.pageCount--
      // await this.addData(this.site.key, this.pageCount)
    },
    async getPage (type) {
      const res = await http.page(this.site.key, type,this.h)
      this.pageCount = res.pagecount
      this.recordcount = res.recordcount
    },
    async getClass(key) {
      this.typeList = [{ name: '最新', tid: 0 }]
      this.type = { name: '最新', tid: 0 }
      http.class(key).then(res => {
        // 屏蔽主分类
        const classToHide = ['电影', '电影片', '电视剧', '连续剧', '综艺', '动漫']
		
        res.forEach(ele => {
          if (!classToHide.includes(ele.name)) {
            this.typeList.push(ele)
          }
        })
      })
    },
    async addData(key, page, t) {
	if(this.recordcount<=this.flowList.length){
		console.log('123')
		this.loadStatus='nomore'
		return false
	}
      const res = await http.list(this.site.key, this.pageCount, this.type.tid,this.h);
	  
      const config = await db.get('setting', 'config')
	 
	  if(res.length==undefined&&this.recordcount>this.flowList.length){
		  this.flowList.push(res);
	  }
      for (let i = 0; i < res.length; i++) {
        let item = res[i];
        if (config.data.R18) {
          const j = this.r18KeyWords.some(v => item.name.includes(v))
          const k = this.r18KeyWords.some(v => item.type.includes(v))
          if (j !== true && k !== true) {
            this.flowList.push(item);
          }
        } else {
          this.flowList.push(item);
        }
      }
	 
      if (this.flowList.length <= 6 && this.pageCount>0&&this.recordcount>this.flowList.length) {
		  
        this.pageCount--;
        this.addData(key, this.pageCount, t);
      }
    },
	async getSettingConfig () {
	  const res = await db.get('setting', 'config')
	  if(!res.data.login){
		  uni.reLaunch({
		  	url:'../login/login'
		  })
	  		 // const url = '/pages/login/login'
	  		 // this.$u.route({ url: url });
	  		 
	  }else{
		  this.getSite();
	  }
	}
  },
  onPageScroll(e) {
    this.scrollTop = e.scrollTop;
  },
  
  onLoad() {
	  this.getSettingConfig()
	 
    
  },
  async onReachBottom() {
	 
    if (this.search !== '') {
      this.loadStatus = "nomore";
      return false
    }
    this.loadStatus = "loading";
    this.pageCount--
    await this.addData(this.site.key, this.pageCount, this.type.tid);
    this.loadStatus = "loadmore";
  },
  onTabItemTap() {
	 
    // this.getSite();
  }
};
</script>

<style lang="scss" scoped>
.film {
  padding: 20rpx 20rpx;
  background-color: #f8f8f8;
  
  .mask{
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .video-list{
  	width: 100%;
  	display: flex;
  	flex-wrap: wrap;
  	align-items: flex-start;
  	background-color: #FFFFFF;
  }
  .single-video{
	width: 30.33%;
  	display: flex;
	
  	flex-direction: column;
  	justify-content: center;
  	margin: 10upx;
  }
  .single-video image{
  	width: 100%;
  	height: 320upx;
  	border-radius: 10px;
  	margin-top: 10upx;
  }
  .single-video .video-name{
  	width: 210upx;
  	font-size: 16px;
  	white-space: nowrap;
  	overflow: hidden;
  	text-overflow: ellipsis;
  	margin-top: 5upx;
  	margin-left: 5upx;
  }
  .box-info {
        margin-top: 10rpx;
        display: flex;
		flex-wrap: wrap;
		align-items: center;
        // justify-content: space-between;
        font-size: 12px;
      }
	  .simple-news-title{
	  	width: 100%;
	  	height: 45px;
	  	background-color: #FFFFFF;
	  	display: flex;
	  	align-items: center;
	  }
	  .simple-news-title .news-title{
	  	width: 44px;
	  	font-size: 22px;
	  	color: #333333;
	  	margin: 0 10px;
	  }
	  .simple-news-title .news-content{
	  	flex: 1;
	  	display: flex;
	  	align-items: center;
	  }
	  .simple-news-title .news-content .news-type{
	  	width: 30px;
	  	color: #FF0000;
	  	border: 1px solid #ff0000;
	  	border-radius: 8px;
	  }
	  .simple-news-title .news-content .news-msg{
	  	width: 440upx;
	  	margin-left: 5px;
	  	color: #8b8b8b;
	  	white-space: nowrap;
	  	overflow: hidden;
	  	text-overflow: ellipsis;
	  }
	  .simple-news-title .more{
	  	font-size: 20px;
	  	margin-right: 10px;
	  }
  
}
</style>
