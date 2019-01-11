<template>
	<view class="wrap">
		<scroll-view scroll-y :style="{height: scrollHeight+'px'}">
			<view class="item header">
				<view class="head">
					<image :src="info.head" mode=""></image>
					<view class="info">
						{{info.nickname}}
					</view>
				</view>
				<view class="code" @tap="goCode">
					<image src="../../static/image/code.png"></image>
				</view>
			</view>
			
			<view class="item">
				<i-cell-group>
					<i-cell title="名称" is-link @tap="editName">
						<text slot="footer">{{info.ader_name||''}}</text>
					</i-cell>
					<i-cell title="电话" is-link @tap="editTel">
						<text slot="footer">{{info.tel||''}}</text>
					</i-cell>
					<i-cell title="地址" is-link @tap="editLocation">
						<text slot="footer">{{info.location||''}}</text>
					</i-cell>
					<i-cell title="服务内容" is-link @tap="editService">
						<text slot="footer">{{info.service||''}}</text>
					</i-cell>
				</i-cell-group>
			</view>
			
			<view class="item">
				<i-cell-group>
					<i-cell title="附近" is-link @tap="goNearby">
						<text slot="footer"></text>
					</i-cell>
				</i-cell-group>
			</view>
			
			<view class="item">
				<i-cell-group>
					<i-cell title="发布" is-link @tap="goList(1)">
						<text slot="footer">{{info.n_ad}}</text>
					</i-cell>
					<i-cell title="收藏" is-link @tap="goList(2)">
						<text slot="footer">{{info.n_collect}}</text>
					</i-cell>
				</i-cell-group>
			</view>
		</scroll-view>

		<view class="tab-bar">
			<i-tab-bar current="mine" @change="barChange">
				<i-tab-bar-item key="home" icon="activity" current-icon="activity_fill" title="Home"></i-tab-bar-item>
				<i-tab-bar-item key="mine" icon="mine" current-icon="mine_fill" title="My"></i-tab-bar-item>
			</i-tab-bar>
		</view>

	</view>
</template>

<script>
	import service from '../../service.js';
	export default {
		data() {
			return {
				scrollHeight: '',
				info: []
			};
		},
		onLoad() {
			let _this = this;
			uni.getSystemInfo({
				success(res) {
					_this.scrollHeight = res.windowHeight - 50;
				}
			})
		},
		onShow() {
			let info = service.getUsers();
			uni.request({
				url: this.$requestUrl + 'get_advertiser',
				method: 'GET',
				data: {
					openid: info.openid
				},
				success: res => {
					this.info = res.data.data;
				},
				fail: () => {},
				complete: () => {}
			});
			
			
			uni.request({
				url: this.$requestUrl+'get_nearby_advertise',
				method: 'GET',
				data: {
					ader_id: 1
				},
				success: res => {},
				fail: () => {},
				complete: () => {}
			});
			
			
		},
		methods: {
			editName() {
				let detail = {
					title: '设置名称',
					type: 'input',
					name: 'ader_name',
					value: this.info.ader_name
				}
				uni.redirectTo({
					url: "../edit/edit?detailData=" + JSON.stringify(detail)
				})
			},
			editTel() {
				let detail = {
					title: '设置联系方式',
					type: 'input',
					name: 'tel',
					value: this.info.tel
				}
				uni.redirectTo({
					url: "../edit/edit?detailData=" + JSON.stringify(detail)
				})
			},
			editLocation() {
				let _this = this;
				uni.chooseLocation({
					success(res) {
						uni.showLoading({
							title: '',
							mask: false
						});
						let location = res.address;
						let latitude = res.latitude;
						let longitude = res.longitude;
						uni.request({
							url: _this.$requestUrl+'update_advertiser',
							method: 'POST',
							header: {
								'content-type': 'application/x-www-form-urlencoded'
							},
							data: {
								openid: _this.info.openid,
								location: location,
								latitude: latitude,
								longitude: longitude
							},
							success: res => {
								console.log(location);
								_this.info.location = location;
							},
							fail: () => {},
							complete: () => {
								uni.hideLoading()
							}
						});
					}
				})
			},
			editService() {
				let detail = {
					title: '设置服务内容',
					type: 'textarea',
					name: 'service',
					value: this.info.service
				}
				uni.redirectTo({
					url: "../edit/edit?detailData=" + JSON.stringify(detail)
				})
			},
			barChange(e) {
				let key = e.detail.key;
				if (key == 'home') {
					let detail = {}
					uni.navigateTo({
						url: "../ad/ad?detailData=" + JSON.stringify(detail)
					})
				}
			},
			goList(e) {
				let detail = {
					type: e
				}
				uni.navigateTo({
					url: "../list/list?detailData=" + JSON.stringify(detail)
				})
			},
			goCode() {
				// 判断是否完善信息
				if (this.info.is_complete) {
					let detail = {
						ader_name: this.info.ader_name,
						tel: this.info.tel,
						location: this.info.location,
						service: this.info.service,
						qrcode: this.info.qrcode
					}
					uni.navigateTo({
						url: '../code/code?detailData=' + JSON.stringify(detail)
					});
				} else{
					uni.showToast({
						title: '请先完善个人信息',
						icon: 'none',
						mask: false,
						duration: 1000
					});
					setTimeout(function(){
						uni.navigateTo({
							url: "../ader-edit/ader-edit"
						})
					}, 1000)
				}
			},
			goNearby() {
				uni.navigateTo({
					url: "../nearby/nearby"
				})
			}
		}
	}
</script>

<style>
	page {
		height: 100%;
		background-color: #f4f4f4;
	}
	
	.item {
		background-color: #fff;
		margin-bottom: 10px;
	}

	.header {
		padding: 20px;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.head {
		display: flex;
	}

	.head image {
		width: 60px;
		height: 60px;
		margin: 0 15px;
	}

	.info {
		font-size: 60upx;
	}

	.code {
		position: relative;
		padding-right: 30px;
	}

	.code image {
		width: 30px;
		height: 30px;
	}

	.code:after {
		content: " ";
		display: inline-block;
		width: 6px;
		height: 6px;
		position: absolute;
		top: 50%;
		right: 2px;
		border-width: 2px 2px 0 0;
		border-color: #dddee1;
		border-style: solid;
		transform: translateY(-50%) matrix(.71, .71, -.71, .71, 0, 0)
	}
</style>
