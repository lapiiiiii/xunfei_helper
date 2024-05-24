<template>
  <div>
    <view class="top-section">
      <text class="welcome-text"><image src="../../static/index/讯飞星火.png" style="width:20px;height:20px;top:4px;margin-right:7px"/>背诵助手</text>
    </view>
	
    <view class="custom-container">
      <div class="container-text">
        <p>哈喽~</p>
        <p>想要检查你的背诵成果吗？</p>
        <p>先输入你要背诵的古诗文的名字吧~</p>
        <p>正确的地方会用<span class="right">绿色</span>标出来哦~</p>
      </div>
    </view>

    <div class="chat">
      <scroll-view :style="{ height: `${windowHeight}rpx` }" id="scrollview" scroll-y="true" :scroll-top="scrollTop"
        :scroll-with-animation="true" class="scroll-view">
        <!-- 聊天主体 -->


        <div id="msglistview" class="chat-body">
          <!-- 聊天记录 -->

          <div v-for="(item, index) in list" :key="index">
            <div v-if="item.me !== ''" class="item self">
              <div class="content right">{{ item.me }}</div>
            </div>


            <div v-if="item.robot !== ''" class="item Ai">
              <div class="content left" v-html="formatText(item.robot)"></div>
            </div>


          </div>
        </div>
      </scroll-view>





      <!-- 底部消息发送栏 -->
      <!-- 用来占位，防止聊天消息被发送框遮挡 -->
      <div class="chat-bottom">


        <div class="send-msg">

          <!--左侧录音的按钮-->
          <div class="content">
            <image class="record" @click="show = true"></image>
            <div class="popup-bottom" v-if="show">
              <div class="popup-bg" @click="show = false"></div>
              <div class="popup-content">
                <sound-recording :maximum="600" :duration="100" @cancel="show = false" @confirm="onUpload">
                </sound-recording>
              </div>
            </div>
          </div>

          <!--右侧的输入框-->
          <div class="uni-textarea">
            <textarea v-model="inputValue" maxlength="300" :show-confirm-bar="false" auto-height
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
const innerAudioContext2 = uni.createInnerAudioContext();
innerAudioContext2.autoplay = true;

export default {
  components: {
    navBar
  },
  data() {
    return {
      scrollTop: 0,
      list: [],
      inputValue: "", // 用户输入的值
      show: false,
      tempFilePath: "", // 存储临时文件路径
    };
  },

  computed: {
    windowHeight() {
      // 获取窗口高度，用于设置滚动视图的高度
      return this.rpxTopx(uni.getSystemInfoSync().windowHeight);
    }
  },
  methods: {
    rpxTopx(px) {
      // 将rpx单位转换为px单位
      let deviceWidth = wx.getSystemInfoSync().windowWidth;
      let rpx = (750 / deviceWidth) * Number(px);
      return Math.floor(rpx);
    },
    handleEnter() {
      const inputValue = this.inputValue;
      if (!inputValue) {
        // 如果用户输入为空，显示提示消息
        uni.showToast({
          title: "请输入古诗文名字",
          icon: "none"
        });
        return;
      }

      // 显示用户输入的古诗名
      this.list.push({
        me: inputValue,
        robot: `您要背诵的古诗为《${inputValue}》。`
      });

      // 如果用户输入的内容不为空，则查询古诗文
      if (inputValue.trim() !== "") {
        this.queryPoetry(inputValue).then(res => {
          const content = res.data;
          console.log(content);
          this.scrollTop = 9999999; // 滚动到底部
        });
      }

      // 清空输入框
      this.inputValue = "";

    },

    queryPoetry(poetryName) {
      // 查询古诗文
      return new Promise((resolve, reject) => {
        uni.request({
          url: baseUrl + "/poem",
          method: "GET",
          data: {
            title: poetryName.trim()
          },
          success: res => {
            console.log("请求古诗成功：", res.data);
            resolve(res.data);
          },
          fail: err => {
            console.log("请求古诗失败：", err);
            reject(err);
          }
        });
      });
    },

    onUpload(tempFilePath) {
      uni.uploadFile({
        url: baseUrl + "/audio",
        filePath: tempFilePath,
        name: 'file',
        success: (uploadRes) => {
          // 上传成功
          const data = uploadRes.data;
          // 在这里处理服务器返回的数据
          console.log('上传成功，服务器的数据是：', data);
        
          // 使用正则表达式匹配百分数之前的数字
          const matchPercentage = data.match(/(\d+(\.\d+)?)%/);
          // 如果匹配成功，则提取出百分数之前的数字
          if (matchPercentage) {
            const percentage = matchPercentage[1];
            console.log('提取到的数字是：', percentage);
            // 获取除去百分号后的数据
            const data1 = data.replace(/(\d+(\.\d+)?)%/, '');
            // 构建需要添加到列表的字符串
            const message = `你背诵的正确率是${percentage}%<br>
			背诵原文对照：<br>
			${data1}`;
            // 将构建的字符串添加到列表中
            this.list.push({
              me: "背诵完成",
              robot: message
            });
          } else {
            // 如果没有匹配到百分数，则直接显示数据
            this.list.push({
              me: "背诵完成",
              robot: data
            });
          }
        
          this.show = false;
        },


        fail: (err) => {
          // 上传失败
          console.error('上传失败：', err);
        }
      });
    },
    formatText(text) {
      return text.replace(/\(([^)]+)\)/g, '<span style="color:green">$1</span>');
    }

  }

};
</script>



