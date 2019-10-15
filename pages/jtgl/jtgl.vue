<template>
	<view class="tabs">
		<scroll-view id="tab-bar" class="scroll-h" :scroll-x="true" :show-scrollbar="false" :scroll-into-view="scrollInto">
			<view v-for="(tab,index) in tabBars" :key="tab.id" class="uni-tab-item" :id="tab.id" :data-current="index" @click="ontabtap">
				<text class="uni-tab-item-title" :class="tabIndex==index ? 'uni-tab-item-title-active' : ''">{{tab.name}}</text>
			</view>
		</scroll-view>
		<view :style="{'backgroundColor': bgColor}">
			<view class="summary-cards">
				<uni-card title="运行机组" thumbnail="" extra="" note="">
					<view class="view" :style="{flexDirection:'row'}">
						<text class="card-text">7台</text>
					</view>
				</uni-card>
				<uni-card title="停运机组" thumbnail="" extra="" note="">
					<view class="view" :style="{flexDirection:'row'}">
						<text class="card-text" style="color:red;">12台</text>
					</view>
				</uni-card>
				<uni-card title="总装机容量" thumbnail="" extra="" note="">
					<view class="view" :style="{flexDirection:'row'}">
						<text class="card-text">9072.00<br />MW</text>
					</view>
				</uni-card>
				<uni-card title="运行机组" thumbnail="" extra="" note="">
					<view class="view" :style="{flexDirection:'row'}">
						<text class="card-text">7台</text>
					</view>
				</uni-card>
			</view>

		</view>
		<swiper :current="tabIndex" class="swiper-box" :style="{'flex': 1,'backgroundColor': bgColor,'height':'100%'}"
		 :duration="300" @change="ontabchange">
			<swiper-item class="swiper-item">
				<scroll-view class="scroll-v list" enableBackToTop="true" scroll-y>
					<view class="jz-item" v-for="(item,index) in itemlist">
						<dc-info></dc-info>
					</view>

				</scroll-view>
				<!-- <scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper" @scrolltolower="lower"
				 @scroll="scroll">
					<view class="scroll-view-item uni-bg-red">
						<dc-info></dc-info>
					</view>
					<view class="scroll-view-item uni-bg-red">
						<dc-info></dc-info>
					</view>
					<view class="scroll-view-item uni-bg-red">
						<dc-info></dc-info>
					</view>
				</scroll-view> -->
			</swiper-item>
		</swiper>
	</view>
