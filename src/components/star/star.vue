<template>
	<div class="star" :class="starType">
		<span class="star-item" :class="starClass" v-for="starClass in starClasses" track-by="$index"></span>
	</div>
</template>

<script type="text/emcascript-6">

	const STARLENGTH = 5;
	const STARHALF = 'half';
	const STAROFF = 'off';
	const STARON = 'on';

	export default {
		props: {
			size: {
				type: Number
			},
			score: {
				type: Number
			}
		},
		computed: {
			starType() {
				return 'star-' + this.size;
			},
			// 根据评分来计算需要的星星
			starClasses() {
				let starArr = [];
				// 拥有的全星数
				let scoreLength = Math.floor(this.score);
				// 是否拥有半星
				let hasHalf = Math.floor((this.score%1) *2) !== 0;

				// 填入全星
				for(let i = 0 ;i < scoreLength; i++){
					starArr.push(STARON);
				}

				// 填入半星
				if(hasHalf){
					starArr.push(STARHALF);
				}

				// 把空星填入剩余空位中
				while(starArr.length < STARLENGTH){
					starArr.push(STAROFF);
				}

				return starArr
			}
			
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import '../../common/stylus/mixin.styl'
	
	.star
		font-size: 0
		.star-item
			display:inline-block
			background-repeat: no-repeat
		&.star-24
			.star-item
				width: 10px
				height: 10px
				margin-right: 3px
				background-size: 10px 10px
				&.half 
					bg-image('images/star24_half')
				&.off
					bg-image('images/star24_off')
				&.on
					bg-image('images/star24_on')
				&:last-child
					margin-right: 0
		&.star-36
			.star-item
				width: 15px
				height: 15px
				margin-right: 6px
				background-size: 15px 15px
				&.half 
					bg-image('images/star36_half')
				&.off
					bg-image('images/star36_off')
				&.on
					bg-image('images/star36_on')
				&:last-child
					margin-right: 0
		&.star-48
			.star-item
				width: 20px
				height: 20px
				margin-right: 22px
				background-size: 20px 20px
				&.half 
					bg-image('images/star48_half')
				&.off
					bg-image('images/star48_off')
				&.on
					bg-image('images/star48_on')
				&:last-child
					margin-right: 0
</style>