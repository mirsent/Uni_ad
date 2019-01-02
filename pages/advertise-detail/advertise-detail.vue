<template>
	<view class="content">
		<swiper :indicator-dots="indicator_dots" :autoplay="autoplay" :interval="interval" :duration="duration">
			<swiper-item>
				<view class="swiper-item">
					<image src="../../static/image/listimg.jpg"></image>
				</view>
			</swiper-item>
		</swiper>

		<view class="ad-list">
			<view class="list-cell">
				<view class="list-title">
					[水果] 新鲜采摘橙子30元5斤包邮
				</view>
				<view class="list-brief">
					果型端庄、色泽鲜艳、风味浓甜、肉质脆嫩，无籽化渣，营养保健价值高，是不可多得的水果珍品。
				</view>
				<view class="flex-rowsb list-footer">
					<view>
						<text class="brief-e">2018-12-28 15:11</text>
						<text class="brief-e">Kim chi</text>
					</view>
					<view>
						<text class="brief-e">36看</text>
					</view>
				</view>
			</view>
		</view>
		
		<view class="tab">
			<view class="tab-more" @tap="goAdvertiser">
				查看此人更多
			</view>
			<view class="action-group">
				<i-icon type="homepage" size="30" color="#5a5e61" @tap="goHome"/>
				<i-icon type="collection" size="30" color="#5a5e61"/>
				<i-icon type="share" size="30" color="#5a5e61"/>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				indicator_dots: true,
				autoplay: true,
				interval: 3000,
				duration: 1000,
			};
		},
		onLoad(e) {
			// let info = JSON.parse(e.detailData);
		},
		methods: {
			goAdvertiser() {
				let detail = {
					
				}
				uni.redirectTo({
					url: "../advertiser/advertiser?detailData=" + JSON.stringify(detail)
				})
			},
			goHome() {
				uni.switchTab({
					url: "../advertise/advertise"
				})
			}
		}
	}
</script>

<style>
	@import "../../common/list.css";

	swiper {
		height: 200px;
	}

	.swiper-item image {
		width: 100%;
	}
	
	.tab{
		padding: 0 10px;
		background-color: #f8fcff;
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		height: 50px;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.tab-more{
		font-size: 28upx;
		background: #ebeef3;
		border-radius: 20px;
		padding: 5px 10px;
	}
	.action-group i-icon{
		margin-left: 12px;
	}
</style>
