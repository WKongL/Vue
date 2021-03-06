<template>
	<div class="rating" v-el:ratings>
		<div class="rating-content">
			<div class="overview">
				<div class="overview-left">
					<h1 class="score">{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank">高于周边商家{{seller.rankRate}}%</div>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<span class="title">服务态度</span>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class="title">商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="time-wrapper">
						<span class="title">送达时间</span>
						<span class="time">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<split></split>
			<ratingselect :select-type="selectType" :only-content="onlyContent" :ratings="ratings"></ratingselect>
			<div class="rating-wrapper">
				<ul>
					<li class="rating-item border-1px" v-for="rating in ratings" v-show="ratingShow(rating.rateType, rating.text)">
						<div class="avatar">
							<img :src="rating.avatar">
						</div>
						<div class="content">
							<h1 class="name">{{rating.username}}</h1>
							<div class="star-wrapper">
								<star :size="24" :score="rating.score"></star>
								<span class="delivery-time" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
							</div>
							<p class="text">{{rating.text}}</p>
							<div class="recommend-wrapper">
								<i :class="{'icon-thumb_up':rating.rateType ===0,'icon-thumb_down':rating.rateType===1}"></i>
								<span class="recommend" v-for="item in rating.recommend">{{item}}</span>
							</div>
							<div class="time">{{rating.rateTime | formatDate}}</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
	import star from 'components/star/star';
	import split from 'components/split/split';
	import ratingselect from 'components/ratingselect/ratingselect';
	import {formatDate} from 'common/js/date.js';
	import BScroll from 'better-scroll';

	const ALL = 2;
	const RES_OK = 0;
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				ratings: [],
				selectType: ALL,
				onlyContent: true
			};
		},
		created() {
			if (process.env.NODE_ENV === 'development') {
				this.$http.get('/api/ratings').then((response) => {
					response = response.body;
					if (response.error === RES_OK) {
						this.ratings = response.data;
						this.$nextTick(() => {
							this.scroll = new BScroll(this.$els.ratings, {
								click: true
							});
						});
					}
				});
			} else {
				this.$http.get('https://wkongl.github.io/Vue-takeaway/data.json').then((response) => {
					response = response.body;
					this.ratings = response.ratings;
					this.$nextTick(() => {
						this.scroll = new BScroll(this.$els.ratings, {
							click: true
						});
					});
				});
			}
		},
		methods: {
			ratingShow(type, text) {
				// 只看有内容评价
				if (this.onlyContent && !text) {
					return false;
				}

				if (this.selectType === ALL) {
					// 显示所有评价
					return true;
				} else {
					// 显示当前选择的评价
					return this.selectType === type;
				}
			}
		},
		filters: {
			formatDate(time) {
				let date = new Date(time);
				return formatDate(date, 'yyyy-MM-dd hh:mm');
			}
		},
		events: {
			'select.type'(type) {
				this.selectType = type;
				this.$nextTick(() => {
					this.contentScroll.refresh();
				});
			},
			'select.only'(onlyContent) {
				this.onlyContent = onlyContent;
				this.$nextTick(() => {
					this.contentScroll.refresh();
				});
			}
		},
		components: {
			star,
			split,
			ratingselect
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">
 	@import "../../common/stylus/mixin.styl"
 	.rating
 		position: absolute
 		top: 174px
 		left: 0
 		bottom: 0
 		width: 100%
 		overflow: hidden
 		.overview
 			display: flex
 			padding: 18px 0
 			.overview-left
 				flex: 0 0 137px
 				width: 137px
 				padding: 6px 0
 				text-align: center
 				border-right: 1px solid rgba(7, 17, 27, 0.1)
 				@media only screen and (max-width: 320px)
 					flex: 0 0 120px
 					width: 120px
 				.score
 					margin-bottom: 6px
 					line-height: 28px
 					font-size: 24px
 					color: rgb(255, 153, 0)
 				.title
 					margin-bottom: 8px
 					line-height: 12px
 					font-size: 12px
 					color: rgb(7, 17, 27)
 				.rank
 					line-height: 10px
 					font-size: 10px
 					color: rgb(147, 153, 159)
 			.overview-right
 				flex: 1
 				padding: 6px 0 6px 24px
 				@media only screen and (max-width: 320px)
 					padding-left: 6px
 				.score-wrapper
 					margin-bottom: 8px
 					font-size: 0
 					.title
 						display: inline-block
 						vertical-align: top
 						margin-right: 12px
 						line-height: 18px
 						font-size: 12px
 						color: rgb(7, 17, 27)
 					.star
 						display: inline-block
 						margin-right: 12px
 						vertical-align: top
 					.score
 						display: inline-block
 						vertical-align: top
 						line-height: 18px
 						font-size: 12px
 						color: rgb(255, 153, 0)
 				.time-wrapper
 					font-size: 0
 					.title
 						margin-right: 12px
 						line-height: 18px
 						font-size: 12px
 						color: rgb(7, 17, 27)
 					.time
 						line-height: 18px
 						font-size: 12px
 						color: rgb(147, 153, 159)
 		.rating-wrapper
 			padding: 0 18px
 			.rating-item
 				display: flex
 				padding: 18px 0
 				border-1px(rgba(7, 17, 27, 0.1))
 				.avatar
 					flex: 0 0 28px
 					margin-right: 12px
 					width: 28px
 					img
 						width: 28px
 						height: 28px
 						border-radius: 50%
 				.content
 					position: relative
 					flex: 1
 					.name
 						margin-bottom: 4px
 						line-height: 12px
 						font-size: 10px
 						color: rgb(7, 17, 27)
 					.star-wrapper
 						font-size: 0
 						.star
 							display: inline-block
 							veritcal-align: top
 							margin-right: 6px
 						.delivery-time
 							display: inline-block
 							veritcal-align: top
 							line-height: 12px
 							font-size: 10px
 							font-weight: 200
 							color: rgb(147, 153, 159)
 					.text
 						margin: 6px 0 8px 0
 						line-height: 18px
 						font-size: 12px
 						color: rgb(7, 17, 27)
	 				.recommend-wrapper
	 					line-height: 16px
	 					font-size: 0
	 					.icon-thumb_up, .icon-thumb_down
	 						margin-right: 8px
	 						font-size: 9px
	 					.icon-thumb_up
	 						color: rgb(0, 160, 220)
	 					.icon-thumb_down
	 						color: rgb(183, 187, 191)
	 					.recommend
	 						display: inline-block
	 						padding: 0 6px
	 						margin-right: 8px
	 						border: 1px solid rgba(7, 17, 27, 0.1)
	 						border-radius: 1px
	 						font-size: 9px
	 						color: rgb(147, 153, 159)
	 						background-color: #fff
 					.time
 						position: absolute
 						top: 0
 						right: 0
 						line-height: 12px
 						font-size: 10px
 						font-weight: 200
 						color: rgb(147, 153, 159)
	 						
</style>