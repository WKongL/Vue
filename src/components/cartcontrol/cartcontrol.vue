<template>
	<div class="cartcontrol">
		<div class="decrease" v-show="food.count>0" @click.stop.prevent="decreaseCount" transition="move">
			<span class="inner icon-remove_circle_outline"></span>
		</div>
		<div class="count" v-show="food.count>0">{{food.count}}</div>
		<div class="add icon-add_circle" @click.stop.prevent="addCount"></div>
	</div>
</template>

<script type="text/ecmascript-6">
	import Vue from 'vue';
	export default {
		props: {
			food: {
				type: Object
			}
		},
		methods: {
			addCount(event) {
				if (!event._constructed) {
					return;
				}
				// 增加食物数量
				if (!this.food.count) {
					Vue.set(this.food, 'count', 1);
				} else {
					this.food.count++;
				}

				this.$dispatch('cart.add', event.target);
				this.$dispatch('food.change', this.food);
			},
			decreaseCount(event) {
				if (!event._constructed) {
					return;
				}
				// 减少食物数量
				if (this.food.count) {
					this.food.count --;
				}
			}
		}
	};
</script>

<style lang="stylus" rel="stylesheet/stylus" > 
	.cartcontrol
		font-size: 0
		.decrease
			display: inline-block
			padding: 6px
			transition: all 0.4s linear
			&.move-transition
				opacity: 1
				transform: translate3d(0, 0, 0)
				.inner
					display: inline-block
					line-height: 24px
					font-size: 24px
					color: rgb(0, 160, 220)
					transition: all 0.4s linear
					transform: rotate(0)
			&.move-enter, &.move-leave
				opacity: 0
				transform: translate3d(24px, 0, 0)
				.inner
					transform: rotate(180deg)
		.count
			display: inline-block
			vertical-align: top
			width: 12px
			padding-top: 6px
			text-align: center
			line-height: 24px
			font-size: 10px
			color: rgb(147, 153, 159)
		.add
			display: inline-block
			padding: 6px
			line-height: 24px
			font-size: 24px
			color: rgb(0, 160, 220)
</style>