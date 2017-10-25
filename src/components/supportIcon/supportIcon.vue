<template>
	<span class="icon" :class="iconType"></span>
</template>

<script type="text/ecmascript-6">
	export default{
		props: {
			format: {
				type: Number
			},
			supporttype: {
				type: Number
			}
		},
		computed: {
			// 获取icon的规格，类型
			iconType() {
				let classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
				if (classMap[this.supporttype] == null) {
					return '';
				}
				return 'icon-' + this.format + ' ' + classMap[this.supporttype];
			}
		}
	};
</script>

<style lang="stylus" cel="stylesheet/stylus">
	@import '../../common/stylus/mixin.styl'
	.icon
		display: inline-block
		vertical-align: top
		background-repeat: no-repeat
		&.icon-1
			width: 12px
			height: 12px
			background-size: 12px 12px
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
		&.icon-2
			width: 16px
			height: 16px
			background-size: 16px 16px
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
		&.icon-3
			width: 12px
			height: 12px
			background-size: 12px 12px
			&.decrease
				bg-image('images/decrease_3')
			&.discount
				bg-image('images/discount_3')
			&.guarantee
				bg-image('images/guarantee_3')
			&.invoice
				bg-image('images/invoice_3')
			&.special
				bg-image('images/special_3')
		&.icon-4
			width: 16px
			height: 16px
			background-size: 16px 16px
			&.decrease
				bg-image('images/decrease_4')
			&.discount
				bg-image('images/discount_4')
			&.guarantee
				bg-image('images/guarantee_4')
			&.invoice
				bg-image('images/invoice_4')
			&.special
				bg-image('images/special_4')
</style>