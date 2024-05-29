<template>
  <view class="container">
    <!-- 顶部图片 -->
    <image class="top-image" src="../../static/login/页面 1 (3).png" mode="widthFix"></image>

    <!-- 输入框区域 -->
    <view class="input-section">
      <input type="text" placeholder="请输入手机号" class="input-box">
      <view class="verification-section">
        <input type="text" placeholder="请输入验证码" class="input-box" v-model="verificationCode" />
        <button :class="['verification-button', buttonDisabled ? 'disabled' : '']" @click="sendVerificationCode" :disabled="buttonDisabled">{{ buttonText }}</button>
      </view>
      <button class="login-button" @click="login">登录</button>
    </view>

    <!-- 底部图标和条款 -->
    <view class="footer">
      <text class="other-login-text">其他登录方式</text>
      <view class="social-icons">
        <image src="../../static/login/微信.png" class="icon" />
        <image src="../../static/login/QQ.png" class="icon" />
      </view>
      <view class="agreement">
        <input type="checkbox" :checked="agreementChecked" @change="toggleAgreement" class="checkbox" />
        <text>已同意<text class="blue-text">《隐私政策》</text>和<text class="blue-text">《服务条款》</text></text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      verificationCode: '',
      buttonDisabled: false,
      buttonText: '发送验证码',
      countdown: 30,
      agreementChecked: false
    };
  },
  methods: {
    sendVerificationCode() {
      if (this.buttonDisabled) return;

      this.buttonDisabled = true;
      this.buttonText = '已发送(30)';
      let countdownInterval = setInterval(() => {
        this.countdown--;
        this.buttonText = `已发送(${this.countdown})`;
        if (this.countdown === 0) {
          clearInterval(countdownInterval);
          this.buttonDisabled = false;
          this.buttonText = '发送验证码';
          this.countdown = 30;
        }
      }, 1000);
    },
    toggleAgreement() {
      this.agreementChecked = !this.agreementChecked;
    },
    login() {
      if (this.verificationCode === '608090') {
        uni.switchTab({
          url: '/pages/index/index' // 替换为实际的页面路径
        });
      } else {
        uni.showToast({
          title: '验证码错误',
          icon: 'none'
        });
      }
    }
  }
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.top-image {
  width: 100%;
  height: 30%;
}

.input-section {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 70%; /* 宽度占70% */
  height: 50%; /* 高度占50% */
  margin: auto; /* 居中对齐 */
  margin-top:-50px;
}

.input-box {
  width: 100%;
  height: 30px; /* 高度调小 */
  margin: 5px 0; /* 间距调小 */
  padding: 5px; /* 内边距调小 */
 fonr-size:12px;
  border-radius: 15px;
  background-color: #f0f0f0; /* 灰色背景 */
  color: black;
}

.input-box::placeholder {
  color: gray;
}

.verification-section {
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-top: 10px; /* 调整验证码输入框和按钮的垂直间距 */
}

.verification-button {
  height: 30px; /* 高度调小 */
  margin-left: 10px;
  margin-top:10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  font-size: 12px; /* 调整按钮内文字大小 */
  width:150px;
}

.verification-button.disabled {
  background-color: #ccc;
}

.login-button {
  width: 100%;
  height: 30px; /* 高度调小 */
  margin-top: 25px; /* 间距调小 */
  background-color:#007bff;
  color: #fff;
  border: none;
  border-radius: 15px;
  text-align: center;
  line-height: 30px; /* 高度调整 */
}

.footer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 20%;
}

.other-login-text {
  margin-bottom: 30px;
  color: #333;
  color:gray;
}

.social-icons {
  display: flex;
  justify-content: space-around;
  width: 50%;
  margin-bottom: 28px;
}

.icon {
  width: 30px; /* 调整图标大小 */
  height: 30px;
}

.agreement {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.checkbox {
  margin-right: 10px;
}

.blue-text {
  color: #007bff;
}
</style>
