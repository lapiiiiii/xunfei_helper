<template>
  <view class="container">
    <!-- 顶部图片 -->
    <image class="top-image" src="https://th.bing.com/th/id/R.0d4130b66c98887b8c82039a05e0e222?rik=qd4cYYVYCFzBPA&pid=ImgRaw&r=0" mode="widthFix"></image>

    <!-- 输入框区域 -->
    <view class="input-section">
      <input type="text" placeholder="请输入手机号" class="input-box" value="13013012312" disabled />
      <view class="verification-section">
        <input type="text" placeholder="请输入验证码" class="input-box" v-model="verificationCode" />
        <button :class="['verification-button', buttonDisabled ? 'disabled' : '']" @click="sendVerificationCode" :disabled="buttonDisabled">{{ buttonText }}</button>
      </view>
    </view>

    <!-- 底部图标和条款 -->
    <view class="footer">
      <view class="social-icons">
        <image src="../../static/login/微信.png" class="icon" />
        <image src="../../static/login/QQ.png" class="icon" />
      </view>
      <view class="agreement">
        <input type="checkbox" :checked="agreementChecked" @change="toggleAgreement" class="checkbox" />
        <text>已同意隐私政策和服务条款</text>
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
    }
  },
  watch: {
    verificationCode(newVal) {
      if (newVal === '608090') {
        uni.navigateTo({
          url: '/pages/index/index' // 替换为实际的页面路径
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
  height: 50%;
}

.input-box {
  width: 80%;
  height: 40px;
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.verification-section {
  display: flex;
  justify-content: space-between;
  width: 80%;
}

.verification-button {
  height: 40px;
  margin-left: 10px;
  padding: 0 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
}

.verification-button.disabled {
  background-color: #ccc;
}

.footer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 20%;
}

.social-icons {
  display: flex;
  justify-content: space-around;
  width: 50%;
  margin-bottom: 10px;
}

.icon {
  width: 40px;
  height: 40px;
}

.agreement {
  display: flex;
  align-items: center;
}

.checkbox {
  margin-right: 10px;
}
</style>
