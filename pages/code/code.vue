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

        <view class="btn">
            <button type="primary" @tap="save">生成名片</button>
        </view>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                qrcodeImg: '',
            };
        },
        onLoad(e) {
            let info = JSON.parse(e.detailData);
            console.log(info);
            this.qrcodeImg = info.qrcode
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
</style>