<style lang="scss" scoped>
$chatContentbgc: #C2DCFF;
$sendBtnbgc: #4F7DF5;

.top-section {
  background-color: #6993FF;
  height: 10%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0px 10px 10px rgba(0, 0, 0, 0.2); /* 添加阴影效果 */
  border-radius: 12px; /* 添加圆角边框 */
   display: flex; /* 将文本设置为 Flex 容器 */
    align-items: center; /* 垂直居中子元素 */
}

.welcome-text {
  color: #fff;
  font-size: 16px;

  padding-top: 30px;
  padding-bottom: 10px;
  text-align:center ;
}

.hello {
  width: 20px;
}

.custom-container {
  margin-top: 20px;
  padding: 10px;
  height: 90px;
  border: 1px solid  #6993FF;
  /* 蓝色边框 */
  border-radius: 10px;
  /* 圆角 */
  box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.1), -5px -5px 10px rgba(0, 0, 0, 0.1);
  /* 阴影效果 */
  background: linear-gradient(to bottom,  #6993FF, #ffffff);
  /* 渐变背景色 */
  margin-left: 25px;
  width: 80%;
}

.container-text {
  color: #000000;
  /* 文字颜色 */
  font-size: 16px;
  /* 文字大小 */
  text-align: left;
  font-weight: bold;
}

.right {
  color: green;
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
    height: 100%;
   background-color: #fff;
    .send-msg {
      display: flex;
      align-items: flex-end;
      padding-left: 80px;
      width: 100%;
      min-height: 130rpx;
      position: fixed;
      bottom: 0;
      background: #EDEDED;
	  border-top-left-radius: 20px;
	  border-top-right-radius: 20px;
    }

    .uni-textarea {
      padding-bottom: 30rpx;

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
      margin-bottom: 35rpx;
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
      background: rgba(0, 0, 0, 0.5);
    }

    .popup-content {
      height: 40%;
      margin-top: auto;
      background-color: #fff;
      position: relative;
      z-index: 11;
    }
  }

  .record {
    width: 80rpx;
    height: 80rpx;
    background-image: url('./record.png');
    display: flex;
    justify-content: center;
    background-size: cover;
    /* 设置背景图片大小 */
    margin-bottom: 20px;
    margin-left: -60px;
    border-radius: 100%;

  }

}
</style>
