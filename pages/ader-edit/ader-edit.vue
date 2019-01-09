<template>
	<view class="wrap">
		<view class="form">
			<view class="input-group">
				<view class="label">
					名称
				</view>
				<view class="input">
					<input type="text" :focus="nameFocus" :value="info.ader_name" @input="nameChange" />
				</view>
			</view>
			<view class="input-group">
				<view class="label">
					电话
				</view>
				<view class="input">
					<input type="text" :focus="telFocus" :value="info.tel" @input="telChange" />
				</view>
			</view>
			<view class="input-group input-textarea">
				<view class="label">
					地址
				</view>
				<view class="input">
					<textarea :focus="locationFocus" :value="info.location" @input="locationChange" />
				</view>
			</view>
			<view class="input-group input-textarea">
				<view class="label">
					服务内容
				</view>
				<view class="input">
					<textarea :focus="serviceFocus" :value="info.service" @input="serviceChange" />
				</view>
			</view>
		</view>
		
		<view class="btn">
			<button type="primary" @tap="save">保存</button>
		</view>
	</view>
</template>

<script>
	import service from '../../service.js';
	export default {
		data() {
			return {
				info: [],
				nameFocus: false,
				telFocus: false,
				locationFocus: false,
				serviceFocus: false
			};
		},
		onLoad() {
			this.info = service.getUsers();
		},
		methods: {
			nameChange(e) {
				this.info.ader_name = e.detail.value;
			},
			telChange(e) {
				this.info.tel = e.detail.value;
			},
			locationChange(e) {
				this.info.location = e.detail.value;
			},
			serviceChange(e) {
				this.info.service = e.detail.value;
			},
			save() {
				if (!this.info.ader_name) {
					uni.showToast({
						title: '请输入名称',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					this.nameFocus = true;
					return
				}
				if (!this.info.tel) {
					uni.showToast({
						title: '请输入电话',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					this.telFocus = true;
					return
				}
				if (!this.info.location) {
					uni.showToast({
						title: '请输入地址',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					this.locationFocus = true;
					return
				}
				if (!this.info.service) {
					uni.showToast({
						title: '请输入服务内容',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					this.serviceFocus = true;
					return
				}
				uni.request({
					url: this.$requestUrl+'update_advertiser',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						openid: this.info.openid,
						ader_name: this.info.ader_name,
						tel: this.info.tel,
						location: this.info.location,
						service: this.info.service,
						is_complete: 1
					},
					success: res => {
						uni.showToast({
							title: '保存成功',
							mask: false,
							duration: 1500
						});
						setTimeout(function(){
							uni.navigateBack({
								delta: 1
							});
						})
					},
					fail: () => {},
					complete: () => {}
				});
			}
		}
	}
</script>

<style>
	page{
		height: 100%;
	}
	.wrap{
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}
	.form {
		padding: 10px;
	}

	.input-group {
		font-size: 36upx;
		padding: 10px 20px;
		margin-bottom: 10px;
		border-radius: 5px;
		background-color: #e6ebfe;
		display: flex;
		align-items: center;
	}
	.input-textarea{
		align-items: flex-start;
	}

	.label {
		color: #8aa7f5;
		margin-right: 8px;
	}

	.input {
		flex: 1;
		color: #9496a2;
	}
	.input textarea{
		width: 100%;
		height: 60px;
	}
	
	.btn{
		margin-bottom: 30px;
	}
	.btn button{
		width: 460upx;
		background-color: #4d9dfe;
		border-radius: 50px;
		box-shadow: 0 2px 5px #73aff7;
	}
</style>
