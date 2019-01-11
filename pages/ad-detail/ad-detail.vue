<template>
	<view class="content">
		<view v-if="adInfo.ad_imgs">
			<swiper :indicator-dots="indicator_dots" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(img,index) in adInfo.ad_imgs_arr" :key="index">
					<view class="swiper-item">
						<image :src="img"></image>
					</view>
				</swiper-item>
			</swiper>
		</view>

		<view class="ad-list">
			<view class="list-cell">
				<view class="list-title">
					[{{adInfo.tag_name}}] {{adInfo.ad_title}}
				</view>
				<view class="list-brief">
					{{adInfo.ad_brief}}
				</view>
				<view class="flex-rowsb list-footer">
					<view>
						<text class="brief-e">{{adInfo.publish_time}}</text>
						<text class="brief-e">{{adInfo.ader_name}}</text>
					</view>
					<view>
						<text class="brief-e">{{adInfo.visited}}看</text>
					</view>
				</view>
			</view>
		</view>

		<view class="tab">
			<view class="tab-more" @tap="goAdvertiser(adInfo.publisher_id)">
				查看此人更多
			</view>
			<view class="action-group">
				<i-icon type="homepage" size="30" color="#5a5e61" @tap="goHome" />
				<i-icon v-if="isCollect" type="collection_fill" size="30" color="#5a5e61" @tap="collectNo" />
				<i-icon v-else type="collection" size="30" color="#5a5e61" @tap="collect" />
				<button open-type="share">
					<i-icon type="share" size="30" color="#5a5e61" />
				</button>
			</view>
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	export default {
		data() {
			return {
				adId: '',
				tagId: '',
				aderId: '',
				isCollect: '',
				indicator_dots: true,
				autoplay: true,
				interval: 3000,
				duration: 1000,
				adInfo: []
			};
		},
		onLoad(e) {
			this.aderId = service.getUsers()['id'];
			let info = JSON.parse(e.detailData);
			this.adId = info.ad_id
			this.tagId = info.tag_id
			this.getAdDetail()
			this.getCollect()
		},
		methods: {
			getAdDetail() {
				uni.showLoading({
					title: '',
					mask: false
				});
				uni.request({
					url: this.$requestUrl + 'get_advertise_detail',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						ad_id: this.adId,
						tag_id: this.tagId,
						ader_id: this.aderId
					},
					success: res => {
						this.adInfo = res.data.data;
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				});
			},
			getCollect() {
				uni.request({
					url: this.$requestUrl + 'is_collect',
					method: 'GET',
					data: {
						ad_id: this.adId,
						ader_id: this.aderId
					},
					success: res => {
						this.isCollect = res.data.data;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			collect() {
				uni.request({
					url: this.$requestUrl + 'collect',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						ad_id: this.adId,
						ader_id: this.aderId
					},
					success: res => {
						this.isCollect = 1;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			collectNo() {
				uni.request({
					url: this.$requestUrl+'cancel_collect',
					method: 'GET',
					data: {
						ad_id: this.adId,
						ader_id: this.aderId
					},
					success: res => {
						this.isCollect = 0;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			goAdvertiser(e) {
				let detail = {
					advertiser_id: e
				}
				uni.navigateTo({
					url: "../ader/ader?detailData=" + JSON.stringify(detail)
				})
			},
			goHome() {
				uni.redirectTo({
					url: '../ad/ad',
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			}
		},
		onShareAppMessage() {
			let imageUrl = '';
			let detail = {
				advertise_id: this.aderId
			}
			if (this.adInfo.ad_imgs) {
				imageUrl = this.adInfo.ad_imgs_arr[0]
			}
			return {
				title: this.adInfo.ad_title,
				imageUrl: imageUrl,
				path: "page/ad-detail/ad-detail?detailData=" + JSON.stringify(detail)
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

	.tab {
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

	.tab-more {
		font-size: 28upx;
		background: #ebeef3;
		border-radius: 20px;
		padding: 5px 10px;
	}

	.action-group {
		display: flex;
	}

	.action-group button {
		padding: 0;
		margin: 0;
		background: none;
		line-height: 0;
	}

	.action-group button:after {
		border: 0;
	}

	.action-group i-icon {
		margin-left: 12px;
	}
</style>
