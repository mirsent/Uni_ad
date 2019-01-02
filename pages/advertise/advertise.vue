<template>
	<view class="content">

		<view class="tabs">
			<i-tabs :current="current_scroll" scroll @change="handleChangeScroll">
				<i-tab key="1" title="招聘"></i-tab>
				<i-tab key="2" title="房产"></i-tab>
				<i-tab key="3" title="二手车"></i-tab>
				<i-tab key="4" title="宠物"></i-tab>
				<i-tab key="5" title="水果"></i-tab>
				<i-tab key="6" title="餐饮"></i-tab>
				<i-tab key="7" title="本地商务"></i-tab>
				<i-tab key="8" title="本地商务"></i-tab>
			</i-tabs>
		</view>

		<scroll-view scroll-y :style="{height: scroll_height+'px'}">
			<view class="ad-list">
				<view class="list-cell" @tap="goDetail">
					<view class="list-title">
						[水果] 新鲜采摘橙子30元5斤包邮
					</view>
					<view class="flex-rowsb list-footer">
						<view>
							<text class="brief-e">15:11</text>
							<text class="brief-e">Kim chi</text>
						</view>
						<view>
							<text class="brief-e">36看</text>
						</view>
					</view>
				</view>
				<view class="list-cell">
					<view class="list-title">
						[开发] 小程序开发
					</view>
					<view class="flex-rowsb list-footer">
						<view>
							<text class="brief-e">15:09</text>
							<text class="brief-e">d_dboy</text>
						</view>
						<view>
							<text class="brief-e">71看</text>
						</view>
					</view>
				</view>
				<view class="list-cell">
					<view class="list-img">
						<image src="../../static/image/listimg.jpg" mode="widthFix"></image>
					</view>
					<view class="list-title">
						[美食] 将疲惫一扫而光，然后优雅出发吧！
					</view>
					<view class="list-brief">
						<view class="text-2-ellipsis">
							本周六日，到店消费满88元，即可免费赠送咖啡一杯哦！
						</view>
					</view>
					<view class="flex-rowsb list-footer">
						<view>
							<text class="brief-e">14:56</text>
							<text class="brief-e">cafei</text>
						</view>
						<view>
							<text class="brief-e">104看</text>
						</view>
					</view>
				</view>
			</view>
		</scroll-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				current_scroll: 1,
				scroll_height: ''
			}
		},
		onLoad() {

		},
		onReady() {
			uni.getSystemInfo({
				success: (res) => {
					let windowHeight = res.windowHeight;

					let query = uni.createSelectorQuery();
					query.select(".tabs").boundingClientRect();
					query.exec(data => {
						let tab = data[0];
						this.scroll_height = windowHeight - tab.height - 24;
					});
				}
			})
		},
		methods: {
			handleChangeScroll(e) {
				let tagId = e.detail.key;
				this.current_scroll = tagId;
			},
			goDetail() {
				let detail = {
					
				}
				uni.navigateTo({
					url: "../advertise-detail/advertise-detail?detailData=" + JSON.stringify(detail)
				})
			}
		},
        onShareAppMessage() {
        	return {
        		title: '纯粹的广告',
        		path: '/pages/advertise/advertise'
        	}
        }
	}
</script>

<style>
	@import "../../common/list.css";
	.content {
		background-color: #f6f6f6;
	}

	.tabs {
		margin-bottom: 8px;
	}
</style>
