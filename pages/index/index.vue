<template>
  <view class="container">
    <!-- 顶部蓝色区域 -->
    <view class="top-section">
      <text class="welcome-text">欢迎使用讯飞教育助手</text>
    </view>

    <!-- 白色区域 -->
    <view class="white-section">
      <!-- 图标区域 -->
      <view class="icon-row">
        <view class="icon-wrapper">
          <image src="../../static/index/AI.png" class="icon" />
          <text class="icon-text">AI学习</text>
        </view>
        <view class="icon-wrapper">
          <image src="../../static/index/建议.png" class="icon" />
          <text class="icon-text">学习建议</text>
        </view>
        <view class="icon-wrapper">
          <image src="../../static/index/指南.png" class="icon" />
          <text class="icon-text">使用指南</text>
        </view>
        <view class="icon-wrapper">
          <image src="../../static/index/视频.png" class="icon" />
          <text class="icon-text">推荐视频</text>
        </view>
      </view>
      <!-- 图片 -->
      <image src="../../static/image.png" class="bottom-image" />

      <!-- AI学习部分 -->
      <view class="section">
        <view class="title">
          <view class="blue-line"></view>
          <text class="blue-title">AI学习</text>
          <text class="gray-text">每天一个AI小知识</text>
          <text class="more">更多&gt;&gt;</text>
        </view>
        <video src="../../static/video.mp4" class="video" />
      </view>

      <!-- 推荐学习视频部分 -->
      <view class="section">
        <view class="title">
          <view class="blue-line"></view>
          <text class="blue-title">推荐学习视频</text>
          <text class="gray-text">适合你的需求设计</text>
          <text class="more">更多&gt;&gt;</text>
        </view>
        <view class="list-item">
          <image src="../../static/image1.png" class="square-image" />
          <view class="text-content">
            <text class="title">视频标题</text>
            <text class="description">视频介绍</text>
          </view>
        </view>
        <view class="list-item">
          <image src="../../static/image2.png" class="square-image" />
          <view class="text-content">
            <text class="title">视频标题</text>
            <text class="description">视频介绍</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<style>
.container {
  height: 100vh;
}

.top-section {
  background-color: #007bff;
  height: 10%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.welcome-text {
  color: #fff;
  font-size: 16px;
}

.white-section {
  background-color: #fff;
  border-top-left-radius: 20px;
  border-top-right-radius: 20px;
  padding: 20px;
}

.icon-row {
  display: flex;
  justify-content: space-around;
  margin-bottom: 10px;
}

.icon-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.icon {
  width: 60px;
  height: 60px;
}

.icon-text {
  font-size: 14px;
  color: #666;
}

.bottom-image {
  width: 100%;
  height: 10%;
  margin-top: 10px;
}

.section {
  margin-top: 20px;
}

.title {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.blue-line {
  width: 6px;
  height: 30px;
  background-color: #007bff;
  margin-right: 10px;
}

.blue-title {
  font-size: 18px;
  color: #007bff;
  font-weight: bold;
  margin-right: 10px;
}

.gray-text {
  color: #666;
}

.more {
  color: #666;
  font-size: 14px;
  margin-left: auto;
}

.video {
  width: 100%;
  height: 20vh;
}

.list-item {
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.square-image {
  width: 80px;
  height: 80px;
  margin-right: 10px;
}

.text-content {
  flex: 1;
}

.title {
  font-size: 16px;
  font-weight: bold;
}

.description {
  color: #666;
}
</style>