</template>
<script>
	import dcInfo from './dc-info.vue';
	import uniCard from "@/components/uni-card/uni-card"
	// 缓存每页最多
	const MAX_CACHE_DATA = 100;
	// 缓存页签数量
	const MAX_CACHE_PAGE = 3;


	const newsData = {
		data0: {
			"datetime": "40分钟前",
			"article_type": 0,
			"title": "uni-app行业峰会频频亮相，开发者反响热烈!",
			"source": "DCloud",
			"comment_count": 639
		},
		data1: {
			"datetime": "一天前",
			"article_type": 1,
			"title": "DCloud完成B2轮融资，uni-app震撼发布!",
			"image_url": "https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/shuijiao.jpg?imageView2/3/w/200/h/100/q/90",
			"source": "DCloud",
			"comment_count": 11395
		},
		data2: {
			"datetime": "一天前",
			"article_type": 2,
			"title": "中国技术界小奇迹：HBuilder开发者突破200万",
			"image_url": "https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/muwu.jpg?imageView2/3/w/200/h/100/q/90",
			"source": "DCloud",
			"comment_count": 11395
		}
	};

	export default {
		components: {
			dcInfo,
			uniCard
		},
		data() {
			return {
				itemlist: [{
					},
					{},
					{},
					{},
					{},
					{},
					{}
				],
				bgColor: "RGB(242,241,246)",
				newsList: [],
				cacheTab: [],
				tabIndex: 0,
				tabBars: [{
					name: '火电',
					id: 'rmdc',
					data: {
						desc: "11"
					}
				}, {
					name: '燃机',
					id: 'rqdc',
					data: {
						desc: "22"
					}
				}, {
					name: '水电',
					id: 'tiyu',
					data: {
						desc: "33"
					}
				}],
				scrollInto: "",
				scrollTop: 0,
				old: {
					scrollTop: 0
				},
				showTips: false,
				navigateFlag: false,
				pulling: false,
				refreshIcon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAMAAABg3Am1AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAB5QTFRFcHBw3Nzct7e39vb2ycnJioqK7e3tpqam29vb////D8oK7wAAAAp0Uk5T////////////ALLMLM8AAABxSURBVHja7JVBDoAgDASrjqj//7CJBi90iyYeOHTPMwmFZrHjYyyFYYUy1bwUZqtJIYVxhf1a6u0R7iUvWsCcrEtwJHp8MwMdvh2amHduiZD3rpWId9+BgPd7Cc2LIkPyqvlQvKxKBJ//Qwq/CacAAwDUv0a0YuKhzgAAAABJRU5ErkJggg=="
			}
		},
		onLoad() {
			setTimeout(() => {
				this.tabBars.forEach((tabBar) => {
					this.newsList.push({
						data: [],
						isLoading: false,
						refreshText: "",
						loadingText: '加载更多...'
					});
				});
			}, 350)
		},
		methods: {
			getList(index) {
				let activeTab = this.newsList[index];
				let list = [];
				for (let i = 1; i <= 10; i++) {
					let item = Object.assign({}, newsData['data' + Math.floor(Math.random() * 5)]);
					item.id = this.newGuid();
					list.push(item);
				}
				activeTab.data = activeTab.data.concat(list);
			},
			goDetail(e) {
				if (this.navigateFlag) {
					return;
				}
				this.navigateFlag = true;
				uni.navigateTo({
					url: './detail/detail?title=' + e.title
				});
				setTimeout(() => {
					this.navigateFlag = false;
				}, 200)
			},
			close(index1, index2) {
				uni.showModal({
					content: '是否删除本条信息？',
					success: (res) => {
						if (res.confirm) {
							this.newsList[index1].data.splice(index2, 1);
						}
					}
				})
			},
			loadMore(e) {
				setTimeout(() => {
					this.getList(this.tabIndex);
				}, 500)
			},
			ontabtap(e) {
				let index = e.target.dataset.current || e.currentTarget.dataset.current;
				this.switchTab(index);
			},
			ontabchange(e) {
				let index = e.target.current || e.detail.current;
				this.switchTab(index);
			},
			switchTab(index) {
				if (this.newsList[index].data.length === 0) {
					this.getList(index);
				}

				if (this.tabIndex === index) {
					return;
				}

				// 缓存 tabId
				if (this.newsList[this.tabIndex].data.length > MAX_CACHE_DATA) {
					let isExist = this.cacheTab.indexOf(this.tabIndex);
					if (isExist < 0) {
						this.cacheTab.push(this.tabIndex);
						//console.log("cache index:: " + this.tabIndex);
					}
				}

				this.tabIndex = index;
				this.scrollInto = this.tabBars[index].id;

				// 释放 tabId
				if (this.cacheTab.length > MAX_CACHE_PAGE) {
					let cacheIndex = this.cacheTab[0];
					this.clearTabData(cacheIndex);
					this.cacheTab.splice(0, 1);
					//console.log("remove cache index:: " + cacheIndex);
				}
			},
			clearTabData(e) {
				this.newsList[e].data.length = 0;
				this.newsList[e].loadingText = "加载更多...";
			},
			refreshData() {},
			onrefresh(e) {
				var tab = this.newsList[this.tabIndex];
				if (!tab.refreshFlag) {
					return;
				}
				tab.refreshing = true;
				tab.refreshText = "正在刷新...";

				setTimeout(() => {
					this.refreshData();
					this.pulling = true;
					tab.refreshing = false;
					tab.refreshFlag = false;
					tab.refreshText = "已刷新";
					setTimeout(() => { // TODO fix ios和Android 动画时间相反问题
						this.pulling = false;
					}, 500);
				}, 2000);
			},
			onpullingdown(e) {
				var tab = this.newsList[this.tabIndex];
				if (tab.refreshing || this.pulling) {
					return;
				}
				if (Math.abs(e.pullingDistance) > Math.abs(e.viewHeight)) {
					tab.refreshFlag = true;
					tab.refreshText = "释放立即刷新";
				} else {
					tab.refreshFlag = false;
					tab.refreshText = "下拉可以刷新";
				}
			},
			newGuid() {
				let s4 = function() {
					return (65536 * (1 + Math.random()) | 0).toString(16).substring(1);
				}
				return (s4() + s4() + "-" + s4() + "-4" + s4().substr(0, 3) + "-" + s4() + "-" + s4() + s4() + s4()).toUpperCase();
			}
		}
	}
