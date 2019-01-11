<template>
	<view>
		<view class="list">
			<view class="list-cell" v-for="(ad,index) in adData" :key="index"
				@tap="goDetail(ad)">
				<view class="list-cell-title">
					<view class="title-header text-ellipsis">
						{{ad.ad_title}}
					</view>
					<view class="title-footer">
						{{ad.distance}}
					</view>
				</view>
				<view class="list-cell-brief text-2-ellipsis">
					{{ad.ad_brief}}
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				adData: []
			};
		},
		onShow() {
			uni.showLoading({
				title: '',
				mask: false
			});
			let _this = this;
			uni.getLocation({
				type: 'wgs84',
				success: res => {
					uni.request({
						url: _this.$requestUrl+'get_nearby_advertise',
						method: 'GET',
						data: {
							latitude: res.latitude,
							longitude: res.longitude
						},
						success: res => {
							_this.adData = res.data.data;
						},
						fail: () => {},
						complete: () => {
							uni.hideLoading()
						}
					});
				},
				fail: () => {},
				complete: () => {}
			});
		},
		methods: {
			goDetail(e) {
				let detail = {
					ad_id: e.id,
					tag_id: e.tag_id
				}
				uni.navigateTo({
					url: "../ad-detail/ad-detail?detailData=" + JSON.stringify(detail)
				})
			},
		}
	}
</script>

<style>
	.list {
		padding-top: 10px;
		padding-left: 10px;
	}

	.list-cell {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 10px 10px 15px 0;
		border-bottom: 1px solid #d9d9d9;
	}

	.title-header {
		font-size: 32upx;
		max-width: 400upx;
		margin-bottom: 8px;
	}

	.title-footer {
		font-size: 28upx;
		color: #909090;
	}

	.list-cell-brief {
		font-size: 28upx;
		color: #888;
		background-color: #f2f2f2;
		border-radius: 5px;
		padding: 3px 8px;
		max-width: 300upx;
	}
</style>
