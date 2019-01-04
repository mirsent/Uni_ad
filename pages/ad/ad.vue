<template>
	<view class="content">

		<view class="tabs">
			<i-tabs :current="currentScroll" scroll @change="handleChangeScroll">
				<i-tab key="0" title="全部"></i-tab>
				<i-tab v-for="tag in tagData" :key="tag.id" :title="tag.tag_name"></i-tab>
			</i-tabs>
		</view>

		<scroll-view scroll-y :style="{height: scrollHeight+'px'}">
			<view class="ad-list">

				<view class="list-cell" v-for="(ad,index) in adData" :key="index" @tap="goDetail(ad.id)">

					<!-- <view class="list-img">
						<image src="../../static/image/listimg.jpg" mode="widthFix"></image>
					</view> -->

					<view class="list-title">
						[{{ad.tag_name}}] {{ad.ad_title}}
					</view>

					<!-- <view class="list-brief">
						<view class="text-2-ellipsis">
							本周六日，到店消费满88元，即可免费赠送咖啡一杯哦！
						</view>
					</view> -->

					<view class="flex-rowsb list-footer">
						<view>
							<text class="brief-e">{{ad.publish_time}}</text>
							<text class="brief-e">{{ad.ader_name}}</text>
						</view>
						<view>
							<text class="brief-e">{{ad.visited}}看</text>
						</view>
					</view>

				</view>

			</view>
		</scroll-view>

		<view class="tab-bar">
			<view class="add" @tap="goAdd">
				<view class="add-box">
					<i-icon type="add" size="30" color="#fff" />
				</view>
			</view>

			<i-tab-bar current="home" @change="barChange">
				<i-tab-bar-item key="home" icon="activity" current-icon="activity_fill" title="Home"></i-tab-bar-item>
				<i-tab-bar-item key="mine" icon="mine" current-icon="mine_fill" title="My"></i-tab-bar-item>
			</i-tab-bar>
		</view>

		<button class="login-btn" open-type="getUserInfo" v-if="!authed" @getuserinfo="getUserInfo"></button>

	</view>
</template>

<script>
	import service from '../../service.js';
	export default {
		data() {
			return {
				authed: false,
				openid: '',
				
				currentScroll: 0,
				scrollHeight: '',
				tagData: [],
				adData: []
			}
		},
		onLoad() {
			uni.showLoading({
				title: '',
				mask: false
			});
			
			let _this = this;
			// #ifdef MP-WEIXIN
			wx.getSetting({
				success(res) {
					if (res.authSetting['scope.userInfo']) {
						_this.authed = true;
					}
				}
			})
			// #endif
			
			uni.login({
				provider: 'weixin',
				success: res => {
					uni.request({
						url: this.$requestUrl + 'code_2_session',
						method: 'GET',
						data: {
							js_code: res.code
						},
						success: res => {
							let info = res.data.data;
							this.openid = info.openid
							service.addUser(info)
						},
						fail: () => {},
						complete: () => {}
					});
				},
				fail: () => {},
				complete: () => {
					this.getTags();
					this.getAdvertises();
				}
			});
		},
		onReady() {
			uni.getSystemInfo({
				success: (res) => {
					let windowHeight = res.windowHeight;

					let query = uni.createSelectorQuery();
					query.select(".tabs").boundingClientRect();
					query.select(".tab-bar").boundingClientRect();
					query.exec(data => {
						let tab = data[0];
						let bar = data[1];
						this.scrollHeight = windowHeight - tab.height - bar.height - 8;
					});
				}
			})
		},
		methods: {
			getUserInfo(e) {
				this.authed = true;
				uni.request({
					url: this.$requestUrl+'update_advertiser',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						openid: this.openid,
						head: e.detail.userInfo.avatarUrl,
						nickname: e.detail.userInfo.nickName
					},
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},
			getTags() {
				uni.request({
					url: this.$requestUrl + 'get_tag_list',
					method: 'GET',
					success: res => {
						this.tagData = res.data.data;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			getAdvertises(tagId = '') {
				uni.request({
					url: this.$requestUrl + 'get_advertise_list',
					method: 'GET',
					data: {
						tag_id: tagId
					},
					success: res => {
						this.adData = res.data.data;
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading();
					}
				});
			},
			handleChangeScroll(e) {
				let tagId = e.detail.key;
				this.currentScroll = tagId;
				this.getAdvertises(tagId);
			},
			goAdd() {
				let detail = {}
				uni.navigateTo({
					url: "../ad-add/ad-add?detailData=" + JSON.stringify(detail)
				})
			},
			goDetail(e) {
				let detail = {
					advertise_id: e
				}
				uni.navigateTo({
					url: "../ad-detail/ad-detail?detailData=" + JSON.stringify(detail)
				})
			},
			barChange(e) {
				let key = e.detail.key;
				if (key == 'mine') {
					let detail = {}
					uni.navigateTo({
						url: "../my/my?detailData=" + JSON.stringify(detail)
					})
				}
			}
		},
		onShareAppMessage() {
			return {
				title: '纯粹的广告',
				path: '/pages/ad/ad'
			}
		}
	}
</script>

<style>
	@import "../../common/list.css";

	.add {
		width: 60px;
		height: 30px;
		border: 1px solid #e9eaec;
		border-radius: 0 0 50% 50%/0 0 100% 100%;
		border-top: none;
		position: relative;
		z-index: 999;
		left: 50%;
		bottom: -31px;
		margin-left: -30px;
		background-color: #fff;
	}

	.add-box {
		width: 44px;
		height: 44px;
		border-radius: 50%;
		background-color: #ff3975;
		display: flex;
		justify-content: center;
		align-items: center;
		position: absolute;
		left: 50%;
		margin-left: -22px;
		top: -22px;
	}

	.content {
		background-color: #f6f6f6;
	}

	.tabs {
		margin-bottom: 8px;
	}

	.login-btn {
		position: absolute;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		z-index: 9999;
		background: transparent;
	}

	.login-btn:after {
		border: 0;
	}
</style>
