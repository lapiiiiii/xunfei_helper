<template>
  <view class="background-image"></view>
</template>


<script>
export default {
  mounted() {
    setTimeout(() => {
     
      uni.navigateTo({
        url: '/pages/login/login'
      });
    }, 2000); // 2000毫秒后执行
  }
}
</script>


<style>
.background-image {
  background-image: url('../../static/start/页面 1 (4).png'); /* 设置背景图片 */
  background-size: cover; /* 覆盖整个容器 */
  background-position: center; /* 图片居中显示 */
  height: 100vh; /* 视图高度 */
  width: 100vw; /* 视图宽度 */
}
</style>
