<template>
  <view>
    <u-cell-group>
      <u-cell-item
        v-for="(i, j) in starList"
        :key="j"
        :title="i.name"
        :value="i.type"
        :arrow="false"
        @click="openDetail(i)"
      ></u-cell-item>
    </u-cell-group>
	<app-update></app-update>
  </view>
</template>

<script>
import AppUpdate from '@/components/app-update/app-update.vue'
import db from "../../utils/database.js";
export default {
  data() {
    return {
      starList: [],
    };
  },
  methods: {
    openDetail(item) {
      const site = item.key.split("-")[0];
      const id = item.key.split("-")[1];
      const url = `/pages/detail/detail?site=${site}&id=${id}`;
      this.$u.route({ url: url });
    },
    async getAllStar() {
      const res = await db.getAll("star");
      if (res.flag) {
        this.starList = res.data;
      }
    },
  },
  onLoad() {
    this.getAllStar();
  },
  onTabItemTap() {
    this.getAllStar();
  }
};
</script>
