<template>
	<view class="center bg-white">
				<view class="status"></view>
				<view class="main">
					<image class="logo" src="../../static/images/jm.png" mode="aspectFit"></image>
					
					<view class="search">
						<input
						confirm-type='search'
						class="input"
						type="text" 
						:value='search'
						placeholder='请输入影片名'
						@input="inputEvent"
						@confirm="searchEvent"
						/>
						<text v-if="search" class="search-icon text-gray cuIcon-roundclose " @tap="searchClearEvent"></text>
						<text class="search-icon cuIcon-search" @tap="searchEvent"></text>
					</view>
					<view class="site-box">
						<view class="site" v-for="(item, index) in siteLists" :key="index" >
							<image class="site-img" :src="item.image"></image>
							<view class="site-name">
								{{item.title}}
							</view>
						</view>
					</view>
					<view class="padding-top-xl">
						<text class="text-bold">目前只支持搜索以上网站影视资源搜索</text>
					</view>
				</view>
		<view class="box-vip">
			<view class="itemes bg-white " v-for="(item, index) in flowList" :key="index" @tap="govipdetaill(item)">
				<view class="left"><image :src="item.img" mode="aspectFill"></image></view>
				<view class="flex flex-direction padding-left-df">
					<rich-text class="padding-left-sm" :nodes="item.type"></rich-text>
					<view class="right">
						<text class="padding-top-xl padding-left-sm text-red">{{item.mask}}</text>
					</view>
				</view>
			</view>
		</view>
		<!-- <u-loadmore
		     bg-color="#f8f8f8"
		     :status="loadStatus"
		     @loadmore="addData"
		   ></u-loadmore> -->
		
		 <u-back-top :scroll-top="scrollTop" icon="search"></u-back-top>
		 <u-top-tips ref="uTips"></u-top-tips>
		 <u-mask :show="mask"  >
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
	      search: "",
	      site: 0,
	      siteShow: false,
	      siteList: [{'key':'https://m.v.qq.com/search.html',name:'腾讯'},
		  {'key':'https://search.youku.com/api/search',name:'优酷'},
		  {'key':'https://search.video.iqiyi.com/o',name:'爱奇艺'},
		  {'key':'https://api.bilibili.com/x/web-interface/search/type',name:'哔哩哔哩'},
		  ],
	      flowList: [],
	      loadStatus: "loadmore",
	      scrollTop: 0,
	      pageCount: 0,
	      recordcount: 0,
	      mask: false,
		  siteLists:[{
		  					'title': '爱奇艺',
		  					'image': '../../static/icon/aiqiyi.jpg',
		  					'src': 'https://m.iqiyi.com/'
		  				},{
		  					'title': '腾讯视频',
		  					'image': '../../static/icon/tenxun.jpeg',
		  					'src': 'https://m.v.qq.com/index.html'
		  				},{
		  					'title': '优酷',
		  					'image': '../../static/icon/youku.jpg',
		  					'src': 'https://www.youku.com/'
		  				},
						{
							'title': 'B站',
							'image': '../../static/icon/bilibili.png',
							'src': 'https://www.youku.com/'
						},
		  				
		  				
		  			]
	     
		  
	    };
	  },
	  methods: {
		  govipdetaill(e){
			
			  if(e.id==0){
				const  url='/pages/vip/tengxun?url='+encodeURIComponent(e.url)+'&img='+encodeURIComponent(e.img) 
				   this.$u.route({ url: url }); 
			  }else if(e.id==1){
				 
				  const  url='/pages/vip/youku?url='+encodeURIComponent(e.url)+'&img='+encodeURIComponent(e.img)+'&showid='+encodeURIComponent(e.showId)
				     this.$u.route({ url: url }); 
			  }else if(e.id==2){
				  
				const  url='/pages/vip/qiyi?url='+encodeURIComponent(e.url)+'&img='+encodeURIComponent(e.img) 
				   this.$u.route({ url: url }); 
				  
			  }else if(e.id==3){
				  
				const  url='/pages/vip/bilibili?url='+JSON.stringify(e.url)+'&img='+encodeURIComponent(e.img) 
				   this.$u.route({ url: url }); 
				  
			  }
			 
		  },
		  goStar(){
			  
			  const url = `/pages/star/star`;
			  this.$u.route({ url: url }); 
		  },
		  goDetail(){
			  
		  },
		 inputEvent(e){
			this.search=e.detail.value 
		 },
	    async searchEvent() {
			uni.hideKeyboard()
	      if (this.search === '') {
	        return false
	      }
		 
	      this.flowList = []
		  this.getSite()
	    },
	    async searchClearEvent() {
	      this.search = ''
		  this.flowList = []
	     
	    },
	    
	    openDetail(item) {
	      const url = `/pages/detail/detail?site=${this.site.key}&id=${item.id}`;
	      this.$u.route({ url: url });
	    },
	    async getSite() {
		  this.mask = true
	      await this.addData(this.siteList[0].key, this.pageCount,this.search)
		  await this.addDataqiyi(this.siteList[2].key, this.pageCount,this.search)
		  await this.getyouku(this.siteList[1].key, this.pageCount,this.search)
		  await this.getbilibili(this.siteList[3].key, this.pageCount,this.search)
			this.mask = false
	    },
	    async addData(key, page, t) {
			
	   await  http.getT(key,{act:0,keyWord:t}).then(res=>{
			 const doc= new HTMLParser(res.toString().trim())
			
			 const pluginListText=doc.getElementsByClassName('search_item');
			
			 pluginListText.forEach(e=>{
				 var listdata={
					 id:'0',
					 url:'',
					 img:'',
					 name:'',
					 type:'',
					 mask:'',//更新状态
				 }
				const tengxun=new HTMLParser(e.outerHTML)
				var url= tengxun.getElementsByClassName('episode_item').length>0?tengxun.getElementsByClassName('episode_item'):'';
				if(url.length>0){					
					var urls=new HTMLParser(url[url.length-1].outerHTML).getElementsByTagName('a')
					if(urls[0].innerHTML=='全部'){
							listdata.url=urls[0].attributes.href
							
					  }
					var src= tengxun.getElementsByTagName('img').length>0?tengxun.getElementsByTagName('img'):'';
					src.forEach(e=>{
						if(e.attributes.alt==''){
							listdata.img=e.attributes.src
						}
					})
					listdata.name=tengxun.getElementsByClassName('hl').length>0?tengxun.getElementsByClassName('hl')[0].innerHTML:'';	
					listdata.type=tengxun.getElementsByClassName('figure_info').length>0?tengxun.getElementsByClassName('figure_info')[0].outerHTML:'';
					listdata.mask=tengxun.getElementsByClassName('mask_txt').length>0?tengxun.getElementsByClassName('mask_txt')[0].innerHTML:'';
					 this.flowList.push(listdata)
				}
				 
			 })
			
		 })
	     } ,
		 async addDataqiyi(key, page, t) {
		 			
		 await  http.getT(key,{if:'mobile',pageNum:'1',pageSize:'20',video_allow_3rd:'1',intent_category_type:'1',intent_result_number:'10',key:t}).then(res=>{
			 if(res.docinfos){
				 const classToHide = ['优酷', '腾讯']
				 		 			 res.docinfos.forEach(e=>{
				 						
				 						 if (!classToHide.includes(e.albumDocInfo.siteName)&&e.albumDocInfo.stragyTime) {
				 							 
				 						  var listdata={
				 						  	 id:'2',
				 						  	 url:'',
				 						  	 img:'',
				 						  	 name:'',
				 						  	 type:'',
				 						  	 mask:'',//更新状态
				 						  	 
				 						   } 
				 						   listdata.url=e.albumDocInfo.albumId
				 						    listdata.img=e.albumDocInfo.albumImg
				 						    var text=`<p>
				 						  				<strong>${e.albumDocInfo.albumTitle?e.albumDocInfo.albumTitle:''}</strong>
				 						  			</p>
				 						  			<p>
				 						  				${e.albumDocInfo.releaseDate?e.albumDocInfo.releaseDate:''} 
				 						  				| ${e.albumDocInfo.region?e.albumDocInfo.region:''} 
				 						  				
				 						  			</p>
				 						  			<p>
				 						  				导演：${e.albumDocInfo.director?e.albumDocInfo.director:''}
				 						  			</p>
				 						  			<p>
				 						  				主演：${e.albumDocInfo.star?e.albumDocInfo.star:''}
				 						  			</p>
				 						  			<p>
				 						  				来源：${e.albumDocInfo.siteName?e.albumDocInfo.siteName:''}
				 						  			</p>
				 						  			<p>
				 						  				<br />
				 						  			</p>
				 						  			<p>
				 						  				<br />
				 						  			</p>`
				 						      listdata.type=text
				 						  	listdata.mask=e.albumDocInfo.stragyTime
				 						  this.flowList.push(listdata) 
				 						 }
				 		 			 })
			 }else{
				 return
			 }
			  
		 			
		 		 })
		   } ,
		async   getyouku(key, page, t){
			   await  http.getT(key,{keyword:t}).then(res=>{
				  if(res.pageComponentList){
					  
					   const classToHide = ['爱奇艺', '腾讯']
					  res.pageComponentList.forEach(e=>{
					  							 if(e.commonData && !classToHide.includes(e.commonData.sourceName)){
					               var listdata={
					  									 id:'1',
					  									 url:'',
					  									 img:'',
					  									 showId:'',
					  									 type:'',
					  									 mask:'',//更新状态
					  								}
					  								listdata.url=e.commonData.titleDTO.displayName
					  								listdata.showId=e.commonData.showId
					  								listdata.img=e.commonData.posterDTO.vThumbUrl
					  								var text=`<p>
					  											<strong>${e.commonData.titleDTO.displayName?e.commonData.titleDTO.displayName:''}</strong>
					  										</p>
					  										<p>
					  											${e.commonData.feature?e.commonData.feature:''} 
					  											
					  											
					  										</p>
					  										<p>
					  											导演：''
					  										</p>
					  										<p>
					  											${e.commonData.director?e.commonData.director:''}
					  										</p>
					  										<p>
					  											来源：${e.commonData.sourceName?e.commonData.sourceName:''}
					  										</p>
					  										<p>
					  											<br />
					  										</p>
					  										<p>
					  											<br />
					  										</p>`
					  								  listdata.type=text
					  								listdata.mask=e.commonData.updateNotice?e.commonData.updateNotice:e.commonData.stripeBottom
					  								 this.flowList.push(listdata) 
					  							 }
					  			   				
					  			   				})
				  }else{
					  return
				  }
			   			 
			   				 
			   			 })
			   			
			    
		   },
		   async   getbilibili(key, page, t){
		   	   await  http.getT(key,{keyword:t,search_type:'media_bangumi',order:'totalrank'}).then(res=>{
		   		  if(res.data.result){
					  res.data.result.forEach(e=>{
					  							
					  		   					 if(e.eps.length>0&&e.badges.length>0){
					  								 console.log('1')
					         var listdata={
					  		   							 id:'3',
					  		   							 url:'',
					  		   							 img:'',
					  		   							 showId:'',
					  		   							 type:'',
					  		   							 mask:'',//更新状态
					  		   						}
					  		   						listdata.url=e
					  		   						listdata.img=e.cover
					  		   						var text=`<p>
					  		   									<strong>${e.org_title?e.org_title:''}</strong>
					  		   								</p>
					  		   								<p>
					  		   									${e.season_type_name?e.season_type_name:''}|
					  											${e.areas?e.areas:''} 
					  		   									
					  		   									
					  		   								</p>
					  		   								<p>
					  		   									导演：''
					  		   								</p>
					  		   								<p>
					  		   									主演：${e.styles?e.styles:''}
					  		   								</p>
					  		   								<p>
					  		   									来源：哔哩哔哩
					  		   								</p>
					  		   								<p>
					  		   									<br />
					  		   								</p>
					  		   								<p>
					  		   									<br />
					  		   								</p>`
					  		   						  listdata.type=text
					  		   						listdata.mask=e.ep_size?'更新至第'+e.ep_size:'';
					  		   						 this.flowList.push(listdata) 
					  		   					 }
					  		   	   				
					  		   	   				})
				  }else{return}
		   	   			 
		   	   				 
		   	   			 })
		   	   			
		   	    
		      }
		   
		 
	  },
	  onPageScroll(e) {
	    this.scrollTop = e.scrollTop;
	  },
	  onLoad() {
	    // this.getSite();
	  },
	  // async onReachBottom() {
		 
	  //   if (this.search !== '') {
	  //     this.loadStatus = "nomore";
	  //     return false
	  //   }
	  //   this.loadStatus = "loading";
	  //   this.pageCount--
	  //   await this.addData(this.site.key, this.pageCount, this.type.tid);
	  //   this.loadStatus = "loadmore";
	  // },
	  onTabItemTap() {
		  this.flowList=[]
	    this.search=''
	  }
	};
