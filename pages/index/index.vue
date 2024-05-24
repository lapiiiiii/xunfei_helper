<template>
  <view class="container">
    <!-- 顶部蓝色区域 -->
    <view class="top-section">
      <text class="welcome-text"><image src="../../static/index/讯飞星火.png" style="width:20px;height:20px;top:4px;"/>欢迎使用讯飞AI教育助手</text>
    </view>

   <view class="post" >
     <image src="../../static/index/post.png" style="width:320px;height:150px;margin-top:20px;margin-left:20px;border-radius: 10px;"/>
   </view>
   
    <!-- 白色区域 -->
    <view class="white-section">
      <!-- 图标区域 -->
      <view class="icon-row">
        <view class="icon-wrapper">
          <image src="../../static/index/代码.png" class="icon" />
          <text class="icon-text">AI学习</text>
        </view>
        <view class="icon-wrapper">
          <image src="../../static/index/会议.png" class="icon" />
          <text class="icon-text">学习建议</text>
        </view>
        <view class="icon-wrapper">
          <image src="../../static/index/书本.png" class="icon" />
          <text class="icon-text">使用指南</text>
        </view>
        <view class="icon-wrapper">
          <image src="../../static/index/手机.png" class="icon" />
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
		<view class="img-container" style="display: flex; overflow-x: auto; width: 100%;">
		    <view style="display: flex; min-width: 400px;">  <!-- 这里的min-width应设置为图片总宽度之和 -->
		        <image src="../../static/index/video1.png" style="height: 150px; width:200px;" />
		        <image src="../../static/index/video2.png" style="height: 150px; width:200px;" />
		    </view>
		</view>

      </view>
	 

		 
        <!-- 推荐学习视频部分 -->
        <view class="section">
          <view class="title">
            <view class="blue-line"></view>
            <text class="blue-title">推荐学习视频</text>
            <text class="gray-text">适合你的需求设计</text>
            <text class="more">更多&gt;&gt;</text>
          </view>
          <view class="scroll-view">
            <view class="list-item">
              <image src="../../static/index/video4.png" class="square-image" />
              <view class="text-content">
                <text class="title">牛顿第二定律的两类问题</text>
                <text class="description">看概念，多做题，练思维。</text>
              </view>
            </view>
			
            <view class="list-item">
              <image src="../../static/index/video5.png" class="square-image" />
              <view class="text-content">
                <text class="title">最全常用倒数公式！</text>
                <text class="description">考前必背，提高十分。</text>
              </view>
            </view>
           
		   <view class="list-item">
		     <image src="../../static/index/video3.png" class="square-image" />
		     <view class="text-content">
		       <text class="title">离子推断题的判断技巧</text>
		       <text class="description">深入浅出地教你如何判断离子。</text>
		     </view>
		   </view>
		   
          </view>
        

		
		
      </view>
    </view>
  </view>
</template>



<script>

</script>



<style>
.container {
  height: 100vh;
}

.top-section {
  background-color: #6993FF;
  height: 10%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0px 10px 10px rgba(0, 0, 0, 0.2); /* 添加阴影效果 */
 border-bottom-left-radius: 10px;
 border-bottom-right-radius: 10px;
   display: flex; /* 将文本设置为 Flex 容器 */
    align-items: center; /* 垂直居中子元素 */
}

.welcome-text {
  color: #fff;
  font-size: 16px;
  padding-right: 140px;
  padding-top: 30px;
}

.white-section {
  background-color: #fff;
  border-top-left-radius: 50px;
  border-top-right-radius: 20px;
  padding: 20px;

}

.icon-row {
  display: flex;
  justify-content: space-around;
  margin-bottom: 5px;
 
}

.icon-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.icon {
  width: 40px;
  height: 40px;
  margin-bottom:15px;
  margin-top:15px;
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
  font-size:13px;
   margin-left:8px;
}

.more {
  color: #666;
  font-size: 15px;
 
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
  border-bottom:1px solid #c4c4c4 ;
}


.square-image {
  width: 120px; /* 调整宽度 */
  height: 100px; /* 调整高度 */
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
  font-size:11px;
}
</style>
