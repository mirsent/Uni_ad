<template>
	<view>

		<view class="info">
			<view class="info-img">
				<i-avatar src="https://i.loli.net/2017/08/21/599a521472424.jpg" size="large"></i-avatar>
			</view>
			<view class="info-name">
				水果批发
			</view>
			<view class="info-list">
				<view class="list-cell">
					<i-icon type="service" color="#fff" size="20" />
					<text class="list-title">服务内容 ...</text>
				</view>
				<view class="list-cell">
					<i-icon type="coordinates" color="#fff" size="20" />
					<text class="list-title">天津市和平区</text>
				</view>
				<view class="list-cell">
					<i-icon type="mobilephone" color="#fff" size="20" />
					<text class="list-title" @tap="call(18222267325)">18222267325</text>
				</view>
			</view>
		</view>
		
		<scroll-view scroll-y :style="{height: scroll_height+'px'}">
			<view class="ad-list">
				<view class="list-cell" @tap="goDetail">
					<view class="list-title">
						[水果] 新鲜采摘橙子30元5斤包邮
					</view>
					<view class="flex-rowsb list-footer">
						<view>
							<text class="brief-e">2018-12-28 15:11</text>
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
							<text class="brief-e">2018-12-28 15:09</text>
						</view>
						<view>
							<text class="brief-e">71看</text>
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
				scroll_height: ''
			};
		},
		onLoad(e) {
			// let info = JSON.parse(e.detailData);
		},
		onReady() {
			uni.getSystemInfo({
				success: (res) => {
					let windowHeight = res.windowHeight;
		
					let query = uni.createSelectorQuery();
					query.select(".info").boundingClientRect();
					query.exec(data => {
						let info = data[0];
						this.scroll_height = windowHeight - info.height;
					});
				}
			})
		},
		methods: {
			call(e) {
				uni.makePhoneCall({
					phoneNumber: e.toString()
				});
			},
			goDetail() {
				let detail = {
					
				}
				uni.navigateTo({
					url: "../advertise-detail/advertise-detail?detailData=" + JSON.stringify(detail)
				})
			}
		}
	}
</script>

<style>
	@import "../../common/list.css";
	.info {
		background-image: radial-gradient(#56a39f, #6c9491);
		padding: 20px;
	}

	.info-img {
		text-align: center;
		padding: 15px 0;
	}

	.info-name {
		font-size: 36upx;
		font-weight: bold;
		color: #fff;
		text-align: center;
		margin-bottom: 20px;
	}

	.info-list .list-title {
		font-size: 28upx;
		color: #fff;
		margin-left: 8px;
	}
</style>
