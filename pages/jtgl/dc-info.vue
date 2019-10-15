<template>
	<view @click="click">
		<uni-card title="新海电厂" thumbnail="" extra="" note="Tips">
			<view class="view" :style="{flexDirection:'row'}">
				<div>
					<text class="info-text">总负荷：1066.70MW</text>
					<text class="info-text">负荷率：80.9%</text>
				</div>
				<image src="../../static/dc.png" mode="" style="height: 106px;"></image>
			</view>

		</uni-card>
	</view>
</template>

<script>
	import uniCard from "@/components/uni-card/uni-card"

	export default {
		components: {
			uniCard
		},
		props: {
			options: {
				type: Object,
				default: function(e) {
					return {}
				}
			}
		},
		methods: {
			click() {
				this.$emit('click');
			},
			close(e) {
				this.$emit('close');
			}
		}
	}
</script>

<style>
	.view {
		flex-direction: column;
	}

	.flex-row {
		flex-direction: row;
	}

	.flex-col {
		flex-direction: column;
	}

	.info-text {
		margin-right: 40px;
		text-align: left;
	}
</style>
