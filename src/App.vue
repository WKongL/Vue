<template>
  <div >
  	<v-header :seller="seller"></v-header>
  	<div class="tab border-1px">
  		<div class="tab-item">
  			<a v-link="{path: '/goods'}">商品</a>
  		</div>
  		<div class="tab-item">
  			<a v-link="{path: '/ratings'}">评价</a>
  		</div>
  		<div class="tab-item">
  			<a v-link="{path: '/seller'}">商家</a>
  		</div>
  	</div>
  	<router-view :seller="seller" keep-alive></router-view>
  </div>
</template>

<script >
	import header from 'components/header/header.vue';
  import {urlParse} from 'common/js/until.js';
	const RES_OK = 0;
	export default {
		data() {
			return {
				seller: {
          id: (() => {
            let queryParam = urlParse();
            return queryParam.id;
          })()
        }
			};
		},
		created() {
			// 获取seller数据
      if (process.env.NODE_ENV === 'development') {
        this.$http.get('/api/seller?id=' + this.seller.id).then((response) => {
          // success
          response = response.body;
          if (response.error === RES_OK) {
            // this.seller = response.data;
            this.seller = Object.assign({}, this.seller, response.data);
          }
        }, (response) => {
          // error
        });
      } else {
        this.$http.get('https://wkongl.github.io/Vue-takeaway/data.json').then((response) => {
          // success
          response = response.body;
          // this.seller = response.data;
          this.seller = Object.assign({}, this.seller, response.seller);
        }, (response) => {
          // error
        });
      }
		},
		components: {
			'v-header': header
		}
	};
</script>

<style rel="stylesheet/stylus" lang="stylus">
  @import  'common/stylus/mixin.styl'
  .tab
    display: flex
    width: 100%
    height: 40px
    line-height: 40px
    // border-bottom: 1px solid rgba(7,17,27,0.1)
    border-1px(rgba(7,17,27,0.1))
    .tab-item
      flex: 1
      text-align: center
      & > a
       display: block
       color: rgb(77, 85, 93)
       font-size: 14px
       &.active
       	color: rgb(240, 20, 20)
</style>
