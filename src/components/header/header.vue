<template>
	<div class="header">
		<div class="content-wrapper">
			<div class="avatar">
				<img :src="seller.avatar">
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟到达</div>
				<div class="support" v-if="seller.supports">
					<span class="icon" :class="classMap[seller.supports[0].type]"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div class="support-count" v-if="seller.supports" @click="detailShowClick" >
				<span class="count">{{seller.supports.length}}个</span>
				<i class="icon-keyboard_arrow_right"></i>
			</div>
		</div>
		<div class="bulletin-wrapper" @click="detailShowClick" >
			<span class="bulletin-icon"></span>
			<span class="bulletin-text">{{seller.bulletin}}</span>
			<i class="icon-keyboard_arrow_right"></i>
		</div>
		<div class="background">
			<img :src="seller.avatar">
		</div>
		<div class="detail" v-show="detailShow" transition="fade">
			<div class="detail-wrapper clearfix">
				<div class="detail-main">
					<h1 class="name">{{seller.name}}</h1>
					<div class="star-wrapper">
						<star :size="48" :score="seller.score"></star>
					</div>
					<div class="title-wrapper">
						<littletitle title="优惠信息"></littletitle>
					</div>
					<ul class="supports" v-if="seller.supports">
						<li class="support-item" v-for="item in seller.supports">
							<span class="icon" :class="classMap[item.type]"></span>
							<span class="text">{{item.description}}</span>
						</li>
					</ul>
					<div class="title-wrapper">
						<littletitle title="商家公告"></littletitle>
					</div>
					<div class="bulletin">
						<p class="content">{{seller.bulletin}}</p>
					</div>
				</div>
			</div>
			<div class="detail-close" @click="detailHideClick">
				<i class="icon-close"></i>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
	import star from 'components/star/star';
	import littleTitle from 'components/littleTitle/littleTitle';

	export default{
		props: {
			seller: {
				type: Object
			}
		},
		data() {
			return {
				detailShow: false
			};
		},
		methods: {
			detailShowClick() {
				this.detailShow = true;
			},
			detailHideClick() {
				this.detailShow = false;
			}
		},
		created() {
			this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
		},
		components: {
			star,
			'littletitle': littleTitle
		}
	};
</script>

<style rel="stylesheet/stylus" lang="stylus">
	@import '../../common/stylus/mixin.styl'
	.header
		position: relative
		overflow: hidden
		color: rgb(255,255,255)
		background: rgba(7, 17, 27, 0.5)
		.content-wrapper
			position: relative
			padding: 24px 12px 18px 24px 
			font-size: 0
			.avatar
				display: inline-block
				vertical-align: top
				img
					border-radius: 2px
					width: 64px
					height: 64px
			.content
				display: inline-block
				margin-left:16px
				.title
					margin: 2px 0 8px 0
					.brand
						display: inline-block
						vertical-align: top
						width: 30px
						height: 18px
						bg-image('images/brand')
						background-size: 30px 18px
						background-repeat: no-repeat
					.name
						margin-left: 6px
						line-height: 18px
						font-weight: blod
						font-size: 16px
				.description
					margin-bottom: 10px
					line-height: 12px
					font-size: 12px
				.support
					margin-bottom: 2px
					.icon
						display: inline-block
						vertical-align: top
						width: 12px
						height: 12px
						background-size: 12px 12px
						background-repeat: no-repeat
						&.decrease
							bg-image('images/decrease_1')
						&.discount
							bg-image('images/discount_1')
						&.guarantee
							bg-image('images/guarantee_1')
						&.invoice
							bg-image('images/invoice_1')
						&.special
							bg-image('images/special_1')
					.text
						margin-left: 4px
						line-height: 12px
						font-size: 10px
			.support-count
				position: absolute
				right: 12px
				bottom: 14px
				padding: 0 8px
				height: 24px
				line-height: 24px
				border-radius: 14px
				text-align: center
				background-color: rgba(0, 0, 0, 0.2)
				.count
					margin-right: 2
					// vertical-align: top
					line-height: 12px
					font-size: 10px
				.icon-keyboard_arrow_right
					line-height: 24px
					font-size: 10px
		.bulletin-wrapper
			position: relative
			padding: 0 22px 0 12px
			height: 28px
			line-height: 28px
			white-space: nowrap
			overflow: hidden
			text-overflow: ellipsis
			background-color: rgba(7, 17, 27, 0.2)
			.bulletin-icon
				display: inline-block
				vertical-align: middle
				width: 22px
				height: 12px
				bg-image('images/bulletin')
				background-size: 22px 12px
				background-repeat: no-repeat
			.bulletin-text
				margin: 0 4px
				vertical-align: middle
				line-height: 28px
				font-size: 10px
			.icon-keyboard_arrow_right
				position: absolute
				right: 12px
				bottom: 8px
				font-size: 10px
		.background
			position: absolute
			top: 0
			left: 0
			width: 100%
			height: 100%
			z-index: -1
			filter: blur(10px)
			img
				width: 100%
				height: 100%
		.detail
			position: fixed
			top: 0
			left: 0
			width: 100%
			height: 100%
			z-index: 100
			overflow: auto
			backdrop-filter: blur(10px)
			&.fade-transition
				opacity: 1
				transition: all 0.5s
				background-color: rgba(7, 17, 27, 0.8)
			&.fade-enter, &.fade-leave
				opacity: 0
				background-color: rgba(7, 17, 27, 0)
			.detail-wrapper
				display: inline-block
				min-height: 100%
				width: 100%
				color: rgb(255, 255, 255)
				.detail-main
					margin-top: 64px
					padding-bottom: 64px 
					.name
						line-height: 16px
						text-align: center
						font-size: 16px
						font-weight: 700
					.star-wrapper
						margin-top: 16px
						padding: 2px 0
						text-align: center
					.title-wrapper
						margin: 28px auto 24px auto
						width: 80%
					.supports
						width: 80%
						margin: 0 auto
						.support-item
							margin-bottom: 12px
							padding: 0 12px
							font-size: 0
							&.last-child
								margin-bottom: 0
							.icon
								display: inline-block
								vertical-align: top
								width: 16px
								height: 16px
								margin-right: 6px
								background-size: 16px 16px
								background-repeat: no-repeat
								&.decrease
									bg-image('images/decrease_2')
								&.discount
									bg-image('images/discount_2')
								&.guarantee
									bg-image('images/guarantee_2')
								&.invoice
									bg-image('images/invoice_2')
								&.special
									bg-image('images/special_2')
							.text
								line-height: 16px
								font-size: 12px
								font-weight: 200
								color: rgb(255, 255, 255)
					.bulletin
						width: 80%
						margin: 0 auto
						padding: 0 12px
						.content
							line-height: 24px
							font-size: 12px
							font-weight: 200
			.detail-close
				position: relative
				width: 32px
				height: 32px
				margin: -64px auto 0 auto
				font-size: 32px
				clear: both
				color: rgba(255, 255, 255, 0.5)
					
</style>