</script>

<style lang="scss" scoped>
	.content{
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		
	}
	.box-vip{
		padding: 20upx 20upx;
		width: 100%;
		.itemes{
			width: 100%;
			height:400upx ;
			display: flex;
			flex-direction: row;
			.left image{
				width: 250upx;
				height: 380upx;
				
			}
		}
	}
	.status{
		width: 100%;
		// height: var(--status-bar-height);
	}
	.main{
		width: 100%;
		height: 100%;
		padding: 0 25upx;
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.logo{
		margin-top: 300upx;
		width: 500upx;
		height: 200upx;
		
	}
	.search{
		margin-top: 40upx;
		width: 560upx;
		height: 90upx;
		border: 1px solid #333333;
		border-radius: 20px;
		display: flex;
		align-items: center;
		justify-content: space-around;
		padding: 10upx 35upx;
	}
	.input{
		flex: 1;
		height: 100%;
		margin: 0 20upx;
		font-size: 18px;
	}
	.search-icon{
		width: 56upx;
		height: 56upx;
		line-height: 56upx;
		font-size: 28px;
		font-weight: 700;
	}
	.site-box{
		margin-top: 25upx;
		width: 100%;
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}
	.site{
		width: 120upx;
		text-align: center;
		align-items: center;
		
		margin-top: 30upx;
		margin-left: 18upx;
	}
	.site-img{
		width: 100upx;
		height: 100upx;
		border-radius: 50%;
		border: 1px solid #eeeeee;
	}
	.site-name{
		margin-top: 6upx;
	}
	.mask{
	  width: 100%;
	  height: 100%;
	  display: flex;
	  justify-content: center;
	  align-items: center;
	}
</style>
