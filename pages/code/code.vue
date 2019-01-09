<template>
	<view class="wrap">
		<view class="top">
			<view class="qrcode">
				<image :src="qrcodeImg"></image>
			</view>
			<view class="brief">
				扫一扫上面的二维码图案，查看我发布信息
			</view>
		</view>

		<view class="mask" v-if="maskShow" @tap="close">
			<image :src="canvasImg"></image>
			<view class="btn">
				<button type="primary" @tap="save">保存</button>
			</view>
		</view>

		<canvas class="canvas-element" canvas-id="canvas"></canvas>

		<view class="btn">
			<button type="primary" @tap="create">生成名片</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				maskShow: false,
				aderName: '',
				tel: '',
				location: '',
				service: '',
				qrcodeImg: '',
				canvasImg: '',
				canvasW: 1087,
				canvasH: 661,
			};
		},
		onLoad(e) {
			let _this = this;
			
			let info = JSON.parse(e.detailData);
			
			uni.getImageInfo({
				src: info.qrcode,
				success(image) {
					_this.qrcodeImg = image.path
				}
			})
			
			this.aderName = info.ader_name
			this.tel = info.tel
			this.location = info.location
			this.service = info.service
		},
		methods: {
			create() {
				uni.showLoading({
					title: '',
					mask: false
				});
				
				let _this = this;
				let width = this.canvasW;
				let height = this.canvasH;

				let ctx = uni.createCanvasContext('canvas')

				let x = 120;
				let y = 200;

				ctx.drawImage('../../static/image/card.jpg', 0, 0, width, height)

				ctx.rotate(45 * Math.PI / 180)
				ctx.drawImage(_this.qrcodeImg, 680, -380, 180, 180)

				ctx.rotate(-45 * Math.PI / 180)

				// 姓名
				ctx.setFillStyle('#424D63')
				ctx.setFontSize(80)
				ctx.fillText(_this.aderName, x, y)

				let oy = 180;
				// 电话
				ctx.setFontSize(35)
				ctx.fillText(_this.tel, x + 50, y + oy)
				// 地址
				ctx.fillText(_this.location, x + 50, y + oy + 70)
				// 描述
				let limit = 14,
					serviceLen = _this.service.length;
				for (let i = 0; i < serviceLen / limit; i++) {
					ctx.fillText(_this.service.substr(limit * i, limit), x + 50, y + oy + 145 + 55 * i)
				}

				ctx.draw(true, () => {
					uni.canvasToTempFilePath({
						canvasId: 'canvas',
						fileType: 'jpg',
						success: function(res) {
							uni.hideLoading()
							_this.canvasImg = res.tempFilePath
							_this.maskShow = true;
						},
						fail: function(res) {
							console.log(res);
						}
					})
				})
			},
			save() {
				let _this = this;
				uni.saveImageToPhotosAlbum({
					filePath: _this.canvasImg,
					quality: 1,
					success: function() {
						_this.maskShow = false;
						uni.showToast({
							title: '保存成功',
							mask: false,
							duration: 1500
						});
					}
				});
			},
			close() {
				this.maskShow = false;
			}
		}
	}
</script>

<style>
	page {
		height: 100%;
	}

	.wrap {
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.qrcode {
		text-align: center;
		margin: 20px;
	}

	.qrcode image {
		width: 480upx;
		height: 480upx;
	}

	.brief {
		font-size: 36upx;
		color: #8F8F8F;
		text-align: center;
	}

	.btn {
		margin-bottom: 30px;
	}

	.btn button {
		width: 460upx;
		background-color: #4d9dfe;
		border-radius: 50px;
		box-shadow: 0 2px 5px #73aff7;
	}

	.mask {
		position: absolute;
		z-index: 999;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background: rgba(0, 0, 0, .8);
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.mask .btn {
		position: absolute;
		bottom: 0;
	}

	.mask image {
		width: 700upx;
		height: 426upx;
		margin-bottom: 100px;
	}

	.canvas-element {
		position: fixed;
		right: 999px;
		width: 1087px;
		height: 661px;
	}
</style>
