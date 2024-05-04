<template>
	<!-- 头部导航内容区域 -->
	<view style="margin-bottom: 20rpx;" class="top-content">
		<view class="title-top">
			<view class="boxAndTop" :style="{height:statusBarHeight+'px'}"></view>
			<view class="back-btn" ></view>
			 <text class="title">{{ titleName }}</text>
		</view>
 
	</view>
</template>
 
<script>
	export default {
		props: {
			titleName: {
				type: String,
				default: ''
			},
		},
		data() {
			return {
				// 状态栏高度
				statusBarHeight: 0,
				// 导航栏高度
				titleBarHeight: 82 + 11,
				menuFlag: false
			}
		},
		created() {
			//获取状态栏的高度
				let systemInfo = uni.getSystemInfoSync()
				this.statusBarHeight = systemInfo.statusBarHeight
				console.log(this.statusBarHeight,'状态栏的高度');
			// #ifndef H5 || APP-PLUS || MP-ALIPAY
				let menuButtonInfo = uni.getMenuButtonBoundingClientRect()
				this.titleBarHeight = (menuButtonInfo.top - this.statusBarHeight) * 2 + menuButtonInfo.height
		
			// #endif
 
		},
		methods: {
		},
	}
</script>
 
<style lang="scss">
	.top-content {
		// position: fixed;
		height: 140rpx;
 
		.title-top {
			position: fixed;
			top: 0px;
			width: 100%;
			z-index: 999;
			height: 140rpx;
			background-color: #fff;
		}
	}
 .title{
	 font-size: 36rpx;
	 color: #333;
	 padding-left:140px;
	 margin-top:60px;
 }
 
 .back-btn{
	 background-image: url("@/static/backbtn.png");
 }
</style>