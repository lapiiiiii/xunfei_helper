<template>
  <view class="container">
    <!-- 头部区域 -->
    <view class="header">
      <text class="personal-center">个人中心</text>
      <view class="profile-area">
        <view class="avatar-container">
          <image class="avatar" src="https://img0.baidu.com/it/u=1264336001,1107747499&fm=253&fmt=auto&app=120&f=JPEG?w=608&h=608"></image> <!-- 头像图片 -->
          <view class="text-container">
            <div class="name">加油！</div>
            <div class="homepage">编辑资料</div>
          </view>
        </view>
      </view>
      <!-- 签到按钮浮动到右下角 -->
      <button class="sign-in-btn" >
		  <image src="../../static/my/签到.png" style="width:20px;height:20px;top:3px;right:3px;"/>
        <text>签到</text>
      </button>
    </view>

    <!-- 主体内容区 -->
    <view class="main">
      <view class="section">
        <view class="title-line">
          <view class="blue-line"></view>
          <text class="title">我的学习</text>
        </view>
        <view class="icon-container">
          <view class="icon-text">
            <image class="icon" src="../../static/my/数据.png"></image>
            <div>我的成绩</div>
          </view>
          <view class="icon-text">
            <image class="icon" src="../../static/my/时间.png"></image>
            <div>浏览记录</div>
          </view>
          <view class="icon-text">
            <image class="icon" src="../../static/my/视频.png"></image>
            <div>我的课程</div>
          </view>
        </view>
      </view>

      <view class="section">
        <view class="title-line">
          <view class="blue-line"></view>
          <text class="title">其他功能</text>
        </view>
        <view class="list-container">
          <view class="list-item">
            <text class="item-text">联系客服</text>
            <text class="arrow">>></text>
          </view>
          <view class="list-item">
            <text class="item-text">反馈</text>
            <text class="arrow">>></text>
          </view>
		  <view class="list-item">
		    <text class="item-text">版本更新</text>
		    <text class="arrow">>></text>
		  </view>
		  
		  <view class="list-item">
		    <text class="item-text">设置</text>
		    <text class="arrow">>></text>
		  </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {};
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.header {
  background-color: #517DFD;
  color: white;
  padding: 10px;
  position: relative;
  height: 30%;
}

.personal-center {
  position: absolute;
  left: 20px;
  top: 20px;
  font-size:20px;
}
.name{
	font-size:20px;
	margin-bottom:5px;
}

.section{
	margin-bottom:20px;
	margin-top:20px;
}

.homepage{
	border:1px solid white ;
	border-radius: 20px;
	padding:3px;
	font-size:12px;
}

.profile-area {
  position: absolute;
  left: 10px;
  bottom: 10px;
}

.avatar-container {
  display: flex;
  align-items: center;
}

.avatar {
  width: 60px;
  height: 60px;
  border-radius: 50%;
}

.text-container {
  margin-left: 10px;
}

.sign-in-btn {
  position: absolute;
  right: 10px;
  bottom: 10px;
  background-color: white;
  color: black;
  border: none;
  padding: 3px;
  border-radius: 10px;
  font-size:15px;
  height:40px;
  width:70px;
  box-shadow: 0 5px 5px #808080;
}

.main {
  background-color:#F7FBFE;
  height: 70%;
  padding: 20px;
}

.title-line {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.title{
	color:#436CE7;
	font-weight:bold;
}
.blue-line {
  height: 20px;
  width: 5px;
  background-color: #436CE7;
  margin-right: 10px;
}

.icon-container {
  display: flex;
  justify-content: space-between;
  background-color: white;
  border-radius: 10px;
  padding: 10px;
}

.icon-text {
  text-align: center;
  font-size:15px;
  color:gray;
}

.icon{
	width:28px;
	height:28px;
}

.list-container {
  background-color: white;
  border-radius: 10px;
  padding: 15px;
 
}

.list-item {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  border-bottom: 1px solid #b1b1b1;
}
.list-item:last-child {
  border-bottom: none;
}

.arrow {
  color: #808080;
}
</style>
