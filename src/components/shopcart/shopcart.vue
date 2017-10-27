<template>
	<div class="shop-cart">
		<div class="content" @click="toggleList">
			<div class="content-left" >
				<div class="cart-wrapper" >
					<div class="icon" :class="{'heightlight':totalCount>0}">
						<i class="icon-shopping_cart" :class="{'heightlight':totalCount>0}"></i>
					</div>
					<div class="num" v-show="totalCount>0">{{totalCount}}</div>
				</div>
				<div class="price" :class="{'heightlight':totalPrice>0}">¥{{totalPrice}}</div>
				<div class="delivery-price">另需配送费¥{{deliveryPrice}}元</div>
			</div>
			<div class="content-right" @click.stop.prevent="payClick" >
				<div class="pay" :class="payClass">{{payDesc}}</div>
			</div>
		</div>
		<div class="ball-container">
			<div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
				<div class="inner inner-hook"></div>
			</div>
		</div>
		<div class="list-shopcart" v-show="showList" transition="fold">
			<div class="list-header">
				<h1 class="title">购物车</h1>
				<span class="empty" @click="empty">清空</span>
			</div>
			<div class="list-content" v-el:list-content>
				<ul>
					<li class="food" v-for="food in selectFoods">
						<span class="name">{{food.name}}</span>
						<div class="price">
							<span>¥{{food.price*food.count}}</span>
						</div>
						<div class="cartcontrol-wrapper">
							<cartcontrol :food="food"></cartcontrol>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
	<div class="list-mask" v-show="showList" transition="fade" @click="hideMask"></div>
</template>

