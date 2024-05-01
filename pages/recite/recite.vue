<template>
  <view class="chat">
    <scroll-view :style="{height: `${windowHeight}rpx`}"
                 id="scrollview"
                 scroll-y="true"
                 :scroll-top="scrollTop"
                 :scroll-with-animation="true"
                 class="scroll-view">
      <!-- 聊天主体 -->
      <view id="msglistview" class="chat-body">
        <!-- 聊天记录 -->
        <view v-for="(item,index) in list" :key="index"  >
          <!-- 自己发的消息 -->
          <view class="item self" v-if="item.me!=''">
            <!-- 文字内容 -->
            <view class="content right">{{ item.me }}</view>
          </view>
          <!-- 机器人发的消息 -->
          <view class="item Ai" v-if="item.robot!=''" >
            <!-- 文字内容 -->
            <view class="content left">{{ item.robot }}</view>
          </view>
        </view>
      </view>
    </scroll-view>
	
	
	<view class="content">
	<button class="record" @click="show = true">语音录制</button>
	<view class="popup-bottom" v-if="show">
	  <view class="popup-bg" @click="show = false"></view>
	  <view class="popup-content">
	    <sound-recording
	      :maximum="36000"
	      :duration="100"
	      @cancel="show = false"
	      @confirm="onUpload">
	    </sound-recording>
	  </view>
	</view>
	</view>
	
	
	
	
    <!-- 底部消息发送栏 -->
    <!-- 用来占位，防止聊天消息被发送框遮挡 -->
    <view class="chat-bottom">
      <view class="send-msg">
        <view class="uni-textarea">
          <textarea v-model="inputValue"
                    maxlength="300"
                    :show-confirm-bar="false"
                    auto-height
                    placeholder="请输入古诗文的名字"></textarea>
        </view>
        <button @click="handleEnter" class="send-btn">发送</button>
      </view>
    </view>
  </view>
</template>
<script>
	
import { baseUrl } from '@/config.js';

export default {
  data() {
    return {
      scrollTop: 0,
      list: [
        {
          me: "",
          id: 0,
          robot: ""
        }
      ],
      inputValue: "",
      show: false,
      voicePath: ''
    };
  },
  computed: {
    windowHeight() {
      return this.rpxTopx(uni.getSystemInfoSync().windowHeight);
    }
  },
  methods: {
    rpxTopx(px) {
      let deviceWidth = wx.getSystemInfoSync().windowWidth;
      let rpx = (750 / deviceWidth) * Number(px);
      return Math.floor(rpx);
    },
    handleEnter() {
      const inputValue = this.inputValue;
      if (!inputValue) {
        uni.showToast({
          title: "请输入古诗文名字",
          icon: "none"
        });
        return;
      }
      this.queryPoetry(inputValue)
        .then(robot => {
          this.list.push({ me: inputValue, robot: robot });
        })
        .catch(err => {
          console.log("请求失败：", err);
        });
      this.inputValue = ""; // 清空输入框
    },
    queryPoetry(poetryName) {
      return new Promise((resolve, reject) => {
        uni.request({
          url: baseUrl + ":8088/poem",
          method: "GET",
          data: {
            title: poetryName.trim()
          },
          success: res => {
            console.log("请求成功：", res.data);
            resolve(res.data);
          },
          fail: err => {
            console.log("请求失败：", err);
            reject(err);
          }
        });
      });
    },
    onEndRecoder () {
      recorderManager.stop();
      recorderManager.onStop(({ tempFilePath }) => {
        this.voicePath = tempFilePath;
      });
    },
    // 在 confirm 方法中触发上传录音文件的操作
    confirm() {
      if (!innerAudioContext.paused) {
        innerAudioContext.stop();
      }
      // 触发上传录音文件的操作
      this.onUpload();
    },
    // 上传录音文件到服务器
    onUpload() {
      const url = baseUrl+':8088/audio';
      const filePath = this.voicePath;
      uni.uploadFile({
        url: url,
        filePath: filePath,
        name: 'file',
        success: (uploadRes) => {
          // 上传成功
          const data = uploadRes.data;
          // 在这里处理服务器返回的数据
          console.log('上传成功，服务器返回的数据：', data);
        },
        fail: (err) => {
          // 上传失败
          console.error('上传失败：', err);
        }
      });
    }
  }
};
</script>





<style lang="scss" scoped>
$chatContentbgc: #C2DCFF;
$sendBtnbgc: #4F7DF5;

.chat {
  .scroll-view {
    ::-webkit-scrollbar {
      display: none;
      width: 0 !important;
      height: 0 !important;
      -webkit-appearance: none;
      background: transparent;
      color: transparent;
    }
    background-color: #F6F6F6;

    .chat-body {
      display: flex;
      flex-direction: column;
      padding-top: 23rpx;

      .self {
        justify-content: flex-end;
      }
      .item {
        display: flex;
        padding: 23rpx 30rpx;

        .right {
          background-color: $chatContentbgc;
        }
        .left {
          background-color: #FFFFFF;
        }
        .right::after {
          position: absolute;
          display: inline-block;
          content: '';
          width: 0;
          height: 0;
          left: 100%;
          top: 10px;
          border: 12rpx solid transparent;
          border-left: 12rpx solid $chatContentbgc;
        }
        .left::after {
          position: absolute;
          display: inline-block;
          content: '';
          width: 0;
          height: 0;
          top: 10px;
          right: 100%;
          border: 12rpx solid transparent;
          border-right: 12rpx solid #FFFFFF;
        }
        .content {
          position: relative;
          max-width: 486rpx;
          border-radius: 8rpx;
          word-wrap: break-word;
          padding: 24rpx 24rpx;
          margin: 0 24rpx;
          border-radius: 5px;
          font-size: 32rpx;
          font-family: PingFang SC;
          font-weight: 500;
          color: #333333;
          line-height: 42rpx;
        }
      }
    }
  }

  .chat-bottom {
    width: 100%;
    height: 177rpx;
    background: #F4F5F7;

    .send-msg {
      display: flex;
      align-items: flex-end;
      padding: 16rpx 30rpx;
      width: 100%;
      min-height: 177rpx;
      position: fixed;
      bottom: 0;
      background: #EDEDED;
    }

    .uni-textarea {
      padding-bottom: 70rpx;

      textarea {
        width: 537rpx;
        min-height: 75rpx;
        max-height: 500rpx;
        background: #FFFFFF;
        border-radius: 8rpx;
        font-size: 32rpx;
        font-family: PingFang SC;
        color: #333333;
        line-height: 43rpx;
        padding: 5rpx 8rpx;
      }
    }

    .send-btn {
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 70rpx;
      margin-left: 25rpx;
      width: 128rpx;
      height: 75rpx;
      background: $sendBtnbgc;
      border-radius: 8rpx;
      font-size: 28rpx;
      font-family: PingFang SC;
      font-weight: 500;
      color: #FFFFFF;
      line-height: 28rpx;
    }
  }
  
  .content {
    padding: 30rpx;
  }
  .popup-bottom {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 10;
    display: flex;
    flex-direction: column;
    .popup-bg {
      position: fixed;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.5);
    }
    .popup-content {
      height: 40%;
      margin-top: auto;
      background-color: #fff;
      position: relative;
      z-index: 11;
    }
  }
  
  
  
}
</style>
