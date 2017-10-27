<template>
	<div class="ratingselect">
		<div class="ratingtype border-1px">
			<span class="block positive" :class="{'active': selectType===2}" @click="select(2,$event)">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
			<span class="block positive" :class="{'active': selectType===0}" @click="select(0,$event)">{{desc.positive}}<span class="count">{{positive.length}}</span></span>
			<span class="block negative" :class="{'active': selectType===1}" @click="select(1,$event)">{{desc.negative}}<span class="count">{{negative.length}}</span></span>
		</div>
		<div class="switch border-1px" :class="{'on':onlyContent}" @click="toggleOnly">
			<i class="icon-check_circle"></i>
			<span class="text">只看有内容的评价</span>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
	const POSITIVE = 0; // 满意
	const NEGATIVE = 1; // 不满意
	const ALL = 2; // 全部
	export default {
		props: {
			ratings: {
				type: Array,
				default() {
					return [];
				}
			},
			selectType: {
				type: Number,
				default: ALL
			},
			onlyContent: {
				type: Boolean,
				default: false
			},
			desc: {
				type: Object,
				default: {
					positive: '满意',
					negative: '不满意',
					all: '全部'
				}
			}
		},
		computed: {
			// 满意评价数
			positive() {
				return this.ratings.filter((rating) => {
					return rating.rateType === POSITIVE;
				});
			},
			// 不满意评价数
			negative() {
				return this.ratings.filter((rating) => {
					return rating.rateType === NEGATIVE;
				});
			}
		},
		methods: {
			// 选择评价
			select(type, event) {
				if (!event._constructed) {
					return;
				}
				this.selectType = type;
				this.$dispatch('select.type', type);
			},
			// 切换只看有内容评价的状态
			toggleOnly(event) {
				if (!event._constructed) {
					return;
				}
				this.onlyContent = !this.onlyContent;
				this.$dispatch('select.only', this.onlyContent);
			}
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus" >
	@import '../../common/stylus/mixin.styl'
	.ratingselect
		.ratingtype
			padding: 18px 0
			margin: 0 18px
			font-size: 0
			border-1px(rgba(7, 17, 27, 0.1))
			.block
				display: inline-block
				padding: 8px 12px
				margin-right: 8px
				line-height: 16px
				border-radius: 1px
				font-size: 12px
				color: rgb(77, 85, 93)
				&.active
					color: #fff
				.count
					margin-left: 2px
					font-size: 8px
			.positive
				background-color: rgba(0, 160, 220, 0.2)
				&.active
					background-color: rgb(0, 160, 220)
			.negative
				background-color: rgba(77, 85, 93, 0.2)
				&.active
					background-color: rgb(77, 85, 93)
		.switch
			padding: 12px 18px
			line-height: 24px
			border-1px(rgba(7, 17, 27, 0.1))
			color: rgb(147, 153, 159)
			font-size: 0
			&.on
				.icon-check_circle
					color: #00c850
			.icon-check_circle
				display: inline-block
				vertical-align: top
				margin-right: 4px
				font-size: 24px
			.text
				font-size: 12px
</style>