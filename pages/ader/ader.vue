<template>
	<view>

		<view class="info">
			<view class="info-img">
				<i-avatar src="https://i.loli.net/2017/08/21/599a521472424.jpg" size="large"></i-avatar>
			</view>
			<view class="info-name">
				{{aderInfo.ader_name}}
			</view>
			<view class="info-list">
				<view class="list-cell">
					<i-icon type="service" color="#fff" size="20" />
					<text class="list-title">{{aderInfo.service}}</text>
				</view>
				<view class="list-cell">
					<i-icon type="coordinates" color="#fff" size="20" />
					<text class="list-title">{{aderInfo.location}}</text>
				</view>
				<view class="list-cell">
					<i-icon type="mobilephone" color="#fff" size="20" />
					<text class="list-title" @tap="call(aderInfo.tel)">{{aderInfo.tel}}</text>
				</view>
			</view>
		</view>
		
		<scroll-view scroll-y :style="{height: scrollHeight+'px'}">
			<view class="ad-list">
				<view class="list-cell" @tap="goDetail(ad.id)" v-for="(ad,index) in adData" :key="index">
					<view class="list-title">
						[{{ad.tag_name}}] {{ad.ad_title}}
					</view>
					<view class="flex-rowsb list-footer">
						<view>
							<text class="brief-e">{{ad.publish_datetime}}</text>
						</view>
						<view>
							<text class="brief-e">{{ad.visited}}看</text>
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
				scrollHeight: '',
				aderInfo: [],
				adData: []
			};
		},
		onLoad(e) {
			let info = JSON.parse(e.detailData);
			this.getAdvertiser(info.advertiser_id)
		},
		onReady() {
			uni.getSystemInfo({
				success: (res) => {
					let windowHeight = res.windowHeight;
		
					let query = uni.createSelectorQuery();
					query.select(".info").boundingClientRect();
					query.exec(data => {
						let info = data[0];
						this.scrollHeight = windowHeight - info.height;
					});
				}
			})
		},
		methods: {
			getAdvertiser(id) {
				uni.request({
					url: this.$requestUrl+'get_advertiser_info',
					method: 'GET',
					data: {
						advertiser_id: id
					},
					success: res => {
						let data = res.data.data;
						this.aderInfo = data.info
						this.adData = data.advertises
					},
					fail: () => {},
					complete: () => {}
				});
			},
			call(e) {
				uni.makePhoneCall({
					phoneNumber: e.toString()
				});
			},
			goDetail(e) {
				let detail = {
					advertise_id: e
				}
				uni.navigateTo({
					url: "../ad-detail/ad-detail?detailData=" + JSON.stringify(detail)
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
