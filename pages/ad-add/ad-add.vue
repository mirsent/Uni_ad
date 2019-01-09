<template>
	<view class="wrap">
		<view class="form">
			<view class="input-group">
				<view class="label">
					标签
				</view>
				<view class="input">
					<picker class="picker-item" mode="selector" :range="tagData" range-key="tag_name" @change="tagChange">
						<view style="min-height: 1em;">{{tag}}</view>
					</picker>
				</view>
			</view>
			<view class="input-group">
				<view class="label">
					标题
				</view>
				<view class="input">
					<input type="text" value="" @input="titleChange" />
				</view>
			</view>
			<view class="input-group input-textarea">
				<view class="label">
					简介
				</view>
				<view class="input">
					<textarea value="" @input="briefChange" />
				</view>
			</view>
		
			<view class="uploader">
				<block v-for="(image,index) in imageList" :key="index">
					<view class="uploader-file">
						<image class="uploader-img" :src="image" :data-src="image"></image>
						<view class="remove" @tap="removeImage(index)">
							<i-icon type="trash" size="30" color="#fff" />
						</view>
					</view>
				</block>
				<view class="uploader-input-box">
					<view class="uploader-input" @tap="chooseImage">
						<i-icon type="add" size="50" color="#ccc" />
					</view>
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
				aderId: '',
				tag: '',
				tagId: '',
				title: '',
				brief: '',
				tagData: [],
				imgs: [],
				imageList: []
			};
		},
		onLoad() {
			this.aderId = service.getUsers()['id'];
			this.getTags();
		},
		methods: {
			getTags() {
				uni.showLoading({
					title: '',
					mask: false
				});
				uni.request({
					url: this.$requestUrl + 'get_tag_list',
					method: 'GET',
					success: res => {
						this.tagData = res.data.data;
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				});
			},
			chooseImage() {
				let _this = this;
				uni.chooseImage({
					count: 9,
					sizeType: ['original', 'compressed'],
					sourceType: ['album', 'camera'],
					success: function (res) {
						_this.imageList = _this.imageList.concat(res.tempFilePaths);
					}
				});
			},
			removeImage(e) {
				this.imageList.splice(e, 1);
			},
			tagChange(e) {
				this.tag = this.tagData[e.detail.value]['tag_name']
				this.tagId = this.tagData[e.detail.value]['id']
			},
			titleChange(e) {
				this.title = e.detail.value;
			},
			briefChange(e) {
				this.brief = e.detail.value;
			},
			addAd() {
				uni.request({
					url: this.$requestUrl+'add_advertise',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						publisher_id: this.aderId,
						tag_id: this.tagId,
						ad_title: this.title,
						ad_brief: this.brief,
						ad_imgs: this.imgs
					},
					success: res => {
						uni.reLaunch({
							url: '../ad/ad',
						});
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				});
			},
			uploadImg(imgPath, count) {
				let _this = this;
				uni.uploadFile({
					url: this.$requestUrl+'upload_img',
					filePath: imgPath,
					name: 'file',
					formData: {
						
					},
					success: (uploadFileRes) => {
						_this.imgs = _this.imgs.concat(uploadFileRes.data);
						count++;
						if (count < _this.imageList.length) {
							_this.uploadImg(_this.imageList[count], count);
						} else {
							_this.addAd();
						}
					}
				});
			},
			save() {
				uni.showLoading({
					title: '',
					mask: false
				});
				if (!this.tagId) {
					uni.showToast({
						title: '选择广告标签',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					return
				}
				if (!this.title) {
					uni.showToast({
						title: '输入标题',
						icon: 'none',
						mask: false,
						duration: 1500
					});
					return
				}
				let imgLength = this.imageList.length;
				if (imgLength == 0) {
					this.addAd();
				} else {
					let count = 0;
					this.uploadImg(this.imageList[count], count);
				}
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
	
	.uploader{
		display: flex;
		flex-wrap: wrap;
	}
	.uploader-file{
		width: 170upx;
		height: 170upx;
		margin-right: 8upx;
		position: relative;
	}
	.remove{
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		background: rgba(0,0,0,.2);
		text-align: center;
		line-height: 160upx;
	}
	.uploader-file image{
		width: 170upx;
		height: 170upx;
	}
	.uploader-input-box{
		width: 170upx;
		height: 170upx;
		border: 1px solid #e9eaec;
		text-align: center;
		line-height: 160upx;
	}
</style>
