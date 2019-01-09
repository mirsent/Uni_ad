<template>
	<view>
		<view class="input" v-if="type == 'input'">
			<input type="text" :value="value" focus @input="valueChange" />
			<view class="icon" v-show="value" @tap="clear">
				<i-icon type="close" />
			</view>
		</view>
		<view class="textarea" v-if="type == 'textarea'">
			<textarea :value="value" @input="valueChange" />
			<view class="icon" v-show="value" @tap="clear">
				<i-icon type="close" />
			</view>
		</view>
		<view class="btn-group">
			<button size="mini" type="primary" @tap="save">完成</button>
			<button size="mini" type="default" @tap="cancle">取消</button>
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	export default {
		data() {
			return {
				openid: '',
				type: '',
				name: '',
				value: ''
			};
		},
		onLoad(e) {
			this.openid = service.getUsers()['openid'];
			let info = JSON.parse(e.detailData);
			this.type = info.type
			this.name = info.name
			this.value = info.value
			uni.setNavigationBarTitle({
				title: info.title
			});
		},
		methods: {
			valueChange(e) {
				this.value = e.detail.value
			},
			clear() {
				this.value = ''
			},
			save() {
				if (this.value == '') {
					uni.showToast({
						title: '还没有填入内容',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					return;
				}
				uni.request({
					url: this.$requestUrl+'update_advertiser_once',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						openid: this.openid,
						name: this.name,
						value: this.value
					},
					success: res => {
						this.goMy()
					},
					fail: () => {},
					complete: () => {}
				});
			},
			cancle() {
				this.goMy()
			},
			goMy() {
				uni.redirectTo({
					url: '../my/my'
				});
			}
		}
	}
</script>

<style>
	page{
		background-color: #f3f3f3;
	}
	
	.input{
		background: #fff;
		padding: 15px 10px;
		display: flex;
		align-items: center;
	}
	.input input{
		flex: 1;
	}
	.icon{
		width: 20px;
	}
	
	.textarea{
		background: #fff;
		padding: 15px 10px;
		position: relative;
	}
	textarea{
		width: 100%;
	}
	.textarea .icon{
		position: absolute;
		right: 15px;
		bottom: 15px;
	}
	
	.btn-group{
		margin: 10px;
		display: flex;
		justify-content: flex-start;
	}
	.btn-group button{
		margin: 0;
		margin-right: 8px;
	}
</style>
