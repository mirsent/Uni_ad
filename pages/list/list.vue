<template>
	<view>
		<view class="ad-list">
			<view class="list-cell" @tap="goDetail(ad)" v-for="(ad,index) in adData" :key="index">
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
	</view>
</template>

<script>
	import service from '../../service.js';
	export default {
		data() {
			return {
				aderId: '',
				adData: []
			};
		},
		onLoad(e) {
			this.aderId = service.getUsers()['id'];
			let info = JSON.parse(e.detailData);
			this.getAdList(info.type)
		},
		methods: {
			getAdList(type) {
				uni.showLoading({
					title: '',
					mask: false
				});
				uni.request({
					url: this.$requestUrl+'get_advertise_list',
					method: 'GET',
					data: {
						type: type,
						ader_id: this.aderId
					},
					success: res => {
						this.adData = res.data.data;
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				});
			},
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
	@import "../../common/list.css";
</style>
