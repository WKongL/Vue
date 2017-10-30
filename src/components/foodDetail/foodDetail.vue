<template>
	<div class="food" v-show="showFlag" transition="move" v-el:content>
		<div class="food-content">
			<div class="header">
				<img :src="food.image">
				<div class="back" @click="hide">
					<i class="icon-arrow_lift"></i>
				</div>
			</div>
			<div class="content" >
				<h1 class="title">{{food.name}}</h1>
				<div class="extra">
					<span class="sales">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
				</div>
				<div class="price">
					<span class="now">¥{{food.price}}</span><span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
				</div>
				<div class="cartcontrol-wrapper">
					<cartcontrol :food="food"></cartcontrol>
				</div>
				<div class="buy-cart" v-show="!this.food.count || this.food.count === 0" @click="buyFirst" transition="fade">加入购物车</div>
			</div>
			<split v-show="food.info"></split>
			<div class="info" v-show="food.info">
				<h1 class="title">商品介绍</h1>
				<p class="desc">{{food.info}}</p>
			</div>
			<split ></split>
			<div class="rating-comment">
				<h1 class="title">商品评价</h1>
				<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
			</div>
			<div class="rating-wrapper">
				<ul v-show="food.ratings && food.ratings.length">
					<li class="rating-item" v-for="rating in food.ratings" v-show="ratingShow(rating.rateType, rating.text)">
						<div class="time">{{rating.rateTime | formatDate}}</div>
						<p class="rating-content"><i :class="{'icon-thumb_up':rating.rateType ===0,'icon-thumb_down':rating.rateType===1}"></i>{{rating.text}}</p>
						<div class="user">
							<span class="name">{{rating.username}}</span>
							<img class="avatar" :src="rating.avatar">
						</div>
					</li>
				</ul>
				<div class="no-rating" v-show="!food.ratings || food.ratings.length">暂无评价</div>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
	import BScroll from 'better-scroll';
	import cartcontrol from 'components/cartcontrol/cartcontrol';
	import split from 'components/split/split';
	import ratingselect from 'components/ratingselect/ratingselect';
	import Vue from 'vue';
	import {formatDate} from 'common/js/date';
	// const POSITIVE = 0; // 满意
	// const NEGATIVE = 1; // 不满意
	const ALL = 2; // 全部
	export default {
		props: {
			food: {
				type: Object
			}
		},
		data() {
			return {
				showFlag: false,
				selectType: ALL,
				onlyContent: true,
				desc: {
					all: '全部',
					positive: '推荐',
					negative: '吐槽'
				}
			};
		},
		methods: {
			show() {
				this.showFlag = true;
				// 初始化数据
				this.selectType = ALL;
				this.onlyContent = true;
				// 绑定滚动条
				this.$nextTick(() => {
					if (!this.contentScroll) {
						this.contentScroll = new BScroll(this.$els.content, {
							click: true
						});
					} else {
						this.contentScroll.refresh();
					}
				});
			},
			hide(event) {
				if (!event._constructed) {
					return;
				}
				this.showFlag = false;
			},
			buyFirst(event) {
				if (!event._constructed) {
					return;
				}
				this.$dispatch('cart.add', event.target);
				Vue.set(this.food, 'count', 1);
			},
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
		filters: {
			formatDate(time) {
				let date = new Date(time);
				return formatDate(date, 'yyyy-MM-dd hh:mm');
			}
		},
		components: {
			cartcontrol,
			split,
			ratingselect
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus" >
	@import '../../common/stylus/mixin.styl'
	.food
		position: fixed
		left: 0
		top: 0
		bottom: 48px
		z-index: 30
		width: 100%
		background-color: #fff
		&.move-transition
			transition: all 0.2s linear
			transform: translate3d(0, 0, 0)
		&.move-enter, &.move-leave
			transform: translate3d(100%, 0, 0)
		.food-content
			.header
				position: relative
				width: 100%
				height: 0
				padding-top: 70%
				img
					position: absolute
					top: 0
					left: 0
					width: 100%
					height: 100%
				.back
					position: absolute
					padding: 10px
					top: 10px
					left: 10px
					border-radius: 50%
					text-align: center
					background-color: rgba(7, 17, 27, 0.5)
					.icon-arrow_lift
						font-size: 14px
						color: #fff
			.content
				position: relative
				padding: 18px
				.title
					line-height: 14px
					font-size: 14px
					font-weight: 700
					color: rgb(7, 17, 27)
				.extra
					margin: 8px 0 18px 0
					font-size: 10px
					line-height: 10px
					color: rgb(147, 153, 159)
					.sales
						margin-right: 12px
				.price
					line-height: 24px
					font-weight: 700
					.now
						margin-right: 8px
						font-size: 14px
						font-weight: 700
						color: rgb(240, 20, 20)
					.old
						text-decoration: line-through
						line-height: 24px
						font-size: 10px
						color: rgb(147, 153, 159)
				.cartcontrol-wrapper
					position: absolute
					right: 12px
					bottom: 12px
				.buy-cart
					position: absolute
					right: 18px
					bottom: 18px
					z-index: 10
					padding: 0px 12px
					height: 24px
					line-height: 24px
					box-sizing: border-box
					border-radius: 12px
					font-size: 10px
					color: rgb(255, 255, 255)
					background-color: rgb(0, 160, 220)
					&.fade-transition
						transition: all 0.2s
						opacity: 1
					&.fade-enter, &.fade-leave
						opacity: 0
			.info
				padding: 18px
				.title
					line-height: 14px
					font-size: 14px
					font-weight: 700
					color: rgb(7, 17, 27)
				.desc
					margin-top: 6px
					padding: 0 8px
					line-height: 24px
					font-size: 12px
					font-weight: 200
					color: rgb(77, 85, 93)
			.rating-comment
				padding-top: 18px
				.title
					margin-left: 18px
					line-height: 14px
					font-size: 14px
					font-weight: 700
					color: rgb(7, 17, 27)
		.rating-wrapper
			padding: 0 18px
			.rating-item
				position: relative
				padding: 16px 0
				border-1px(rgba(7, 17, 27, 0.1))
				.time
					margin-bottom: 6px
					line-height: 12px
					font-size: 10px
					color: rgb(147, 153, 159)
				.rating-content
					line-height: 16px
					font-size: 12px
					color: rgb(7, 17, 27)
					.icon-thumb_up, .icon-thumb_down
						margin-right: 4px
						line-height: 14px
						font-size: 12px
					.icon-thumb_up
						color: rgb(0, 160, 220)
					.icon-thumb_down
						color: rgb(147, 152, 159)
				.user
					position: absolute
					top: 16px
					right: 0
					line-height: 12px
					font-size: 0
					.name
						display: inline-block
						vertical-align: top
						margin-right: 6px
						font-size: 10px
						color: rgb(147, 153, 159)
					.avatar
						height: 12px
						wieth: 12px
						border-radius: 50%
			.no-rating
				padding: 16px 0
				font-size: 12px
				color: rgb(147, 153, 159);
				
</style>