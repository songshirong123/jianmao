<script>
import db from "./utils/database.js";
export default {
  onLaunch: function () {
	  db.clearDB()
    db.init('site')
    db.init('setting')
  },
  onShow: function () {
    // console.log('App Show')
  },
  onHide: function () {
    // console.log('App Hide')
  },
};
</script>

<style lang="scss">
/*每个页面公共css */
@import "uview-ui/index.scss";
@import url('./components/colorui/icon.css');
	@import url('./components/colorui/main.css');
	@import url('./components/colorui/animation.css');
</style>
