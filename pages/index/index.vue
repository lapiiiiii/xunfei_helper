<template>
	<view class="diagnosis">
		<!-- 录制时长输入框 -->
		<view class="recordinput">
			<uni-easyinput class='input' type="number" v-model="duration" placeholder="请输入录制时长" />
		</view>
		<text>S</text>

		<!-- /录制按钮 -->
		<view class="start" @tap="startRecord()">
			<image src="../../static/录音 (选中).png" class="image"></image>
			开始录音
		</view>
		<view v-show="show">
			<!-- 进度条效果 -->
			<!-- <view>进度条</view> -->
			<!-- 回放以及上传按钮 -->
			<view class="butt" @tap="playVoice()">
				<uni-icons type="sound-filled" size="30"></uni-icons>
				回放录音
			</view>
			<view class="butt" @tap="submitrecord()">
				<uni-icons type="upload-filled" size="30"></uni-icons>
				上传录音
			</view>
		</view>
		<!-- 弹出层 -->
		<view>
			<uni-popup ref="popup" :mask-click="false" type="center" background-color="#fff">
				<view class="time">
					{{second}}/{{duration}}
				</view>
				<view class="start" @tap="endRecord()">
					<image src="../../static/录音 .png" class="image"></image>
					停止录音
				</view>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	const recorderManager = uni.getRecorderManager();
	const innerAudioContext = uni.createInnerAudioContext();
	innerAudioContext.autoplay = true;

	export default {
		data() {
			return {
				//回放和上传是否显示
				show: false,
				//录制时长
				duration: '',
				//录制音频路径
				voicePath: '',
				//计时器
				timer: '',
				//计时器秒数
				second: 0,
			}
		},
		onLoad() {
			let self = this;
			recorderManager.onStop(function(res) {
				console.log('recorder stop' + JSON.stringify(res));
				self.voicePath = res.tempFilePath;
			});
		},
		methods: {
			// 计时器方法
			getTimeIntervalplus() {
				//初始化秒数
				this.second = 0
				//首先清除所有计时器
				clearInterval(this.timer);
				//设定一个计时器,将计时器的编号赋值给this.timer
				this.timer = setInterval(() => {
					//延迟的时间设为一秒,所以每过一秒,this.second加一
					this.second += 1;
					//判断当秒数大于等于输入框设定的秒数的时候
					if (this.second >= this.duration) {
						//停止录音
						recorderManager.stop();
						//清除计时器
						clearInterval(this.timer);
						//关闭弹出层
						this.$refs.popup.close('bottom')
						console.log('录音结束')
						this.show = true
					}
				}, 1000);
			},
			startRecord() {
				//首先对输入框中输入的数字进行判断
				if (this.duration >= 3 && this.duration <= 10) {
					// 点击开始录音
					console.log('开始录音');
					this.$refs.popup.open('bottom')
					//将输入框中的数字进行加工成单位为ms的
					var tims = this.duration * 1000
					//封装的一个计时器,调用开始计时
					this.getTimeIntervalplus();
					const options = {
						//指定录音的时长
						duration: tims,
						//采样率
						sampleRate: 44100,
						//录音通道数
						numberOfChannels: 1,
						//编码码率
						encodeBitRate: 192000,
						//音频格式
						format: 'mp3',
						//指定帧大小
						frameSize: 50
					}
					console.log(options)
					recorderManager.start(options);
				} else {
					uni.showToast({
						title: '录制时长在3-10S之间',
						icon: 'none',
						duration: 2000
					});
				}
			},
			endRecord() {
				console.log('录音结束');
				recorderManager.stop(); //结束录音
				clearInterval(this.timer); //清除计时器
				//关闭弹出层
				this.$refs.popup.close('bottom')
				this.show = true
			},
			playVoice() {
				console.log('播放录音');
				console.log(this.voicePath)
				//如果有录音文件的话,将文件赋值给innerAudioContext.src进行播放
				if (this.voicePath) {
					innerAudioContext.src = this.voicePath;
					innerAudioContext.play();
				}
			},
			submitrecord() {
				console.log('上传录音')
				console.log(this.duration)

			}
		}
	}
</script>

<style lang="scss" scoped>
	.diagnosis {
		width: 86%;
		margin: auto;
	}

	.recordinput {
		display: inline-block;
		width: 80%;
		margin: 300rpx 20rpx 100rpx 50rpx;
	}

	.start {
		width: 50%;
		margin: 50rpx auto;
		font-size: 16px;
		color: #666666;
		height: 80rpx;
		border: 1px solid;
		display: flex;
		flex-direction: row;
		align-items: center;
		border-color: #b8b8b8;
		border-radius: 10rpx;
		font-family: "楷体";
		justify-content: center;

	}

	.start image {
		margin-left: 10rpx;
		margin-right: 10rpx;
		width: 35rpx;
		height: 35rpx;
	}

	.butt {
		width: 50%;
		margin: 50rpx auto;
		font-size: 16px;
		color: #666666;
		height: 80rpx;
		border: 1px solid;
		display: flex;
		flex-direction: row;
		align-items: center;
		border-color: #b8b8b8;
		border-radius: 10rpx;
		font-family: "楷体";
		justify-content: center;
	}


	.time {
		width: 50%;
		margin: 10rpx auto;
		font-size: 22px;
		height: 80rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		font-family: "楷体";
		justify-content: center;
	}
</style>
