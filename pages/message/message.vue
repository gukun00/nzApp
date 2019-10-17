<template>
	<view class="uni-container">
		<!-- 		<view class="uni-header-logo">
			<image class="uni-header-image" src="/static/extuiIndex.png"></image>
		</view>
		<view class="uni-hello-text">
			<text class="hello-text">以下是uni-app扩展组件示例，更多组件见插件市场：</text>
			<u-link class="hello-link" :href="'https://ext.dcloud.net.cn/'" :text="'https://ext.dcloud.net.cn'" :inWhiteList="true"></u-link>
		</view> -->
		<view class="uni-panel" v-for="item in lists" :key="item.url">
			<view class="uni-panel-h" @click="goDetailPage(item.url)">
				<text class="uni-panel-icon uni-icon" style="font-size: 16px;vertical-align: middle;color: red;">&#xe504;</text>
				<text class="uni-panel-text">{{item.name}}</text>
			</view>
		</view>
	</view>
</template>
<script>
	import uLink from "@/components/uLink.vue"
	export default {
		components: {
			uLink
		},
		data() {
			return {
				navigateFlag: false,
				lists: []
			};
		},
		onLoad() {
			for (var i = 0; i < 30; i++) {
				this.lists.push({
					name: "主汽压力温度越低限：设定值：550 ℃ ， 越限最值：545 ℃",
					url: "/pages/message/detail"
				})
			}
		},
		onReady() {},
		onShareAppMessage() {
			return {
				title: '欢迎体验uni-app',
				path: '/pages/tabBar/extUI/extUI'
			}
		},
		onNavigationBarButtonTap(e) {
			uni.navigateTo({
				url: '/pages/about/about'
			});
		},
		methods: {
			goDetailPage(path) {
				if (this.navigateFlag) {
					return;
				}
				this.navigateFlag = true;
				uni.navigateTo({
					url: '/pages/extUI/' + path + '/' + path
				});
				setTimeout(() => {
					this.navigateFlag = false;
				}, 200)
			}
		}
	}
</script>

<style>
	@import '../../common/uni-nvue.css';
</style>
