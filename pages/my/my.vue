<template>
	<view class="wrap">
		<view class="item header">
			<view class="head">
				<image :src="info.head" mode=""></image>
			</view>
			<view class="info">
				<view class="info-top">
					{{info.nickname}}
				</view>
			</view>
		</view>
		
		
		<view class="item">
			<i-cell-group>
				<i-cell title="名称" is-link >
					<text slot="footer" @tap="editName">{{info.ader_name}}</text>
				</i-cell>
				<i-cell title="电话" is-link>
					<text slot="footer" @tap="editTel">{{info.tel}}</text>
				</i-cell>
				<i-cell title="地址" is-link>
					<text slot="footer" @tap="editLocation">{{info.location}}</text>
				</i-cell>
				<i-cell title="服务内容" is-link>
					<text slot="footer" @tap="editService">{{info.service}}</text>
				</i-cell>
			</i-cell-group>
		</view>
		
		<view class="item">
			<i-cell-group>
				<i-cell title="发布" is-link url="/pages/ad/ad">
				
				</i-cell>
				<i-cell title="收藏" is-link url="/pages/ad/ad">
				
				</i-cell>
			</i-cell-group>
		</view>
		
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
				info: []
			};
		},
		onLoad() {
			let info = service.getUsers();
			uni.request({
				url: this.$requestUrl+'get_advertiser',
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
				let detail = {
					title: '设置地址',
					type: 'textarea',
					name: 'location',
					value: this.info.location
				}
				uni.redirectTo({
					url: "../edit/edit?detailData=" + JSON.stringify(detail)
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
	}
	
	.head {
		margin: 0 15px;
	}
	
	.head image {
		width: 60px;
		height: 60px;
	}
	
	.info .info-top {
		font-size: 60upx;
	}
</style>
