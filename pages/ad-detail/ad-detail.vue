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
				adInfo: []
			};
		},
		onLoad(e) {
			let info = JSON.parse(e.detailData);
			this.getAdDetail(info.advertise_id)
		},
		methods: {
			getAdDetail(id) {
				uni.request({
					url: this.$requestUrl+'get_advertise_detail',
					method: 'GET',
					data: {
						advertise_id: id
					},
					success: res => {
						console.log(res.data);
						this.adInfo = res.data.data;
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
