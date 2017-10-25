<template>
	<div class="goods">
		<div class="menu-wrapper" v-el:menu-wrapper>
			<ul>
				<li class="menu-item" v-for="item in goods" :class="{'current':currentIndex === $index}" @click="clickMenu($index, $event)">
					<span class="text border-1px"><supporticon :format="3" :supporttype="item.type"></supporticon>{{item.name}}</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper" v-el:food-wrapper>
			<ul>
				<li class="food-list food-list-hook" v-for="good in goods" >
					<h1 class="title">{{good.name}}</h1>
					<ul>
						<li class="food-item border-1px" v-for="food in good.foods">
							<div class="icon">
								<img :src="food.icon">
							</div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="desc">{{food.description}}</p>
								<div class="extra">
									<span class="sales">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span class="now">¥{{food.price}}</span><span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
								</div>
								<div class="control-wrapper">
									<cartcontrol :food="food"></cartcontrol>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
	</div>
	<div class="footer">
		<shopcart v-ref:shopcart :min-price="seller.minPrice" :delivery-price="seller.deliveryPrice" :select-foods="selectFoods"></shopcart>
	</div>
</template>

<script type="text/ecmascript-6">
	import supportIcon from 'components/supportIcon/supportIcon';
	import shopcart from 'components/shopcart/shopcart';
	import cartcontrol from 'components/cartcontrol/cartcontrol';
	import BScroll from 'better-scroll';

	const RES_OK = 0;
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				goods: [],
				listHeight: [],
				scrollY: 0
			};
		},
		created() {
			this.$http.get('/api/goods').then((response) => {
				var result = response.body;
				if (result.error === RES_OK) {
					this.goods = result.data;
					this.$nextTick(() => {
						this._initScroll();
						this._calculateHeight();
					});
				}
			});
		},
		methods: {
			_initScroll() {
				// 菜单栏绑定滚动条插件库
				this.menuScroll = new BScroll(this.$els.menuWrapper, {
					click: true
				});
				// 食物栏绑定滚动条插件库
				this.foodScroll = new BScroll(this.$els.foodWrapper, {
					click: true,
					probeType: 3
				});

				this.foodScroll.on('scroll', (pos) => {
					this.scrollY = Math.abs(Math.round(pos.y));
				});
			},
			_drop(target) {
				// 体验优化，异步执行下落动画
				this.$nextTick(() => {
					this.$refs.shopcart.drop(target);
				});
			},
			_calculateHeight() {
				let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
				let height = 0;
				// 保存每种食物区域的高度
				this.listHeight.push(height);
				for (let i = 0; i < foodList.length; i++) {
					let foodItem = foodList[i];
					height += foodItem.clientHeight;
					this.listHeight.push(height);
				}
			},
			clickMenu(index, event) {
				if (!event._constructed) {
					return;
				}
				let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
				let foodItem = foodList[index];
				// 跳转到当前点击项目所对应的食物区域
				this.foodScroll.scrollToElement(foodItem, 300);
			}
		},
		computed: {
			currentIndex() {
				// 获取当前食物区域对应的菜单栏索引
				for (let i = 0; i < this.listHeight.length; i++) {
					let height1 = this.listHeight[i];
					let height2 = this.listHeight[i + 1];

					if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
						return i;
					}
				}

				return 0;
			},
			selectFoods() {
				let foods = [];
				// 存储选择的食物
				this.goods.forEach((good) => {
					good.foods.forEach((food) => {
						if (food.count) {
							foods.push(food);
						}
					});
				});

				return foods;
			}
		},
		components: {
			'supporticon': supportIcon,
			shopcart,
			cartcontrol
		},
		events: {
			'cart.add'(target) {
				this._drop(target);
			}
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import '../../common/stylus/mixin.styl'

	.goods
		display: flex
		position: absolute
		top: 174px
		bottom: 46px
		width: 100%
		overflow: hidden
		.menu-wrapper
			flex: 0 0 80px
			width: 80px
			background-color: #f3f5f7
			.menu-item
				display: table
				padding: 0 12px
				width: 56px
				height: 54px
				&.current
					position: relative
					margin-top: -2px
					z-index: 10
					background-color: #fff
					font-weight: 700
					.text
						border-none()
			.text
				display: table-cell
				vertical-align: middle
				line-height: 14px
				border-1px(rgba(7, 17, 27, 0.1))
				color: rgb(7, 17, 27)
				font-size: 12px
				font-weight: 200
		.foods-wrapper
			flex: 1
			.food-list
				.title
					padding-left: 14px
					line-height: 26px
					height: 26px
					border-left: 2px solid #d9dde1
					font-size: 12px
					color: rgb(147, 153, 159)
					background-color: #f3f5f7
				.food-item
					display: flex
					margin: 18px 18px 0px 18px
					padding-bottom: 18px
					border-1px(rgba(7, 17, 27, 0.1))
					&:last-child
						// padding-bottom: 0
						border-none()
					.icon
						flex: 0 0 57px
						margin-right: 10px
						img
							width: 57px
							heigth: 57px
					.content
						flex: 1
						.name
							margin: 2px 0 8px 0
							height: 14px
							line-height: 14px
							font-size: 14px
							color: rgb(7, 17, 27)
						.desc, .extra
							line-height: 10px
							font-size: 10px
							color: rgb(147, 153, 159)
						.desc
							line-height: 14px
							margin-bottom: 8px
						.extra
							.sales
								margin-right: 12px
						.price
							line-height: 24px
							font-weight: 700
							.now
								margin-right: 8px
								font-size: 14px
								color: rgb(240, 20, 20)
							.old
								text-decoration: line-through
								font-size: 10px
								color: rgb(147, 153, 159)
						.control-wrapper
							position: absolute
							right: 0
							bottom: 12px
							
				
</style>