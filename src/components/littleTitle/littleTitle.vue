<template>
	<div class="little-title">
		<div class="line"></div>
		<div class="text">{{title}}</div>
		<div class="line"></div>
	</div>
</template>

<script type="text/ecmascript-6">
	export default{
		props: {
			title: {
				type: String
			}
		}
	};
</script>

<style lang="stylus" cel="stylesheet/stylus">
	.little-title
		display: flex
		.line
			flex: 1
			position: relative
			top: -7px
			border-bottom: 1px solid rgba(255, 255, 255, 0.2)
		.text
			margin: 0 12px
			line-height: 14px
			font-size: 14px
			font-weight: 700
			color: rgb(255, 255, 255)
</style>