</script>

<style>
	/* #ifndef APP-PLUS */
	page {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* #endif */

	.tabs {
		flex: 1;
		flex-direction: column;
		overflow: hidden;
		background-color: #ffffff;
		/* #ifdef MP-ALIPAY || MP-BAIDU */
		height: 100vh;
		/* #endif */
	}

	.scroll-h {
		width: 750upx;
		height: 80upx;
		flex-direction: row;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
		/* flex-wrap: nowrap; */
		/* border-color: #cccccc;
		border-bottom-style: solid;
		border-bottom-width: 1px; */
	}

	.line-h {
		height: 1upx;
		background-color: #cccccc;
	}

	.uni-tab-item {
		/* #ifndef APP-PLUS */
		display: inline-block;
		/* #endif */
		flex-wrap: nowrap;
		padding-left: 34upx;
		padding-right: 34upx;
		width: 33.33%;
		text-align: center;

	}

	.uni-tab-item-title {
		color: #555;
		font-size: 30upx;
		height: 80upx;
		line-height: 80upx;
		flex-wrap: nowrap;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
	}

	.uni-tab-item-title-active {
		color: #007AFF;
	}

	.swiper-box {
		flex: 1;
	}

	.swiper-item {
		flex: 1;
		flex-direction: row;
	}

	.scroll-v {
		flex: 1;
		/* #ifndef MP-ALIPAY */
		flex-direction: column;
		/* #endif */
		width: 750upx;
	}

	.update-tips {
		position: absolute;
		left: 0;
		top: 41px;
		right: 0;
		padding-top: 5px;
		padding-bottom: 5px;
		background-color: #FDDD9B;
		align-items: center;
		justify-content: center;
		text-align: center;
	}

	.update-tips-text {
		font-size: 14px;
		color: #ffffff;
	}

	.refresh {
		width: 750upx;
		height: 64px;
		justify-content: center;
	}

	.refresh-view {
		flex-direction: row;
		flex-wrap: nowrap;
		align-items: center;
		justify-content: center;
	}

	.refresh-icon {
		width: 30px;
		height: 30px;
		transition-duration: .5s;
		transition-property: transform;
		transform: rotate(0deg);
		transform-origin: 15px 15px;
	}

	.refresh-icon-active {
		transform: rotate(180deg);
	}

	.loading-icon {
		width: 20px;
		height: 20px;
		margin-right: 5px;
		color: #999999;
	}

	.loading-text {
		margin-left: 2px;
		font-size: 16px;
		color: #999999;
	}

	.loading-more {
		align-items: center;
		justify-content: center;
		padding-top: 10px;
		padding-bottom: 10px;
		text-align: center;
	}

	.loading-more-text {
		font-size: 28upx;
		color: #999;
	}

	.summary-cards {
		display: flex;
		flex-direction: row;
		justify-content: center;
		padding: 5upx;
	}

	.card-text {
		font-size: 38upx;
		text-align: center;
	}

	.uni-card {
		width: 170upx;
		margin-right: 5upx;
		margin-left: 5upx;
	}

	.uni-card__header-title-text {
		font-size: 1upx !important
	}

	.view {
		text-align: center;
	}

	.scroll-Y {
		height: 300upx;
	}
	
	.jz-item {
		margin: 3px;
		}
</style>
