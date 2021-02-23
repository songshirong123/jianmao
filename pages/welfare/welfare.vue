<template>
  <view class="film">
	  <scroll-view scroll-x class="bg-white nav" scroll-with-animation :scroll-left="scrollLeft">
	  	<view 
	  	class="cu-item" 
	  	:class="index==TabCur?'text-green cur':''" 
	  	v-for="(item,index) in typeList" 
	  	:key="index" 
	  	:data-id="item.id" :data-index="index"
	  	@tap="typeConfirm" 
	  	>
	  		{{item.name}}
	  	</view>
	  </scroll-view>  
	<view class="" v-for="(item, index) in flowList" :key="index">
		<view class="cu-card dynamic ">
			<view class="cu-item shadow ">
				<view class="cu-list menu-avatar">
					<view class="cu-item">
						<view class="content flex-sub text-bold">
							<view>{{item.name}}</view>
						</view>
					</view>
				</view>
				<view class="text-content">
					{{item.centent}}
				</view>
			</view>
		</view>
			
	</view>
      <u-loadmore
        bg-color="#f8f8f8"
        :status="loadStatus"
        @loadmore="addData"
      ></u-loadmore>
   <!-- </view> -->
    <u-back-top :scroll-top="scrollTop" icon="search"></u-back-top>

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
import HTMLParser from "@/common/HTMLParser/html-parser.js"
export default {
  data() {
    return {
		buttonindex:0,
	
      typeList: [{id:0,
	  name:'羊毛党'
	  }],
      flowList: [],
      loadStatus: "nomore",
      scrollTop: 0,
      pageCount: 0,
      recordcount: 0,
      mask: false,
	  h:'',
	  	TabCur: 0,
	  	scrollLeft: 0,
	  	
	  
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
	 
    
    async openSiteSelect() {
      this.siteShow = true;
      const site = await db.get("site", this.site.key);
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
      
      // this.site = this.siteList[0]

      await this.addData()
      // this.pageCount--
      // await this.addData(this.site.key, this.pageCount)
    },
    async getPage (type) {
      const res = await http.page(this.site.key, type,this.h)
      this.pageCount = res.pagecount
      this.recordcount = res.recordcount
    },
    
    async addData(key, page, t) {
		this.mask = true
      const res = await http.getT('http://www.aishoujizy.com/i_wz_62551.html');
	 
	  const doc= new HTMLParser(res.toString().trim())
	  const pluginListText=doc.getElementsByClassName('post-list');
	  if(pluginListText.length>0){
		  pluginListText.forEach(e=>{
		  	var listdata={
		  		 name:'',
		  		 url:'',
		  		 centent:'',
		  	 }
		  	const tengxun=new HTMLParser(e.outerHTML)
		  	var url= tengxun.getElementsByClassName('post-title');
		  	
		  	const urls=new HTMLParser(url[0].outerHTML).getElementsByTagName('a')
		  	listdata.name=urls[0].innerHTML;
		  	listdata.url=urls[0].attributes.href;
		  	var centent= tengxun.getElementsByClassName('post-excerpt');
			
			if(centent.length>0){
				listdata.centent=centent[0].innerHTML;
			}
			 const classToHide = ['你懂的QQ福利群滴滴滴！']
			 if (!classToHide.includes(listdata.name)) {
			  this.flowList.push(listdata);
			 }
		  	
		  }) 
		  this.mask = false
	  }else{
		this.mask = false
	  }
	  			
      
    },
	
  },
  onPageScroll(e) {
    this.scrollTop = e.scrollTop;
  },
  
  onLoad() {
	  this.getSite()
	 
    
  },
  async onReachBottom() {
    this.loadStatus = "loading";
    await this.addData();
    this.loadStatus = "loadmore";
  },
  onTabItemTap() {
	 
    // this.getSite();
  }
};
</script>

<style lang="scss" scoped>
.film {
  
  background-color: #e3e3e3;
  
  .mask{
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
 
  
}
</style>
