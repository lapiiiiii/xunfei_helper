
<template>
  <view class="container">
    <view class="header">
      <view class="back-icon" @tap="navigateBack">&#x2190;</view>
      <text class="title">编辑个人资料</text>
    </view>
    <view class="avatar-section" @tap="changeAvatar">
      <image class="avatar" src="https://img0.baidu.com/it/u=1264336001,1107747499&fm=253&fmt=auto&app=120&f=JPEG?w=608&h=608"></image>
      <image class="edit-icon" src="../../static/settings/相机1-fill (1).png"></image>
    </view>
    <view class="info-row">
      <text>名字</text>
      <text>{{ name }}</text>
      <view class="arrow" @tap="showEditNamePopup">&#x2192;</view>
    </view>
    <u-modal v-model="showNameModal" title="编辑名字">
      <view class="input-section">
        <u-input v-model="newName" placeholder="请输入新名字" />
      </view>
      <view class="modal-buttons">
        <button @tap="cancelEdit">取消</button>
        <button @tap="saveName">保存</button>
      </view>
    </u-modal>
  </view>
</template>

<script>
export default {
  data() {
    return {
      name: 'John Doe',
      newName: '',
      showNameModal: false
    };
  },
  methods: {
    navigateBack() {
      uni.navigateBack();
    },
    changeAvatar() {
      // 实现换头像功能
    },
    showEditNamePopup() {
      this.newName = this.name;
      this.showNameModal = true;
    },
    cancelEdit() {
      this.showNameModal = false;
    },
    saveName() {
      this.name = this.newName;
      this.showNameModal = false;
    }
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 10px;
}
.header {
  width: 100%;
  display: flex;
  justify-content: center;
  position: relative;
}
.back-icon {
  position: absolute;
  left: 10px;
}
.title {
  font-size: 18px;
}
.avatar-section {
  position: relative;
  margin-top: 20px;
}
.avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}
.edit-icon {
  position: absolute;
  right: 0;
  bottom: 0;
  width: 24px;
  height: 24px;
}
.info-row {
  display: flex;
  justify-content: space-between;
  width: 100%;
  align-items: center;
  margin-top: 20px;
}
.input-section {
  margin-top: 20px;
}
.modal-buttons {
  display: flex;
  justify-content: space-around;
  margin-top: 10px;
}
</style>
