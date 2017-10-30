<template>
	<div class="seller" v-el:seller>
		<div class="seller-content">
			<div class="overview">
				<h1 class="title">{{seller.name}}</h1>
				<div class="desc border-1px">
					<star :size="36" :score="seller.score"></star>
					<span class="rating-count">({{seller.ratingCount}})</span>
					<span class="sell-count">月售{{seller.sellCount}}单</span>
				</div>
				<ul class="remarks">
					<li class="block">
						<h2 class="title">起送价</h2>
						<div class="content">
							<span class="stress">{{seller.minPrice}}</span>元
						</div>
					</li>
					<li class="block">
						<h2 class="title">商家配送</h2>
						<div class="content">
							<span class="stress">{{seller.deliveryPrice}}</span>元
						</div>
					</li>
					<li class="block">
						<h2 class="title">平均配送时间</h2>
						<div class="content">
							<span class="stress">{{seller.deliveryTime}}</span>分钟
						</div>
					</li>
				</ul>
				<div class="favorite-wrapper" @click="toggleFavorite">
					<i class="icon-favorite" :class="{'active':favorite}"></i>
					<span class="text">{{favoriteText}}</span>
				</div>
			</div>
			<split></split>	
			<div class="bulletin">
				<h1 class="title">公告与活动</h1>
				<div class="text-content border-1px">
					<p class="text">{{seller.bulletin}}</p>
				</div>
				<ul class="supports" v-if="seller.supports">
					<li class="support-item" v-for="item in seller.supports">
						<support-icon :format="4" :supporttype="item.type"></support-icon>
						<span class="text">{{item.description}}</span>
					</li>
				</ul>
			</div>
			<split></split>
			<div class="pics">
				<h1 class="title">商家实景</h1>
				<div class="pics-wrapper" v-el:pic-wrapper>
					<ul class="pic-list" v-el:pic-list>
						<li class="pic-item" v-for="pic in seller.pics">
							<img :src="pic">
						</li>
					</ul>
				</div>
			</div>
			<split></split>
			<div class="info">
				<h1 class="title border-1px">商家信息</h1>
				<ul>
					<li class="info-item border-1px" v-for="info in seller.infos">{{info}}</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
	import star from 'components/star/star';
	import split from 'components/split/split';
	import supportIcon from 'components/supportIcon/supportIcon';
	import {saveToLocal, loadFromLocal} from 'common/js/store.js';
	import BScroll from 'better-scroll';

	export default {
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				favorite: (() => {
					return loadFromLocal(this.seller.id, 'favorite', false);
				})()
			};
		},
		computed: {
			favoriteText() {
				return this.favorite ? '已收藏' : '收藏';
			}
		},
		watch: {
			'seller'() {
				this._initScroll();
				this._initPic();
			}
		},
		ready() {
			this._initScroll();
			this._initPic();
		},
		methods: {
			toggleFavorite(event) {
				if (!event._constructed) {
					return;
				}
				this.favorite = !this.favorite;
				saveToLocal(this.seller.id, 'favorite', this.favorite);
			},
			_initScroll() {
				if (!this.scroll) {
					this.scroll = new BScroll(this.$els.seller, {
						click: true
					});
				} else {
					this.scroll.refresh();
				}
			},
			_initPic() {
				if (!this.picScroll) {
					if (this.seller.pics) {
						let picWidth = 120;
						let picMargin = 6;
						let width = (picWidth + picMargin) * this.seller.pics.length - picMargin;
						this.$els.picList.style.width = width + 'px';
						this.$nextTick(() => {
							this.picScroll = new BScroll(this.$els.picWrapper, {
								scrollX: true,
								eventPassthrough: 'vertical'
							});
						});
					}
				} else {
					this.picScroll.refresh();
				}
			}
		},
		components: {
			star,
			split,
			supportIcon
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import '../../common/stylus/mixin.styl';
	.seller
		position: absolute
		top: 174px
		left: 0
		bottom: 0
		width:100%
		overflow: hidden
		.seller-content
			.overview
				position: relative
				padding: 18px
				.title
					margin-bottom: 8px
					line-height: 14px
					font-size: 14px
					color: rgb(7, 17, 27)
				.desc
					padding-bottom: 18px
					border-1px(rgba(7, 17, 27, 0.1))
					font-size: 0
					.star
						display: inline-block
						margin-right: 8px
						vertical-align: top
					.rating-count, .sell-count
						display: inline-block
						vertical-align: top
						line-height: 18px
						font-size: 10px
						color: rgb(77, 85, 93)
					.rating-count
						margin-right: 12px
				.remarks
					display: flex
					margin-top: 18px
					.block
						flex: 1
						text-align: center
						border-right: 1px solid rgba(7, 17, 27, 0.1)
						&:last-child
							border-right: none
						.title
							margin-bottom: 4px
							line-height: 10px
							font-size: 10px
							color: rgb(147, 153, 159)
						.content
							line-height: 24px
							font-size: 10px
							font-weight: 200
							color: rgb(7, 17, 27)
							.stress
								font-size: 24px
				.favorite-wrapper
					position: absolute
					width: 50px
					top: 11px
					right: 18px
					text-align: center
					.icon-favorite
						display: block
						margin-bottom: 4px
						line-height: 24px
						font-size: 24px
						color: #d4d6d9
						&.active
							color: rgb(240, 20, 20)
					.text
						line-height: 10px
						font-size: 10px
						color: rgb(77, 85, 93)
			.bulletin
				padding: 0 18px
				.title
					margin-top: 18px
					line-height: 14px
					font-size: 14px
					color: rgb(7, 17, 27)
				.text-content
					margin-top: 8px
					padding: 0 12px 16px 12px
					border-1px(rgba(7, 17, 27, 0.1))
					.text
						line-height: 24px
						font-size: 12px
						font-weight: 200
						color: rgb(240, 20, 20) 
				.supports
					padding: 0 18px
					.support-item
						padding: 16px 12px
						font-size: 0
						border-1px(rgba(7, 17, 27, 0.1))
						&:last-child
							border-none()
						.icon
							margin-right: 6px
						.text
							line-height: 16px
							font-size: 12px
							font-weight: 200
							color: rgb(7, 17, 27)
			.pics
				padding: 18px 0 18px 18px
				.title
					margin-bottom: 12px	
					line-height: 14px
					font-size: 14px
					color: rgb(7, 17, 27)
				.pics-wrapper
					width: 100%
					overflow: hidden
					white-space: nowrap
					.pic-list
						font-size: 0			
						.pic-item
							display: inline-block
							margin-right: 6px
							width: 120px
							height: 90px
							&:last-child
								margin-right: 0
							img
								width: 120px
								height: 90px
			.info
				padding: 18px 18px 0 18px	
				.title
					padding-bottom: 12px	
					line-height: 14px
					border-1px(rgba(7, 17, 27, 0.1))
					font-size: 14px
					color: rgb(7, 17, 27)
				.info-item
					padding: 16px 12px
					border-1px(rgba(7, 17, 27, 0.1))
					line-height: 16px
					font-size: 12px
					font-weight: 200
					color: rgb(7, 17, 27)
					&:last-child
						border-none
						
</style>