<script type="text/ecmascript-6">
	import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
	import BScroll from 'better-scroll';
	export default {
		props: {
			selectFoods: {
				type: Array,
				default() {
					return [];
				}
			},
			minPrice: {
				type: Number,
				default: 0
			},
			deliveryPrice: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				balls: [
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					}],
				dropBalls: [],
				fold: true // 判断购物车列表是否折叠 true-已折叠 false-未折叠
			};
		},
		computed: {
			totalPrice() {
				let price = 0;
				// 计算购买产品的总金额
				this.selectFoods.forEach((food) => {
					price += food.price * food.count;
				});
				return price;
			},
			totalCount() {
				let count = 0;
				// 共购买的产品数量
				this.selectFoods.forEach((food) => {
					count += food.count;
				});
				return count;
			},
			payDesc() {
				// 设置结算的描述
				if (this.totalPrice === 0) {
					return `¥${this.minPrice}起送`;
				} else if (this.totalPrice < this.minPrice) {
					let diff = this.minPrice - this.totalPrice;
					return `还差¥${diff}起送`;
				} else {
					return '去结算';
				}
			},
			payClass() {
				// 设置结算的class
				if (this.totalPrice < this.minPrice) {
					return 'not-enough';
				} else {
					return 'enough';
				}
			},
			showList() {
				// 没选择食物时不显示列表
				if (!this.totalCount) {
					this.fold = true;
					return false;
				}
				// 已选择食物，则在已折叠状态下隐藏列表，未折叠显示列表
				let show = !this.fold;
				if (show) {
					this.$nextTick(() => {
						if (!this.listScroll) {
							this.listScroll = new BScroll(this.$els.listContent, {
								click: true
							});
						} else {
							this.listScroll.refresh();
						}
					});
				}
				return show;
			}
		},
		methods: {
			drop(el) {
				for (let i = 0; i < this.balls.length; i++) {
					let ball = this.balls[i];
					if (!ball.show) {
						ball.show = true;
						ball.el = el;
						this.dropBalls.push(ball);
						return;
					}
				}
			},
			toggleList() {
				// 切换购物列表折叠状态
				if (!this.totalCount) {
					return;
				}
				this.fold = !this.fold;
			},
			empty() {
				// 清空购物数量
				this.selectFoods.forEach((food) => {
					food.count = 0;
				});
			},
			hideMask() {
				this.fold = true;
			},
			payClick() {
				if (this.totalPrice < this.minPrice) {
					return;
				}
				window.alert(`支付${this.totalPrice}¥`);
			}
		},
		transitions: {
			drop: {
				beforeEnter(el) {
					let count = this.balls.length;
					while (count--) {
						let ball = this.balls[count];
						if (ball.show) {
							let rect = ball.el.getBoundingClientRect();
							let x = rect.left - 32;
							let y = -(window.innerHeight - rect.top - 22);
							el.style.display = '';
							el.style.webkitTransform = `translate3d(0,${y}px,0)`;
							el.style.transform = `translate3d(0,${y}px,0)`;
							let inner = el.getElementsByClassName('inner-hook')[0];
							inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
							inner.style.transform = `translate3d(${x}px,0,0)`;
						}
					}
				},
				enter(el) {
					/* eslint-disable no-unused-vars */
					let rf = el.offsetHeight;
					this.$nextTick(() => {
						el.style.webkitTransform = 'translate3d(0,0,0)';
						el.style.transform = 'translate3d(0,0,0)';
						let inner = el.getElementsByClassName('inner-hook')[0];
						inner.style.webkitTransform = 'translate3d(0,0,0)';
						inner.style.transform = 'translate3d(0,0,0)';
					});
				},
				afterEnter(el) {
					let ball = this.dropBalls.shift();
					if (ball) {
						ball.show = false;
						el.style.display = 'none';
					}
				}
			}
		},
		components: {
			cartcontrol
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus" > 
	@import '../../common/stylus/mixin.styl'
	.shop-cart
		position: fixed
		left: 0
		bottom: 0
		z-index: 50
		width: 100%
		height: 48px
		.content
			display: flex
			background-color: #141d27
			font-size: 0
			color: rgba(255, 255, 255, 0.4)
			.content-left
				flex: 1
				.cart-wrapper
					display: inline-block
					position: relative
					vertical-align: top
					bottom: 7px
					margin:0 12px 2px 12px
					padding: 6px
					width: 56px
					height: 56px
					box-sizing: border-box
					border-radius: 50%
					background-color: #141d27
					.icon
						width: 100%
						height: 100%
						text-align: center
						border-radius: 50%
						background-color: #2b343c
						&.heightlight
							background-color: rgb(0, 160, 220)
						.icon-shopping_cart
							line-height: 44px
							font-size: 24px
							color: #80858a
							&.heightlight
								color: #fff
					.num
						position: absolute
						top: 0
						right: 0
						width: 24px
						height: 16px
						line-height: 16px
						border-radius: 6px
						text-align: center
						font-size: 9px
						font-weight: 700
						color: rgb(255, 255, 255)
						background-color: rgb(240, 20, 20)
						box-shadow: rgba(0, 0, 0, 0.4)
				.price
					display:inline-block
					margin-top: 12px
					padding-right: 12px
					line-height: 24px
					border-right: 1px solid rgba(255, 255, 255, 0.1)
					font-size: 16px
					font-weight: 700
					&.heightlight
						color: #fff
				.delivery-price
					display: inline-block
					margin: 12px 0 0 12px
					line-height: 24px
					font-size: 10px
			.content-right
				flex: 0 0 105px
				width: 105px
				.pay
					line-height: 48px
					text-align: center
					font-size: 12px
					font-weight: 700
					&.not-enough
						background-color: #2b333b
					&.enough
						background-color: #00b43c
						color: #fff
		.ball-container
			.ball
				position: fixed
				left: 32px
				bottom: 22px
				z-index: 200
				&.drop-transition
					transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
					.inner
						width: 16px
						height: 16px
						border-radius: 50%
						background: rgb(0, 160, 220)
						transition: all 0.4s linear
		.list-shopcart
			position: absolute
			top: 0px
			left: 0px
			z-index: -1
			width: 100%
			&.fold-transition
				transition: all 0.5s
				transform: translate3d(0, -100%, 0)
			&.fold-enter, &.fold-leave
				transform: translate3d(0, 0, 0)
			.list-header
				padding: 0 18px
				height: 40px
				line-height: 40px
				border-bottom: 1px solid rgba(7, 17, 27, 0.1)
				background: #f3f5f7
				.title
					float: left
					font-size: 14px
					font-weight: 200
					color: rgb(7, 17, 27)
				.empty
					float: right
					font-size: 12px
					color: rgb(0, 160, 220)
			.list-content
				padding: 0 18px
				max-height: 217px
				overflow: hidden
				background: #fff
				.food
					position: relative
					padding: 12px 0
					border-box: border-box
					border-1px(rgba(7, 17, 27, 0.1))
					.name
						font-size: 14px
						line-height: 24px
						color: rgb(7, 17, 27)
					.price
						position: absolute
						right: 90px
						bottom: 12px
						line-height: 24px
						font-size: 12px
						font-weight: 700
						color: rgb(240, 20, 20)
					.cartcontrol-wrapper
						position: absolute
						right:0
						bottom: 6px
	.list-mask
		position: fixed
		top: 0
		left: 0
		width: 100%
		height: 100%
		z-index: 40
		backdrop-filter: blur(10px)
		&.fade-transition
			transition: all 0.5s
			opacity: 1
			background: rgba(7, 17, 27, 0.6)
		&.fade-enter, &.fade-leave
			opacity: 0
			background: rgba(7, 17, 27, 0)
		
</style>