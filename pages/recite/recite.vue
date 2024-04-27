

<template>
  <view class="container">
    <view class="nav-bar">
      <!-- 左侧返回按钮 -->
      <view class="back-btn" >
          <image  class="back-image" mode="widthFix"></image>
      </view>
      <!-- 中间标题 -->
      <view class="title">{{ pageTitle }}</view>
    </view>


   <view class="custom-container">
     <div class="container-text">
       <p>哈喽~<image class="hello"  mode="widthFix"></image></p>
       <p>想要检查你的背诵成果吗？</p>
       <p>先输入你要背诵的古诗文的名字吧~</p>
       <p>正确的地方会用<span class="wrong">红色</span>标出来哦~</p>
     </div>
   </view>

    <view class="content">
       <p>{{result}}</p>
    </view>


    <!-- 底部输入框和按钮 -->
    <view class="bottom-bar">
      <input class="input-box" type="text" placeholder="请输入古诗文名字" v-model="inputValue" value="inputValue" value="inputValue"  @confirm="handleEnter" />
      <button class="enter-button" @tap="handleEnter">
      <image class="enter-icon"  mode="aspectFit"></image>
    </button>
    </view>

   
   
   
  </view>
</template>

<script>

export default {
  data() {
    return {
      inputValue: '', // 添加inputValue属性
      result: '' // 添加result属性
    };
  },
  props: {
    // 页面标题
    pageTitle: {
      type: String,
      default: '背诵助手'
    }
  },
 methods: {
   handleEnter() {
     // Check if inputValue is not empty
     if (!this.inputValue.trim()) {
       // Handle empty input case here if needed
       return;
     }
 
     uni.request({
       url: 'http://localhost:8080/poem', // Concatenate baseURL with endpoint URL
       method: 'GET',
       data: {
         title: this.inputValue.trim() // Trim input value to remove leading/trailing spaces
       },
       success: (res) => {
         console.log('请求成功：', res.data);
         this.result = res.data; // Assign result to the component's result property
       },
       fail: (err) => {
         console.log('请求失败：', err);
         // Handle failed request here if needed
       }
     });
   }
 }

};
</script>

<style scoped>
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
  
.back-image{
 width: 20px;
height:20px;
 margin-left: 5px;
 padding-top: 18px;
 padding-right: 50px;
}
  
.container {
display: flex;
 flex-direction: column;
 height: 130vh; /* 使用视窗的高度 */
 padding: 20px;
 margin-top: 40px;
}

.nav-bar {
 width: 100%;
 height: 54px;
 background-color: #ffffff;
 display: flex;
 align-items: center;
 padding: 0 15px;
 position:fixed;
 left: 0;
 right: 0;
 top:0;
}

.title {
 font-size: 18px;
 font-weight: bold;
 color: #333333;
 margin-left: 28px;
 margin-top: 15px;
}

.bottom-bar {
position: fixed;
 bottom: 0;
 left: 0;
 width: 100%;
 display: flex;
background-color: #ededed;
  }

.input-box {
 flex: 1;
 height: 30px;
 width: 300px;
 border: 1px solid #ccc;
 border-radius: 4px;
 font-size: 14px;
 background-color: #fff;
 margin: 10px; /* 调整输入框距离底部边框的距离 */
 margin-left: 15px;
}

.enter-button {
 width: 80px;
 height: 36px;
 background-color: transparent; /* Enter按钮的背景色 */
 border: none;
 border-radius: 50%;

}
.enter-icon{
 width:25px;
 height: 25px;
 margin-top: 10px;
}
</style>
