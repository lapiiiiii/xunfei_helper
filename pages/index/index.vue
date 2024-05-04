
<template>
  <div>
    <navBar titleName="背诵助手" />	
	
	<view class="custom-container">
	     <div class="container-text">
	       <p>哈喽~<image class="hello" src="https://i.postimg.cc/KYzyKS0D/2.png" mode="widthFix"></image></p>
	       <p>想要检查你的背诵成果吗？</p>
	       <p>先输入你要背诵的古诗文的名字吧~</p>
	       <p>正确的地方会用<span class="wrong">红色</span>标出来哦~</p>
	     </div>
	   </view>
	
    <div class="chat">
      <scroll-view :style="{height: `${windowHeight}rpx`}"
                   id="scrollview"
                   scroll-y="true"
                   :scroll-top="scrollTop"
                   :scroll-with-animation="true"
                   class="scroll-view">
        <!-- 聊天主体 -->
        <div id="msglistview" class="chat-body">
          <!-- 聊天记录 -->
          <div v-for="(item,index) in list" :key="index">
            <!-- 自己发的消息 -->
            <div class="item self" v-if="item.me !== ''">
              <!-- 文字内容 -->
              <div class="content right">{{ item.me }}</div>
            </div>
            <!-- 机器人发的消息 -->
            <div class="item Ai" v-if="item.robot !== ''">
              <!-- 文字内容 -->
              <div class="content left" v-html="item.robot"></div>
            </div>
          </div>
        </div>
      </scroll-view>

      
      <!-- 底部消息发送栏 -->
      <!-- 用来占位，防止聊天消息被发送框遮挡 -->
      <div class="chat-bottom">
	  
		  
        <div class="send-msg">
			
			<div class="content">
			  <image class="record" @click="show = true"></image>
			  <div class="popup-bottom" v-if="show">
			    <div class="popup-bg" @click="show = false"></div>
			    <div class="popup-content">
			      <sound-recording
			        :maximum="36000"
			        :duration="100"
			        @cancel="show = false"
			        @confirm="onUpload">
			      </sound-recording>
			    </div>
			  </div>
			</div>
			
          <div class="uni-textarea">
            <textarea v-model="inputValue"
                      maxlength="300"
                      :show-confirm-bar="false"
                      auto-height
                      placeholder="请输入古诗文的名字"></textarea>
          </div>
          <button @click="handleEnter" class="send-btn">发送</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import navBar from '@/components/navBar/navBar.vue'
import { baseUrl } from '@/config.js';

export default {
  components: {
    navBar
  },
  data() {
    return {
      scrollTop: 0,
      list: [],
      inputValue: "",
      show: false,
      tempFilePath: "" // 添加 tempFilePath 变量
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
      
      // 立即显示输入的诗名
      this.list.push({ me: inputValue, robot: "" });

      // 在录音结束后上传录音并处理
      this.uploadAudio(this.tempFilePath)
        .then(() => {
          // 无论上传成功或失败，都关闭弹窗
          this.show = false;
          return this.queryPoetry(inputValue);
        })
        .then(poetryData => {
          // 将诗文数据添加到列表中
          this.list.push({ me: "", robot: poetryData.text });
        })
        .catch(err => {
          console.log("请求失败：", err);
        });
        
      this.inputValue = ""; // 清空输入框
    },
    queryPoetry(poetryName) {
      return new Promise((resolve, reject) => {
        uni.request({
          url: baseUrl + "/poem",
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
    // 上传录音文件到服务器
    uploadAudio(tempFilePath) {
      return new Promise((resolve, reject) => {
        uni.uploadFile({
          url: baseUrl+'/audiotest',
          filePath: tempFilePath,
          name: 'file',
          success: (uploadRes) => {
            // 上传成功
            const data = uploadRes.data;
            // 在这里处理服务器返回的数据
            console.log('上传成功，服务器数据：', data);
            resolve();
          },
          fail: (err) => {
            // 上传失败
            console.error('上传失败：', err);
            reject(err);
          }
        });
      });
    },
    onUpload(tempFilePath) {
      // 在录音确认时更新 tempFilePath
      this.tempFilePath = tempFilePath;
      // 显示确认录音后的相关操作，例如发送按钮等
	   this.show = false;
    }
  }
};
</script>


<style lang="scss" scoped>
$chatContentbgc: #C2DCFF;
$sendBtnbgc: #4F7DF5;
.hello{
  width:20px;
} 

.custom-container {
  margin-top: 20px;
  padding: 10px;
  height:90px;
  border: 1px solid #007bff; /* 蓝色边框 */
  border-radius: 10px; /* 圆角 */
  box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.1), -5px -5px 10px rgba(0, 0, 0, 0.1); /* 阴影效果 */
  background: linear-gradient(to bottom, #007bff, #ffffff); /* 渐变背景色 */
  margin-left:25px;
  width:80%;
}
.container-text {
  color: #000000; /* 文字颜色 */
  font-size: 16px; /* 文字大小 */
  text-align:left; 
  font-weight: bold;
} 
.wrong{
  color:red;
  font-size: 20px;
}

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
      padding-left:80px;
      width: 100%;
      min-height: 177rpx;
      position: fixed;
      bottom: 0;
      background: #EDEDED;
    }

    .uni-textarea {
      padding-bottom: 70rpx;
	  
      textarea {
		
        width: 360rpx;
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
    padding-: 30rpx;
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
  
  .record{
	width: 80rpx;
    height: 80rpx;
    background-image: url('./record.png');
    display: flex;
    justify-content: center;
	background-size: cover; /* 设置背景图片大小 */
	 margin-bottom: 35px;
	 margin-left:-60px;
	 border-radius: 100%;
	 
  }
  
}
</style>
