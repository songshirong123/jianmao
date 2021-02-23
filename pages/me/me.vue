<template>
  <view class="me">
    <view class="status_bar"></view>
    <view class="logo">
      <img src="static/images/logo.png" alt="" />
    </view>
    <view class="name"> 简猫影视 APP </view>
    <u-cell-group>
			<u-cell-item
        title="播放记录"
        value="记录播放历史"
        :arrow="false"
        @click="openHistoryPage()"
      ></u-cell-item>
			<u-cell-item
        title="视频源管理"
        value="管理视频源数据"
        :arrow="false"
        @click="siteEdite()"
      ></u-cell-item>
			<u-cell-item
        title="清空播放记录"
        value="清空播放记录数据"
        :arrow="false"
        @click="clearHistory()"
      ></u-cell-item>
      <u-cell-item
        title="清空收藏夹"
        value="清空收藏夹数据"
        :arrow="false"
        @click="clearStar()"
      ></u-cell-item>
      <u-cell-item
        title="重置软件"
        value="清空所有数据, 慎点"
        :arrow="false"
        @click="resetApp()"
      ></u-cell-item>
	  <u-cell-item
	    title="打赏"
	    value="欢迎打赏蚊子也是肉"
	    :arrow="false"
	    @click="dashangApp()"
	  ></u-cell-item>
	  <u-cell-item
	    title="交流群"
	    value="app更新及订制请加群"
	    :arrow="false"
	    @click="jiaoliu()"
	  ></u-cell-item>
      <u-cell-item
        title="版本更新"
        value="0.1.2"
        :arrow="false"
        @click="itemClickEvent(i)"
      ></u-cell-item>
    </u-cell-group>
    <view class="tips">
      所有资源来自网上, 该软件不参与任何制作, 上传, 储存等内容,
      禁止传播违法资源. 该软件仅供学习参考, 请于安装后24小时内删除.
	  若本站收录的节目无意侵犯了贵司版权，请给网页底部邮箱地址来信,我们会及时处理和回复,谢谢。
		管理员邮箱：songshiyun123@gmail.com
    </view>
		<u-toast ref="uToast" />
		<u-modal v-model="modalShow" :content="modalContent" @confirm="resetAppconfirm" :mask-close-able="true" show-cancel-button @cancel="resetAppCancel"></u-modal>
		<app-update></app-update>
  </view>
</template>

<script>
	import AppUpdate from '@/components/app-update/app-update.vue'
import db from "../../utils/database.js";
export default {
  data() {
    return {
			modalShow: false,
			modalContent: ''
		};
  },
  methods: {
		openHistoryPage () {
			this.$u.route({ url: '/pages/history/history' });
		},
		siteEdite () {
      this.$u.route({ url: '/pages/site/site' });
		},
		async clearHistory () {
			const res = await db.removeAll('history')
			if (res.flag) {
				this.$refs.uToast.show({ title: '清空播放数据成功', type: 'success', duration: '2300' })
			} else {
				this.$refs.uToast.show({ title: '清空播放数据失败', type: 'warning', duration: '2300' })
			}
		},
    async clearStar() {
			const res = await db.removeAll('star')
			if (res.flag) {
				this.$refs.uToast.show({ title: '清空收藏夹数据成功', type: 'success', duration: '2300' })
			} else {
				this.$refs.uToast.show({ title: '清空收藏夹数据失败', type: 'warning', duration: '2300' })
			}
		},
		async resetApp() {
			this.modalContent = '重置软件会清空：收藏夹数据，历史记录，导入的视频源等。恢复默认设置，以及默认的视频源。'
			this.modalShow = true
		},
		dashangApp(){
			
			 this.$u.route({ url: '/pages/type/dashang' });
		},
		jiaoliu(){
			 this.$u.route({ url: '/pages/type/jiaoliu' });
		},
		async resetAppconfirm () {
			await db.clearDB()
			await db.reset('site')
			await db.reset('setting')
			this.modalShow = false
			this.$refs.uToast.show({ title: '软件重置成功', type: 'success', duration: '2300' })
			plus.runtime.restart()
		},
		resetAppCancel () {
			this.modalShow = false
		},
    itemClickEvent(item) {},
  },
};
</script>

<style lang="scss" scoped>
.me {
  .status_bar {
    height: var(--status-bar-height);
    width: 100%;
  }
  .logo {
    margin-top: 20px;
    width: 100%;
    text-align: center;
    img {
      width: 26%;
    }
  }
  .name {
    margin-top: 10px;
    text-align: center;
    font-size: 20px;
    margin-bottom: 10px;
  }
  .tips {
    color: #ff4445;
    padding: 15px;
  }
}
</style>
