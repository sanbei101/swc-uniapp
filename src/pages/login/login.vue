<template>
  <view class="container">
    <view class="login-form">
      <view class="title">欢迎登录</view>
      <form @submit="handleLogin">
        <input
          v-model="formData.username"
          class="input-field"
          placeholder="请输入用户名"
          placeholder-class="input-placeholder" />
        <input
          v-model="formData.password"
          class="input-field"
          placeholder="请输入密码"
          placeholder-class="input-placeholder" />
        <button class="submit-button" form-type="submit">登录</button>
      </form>
      <button class="fingerprint-button" @click="handleFingerprintLogin">指纹登录</button>
    </view>
  </view>
</template>

<script setup lang="ts">
import { reactive } from 'vue';

const formData = reactive({
  username: '',
  password: ''
});

export type Response = {
  Data: Data;
  IsSuccess: boolean;
};

export type Data = {
  Area: null;
  ID: number;
  IsAdmin: string;
  IsCreateUser: string;
  IsDelete: string;
  ParentID: string;
  UserID: string;
  UserName: string;
};

const handleLogin = () => {
  if (!formData.username || !formData.password) {
    uni.showToast({
      title: '请输入用户名和密码',
      icon: 'none'
    });
    return;
  }
  if (formData.username === 'SNM' && formData.password === 'qwe123') {
    uni.showToast({
      title: '登录成功',
      icon: 'success'
    });
    uni.navigateTo({ url: '/pages/index/index' });
  } else {
    uni.showToast({
      title: '登录失败',
      icon: 'none'
    });
  }
  // 正式逻辑,解决后端跨域问题后使用

  // uni.request({
  //   url: 'https://swc.cau-iae.cn/webapi/login',
  //   method: 'POST',
  //   data: {
  //     username: formData.username,
  //     password: formData.password
  //   },
  //   success: (res) => {
  //     console.log('登录成功:', res.data);
  //     if ((res.data as Response).IsSuccess) {
  //       uni.showToast({
  //         title: '登录成功',
  //         icon: 'success'
  //       });
  //       uni.navigateTo({ url: '/pages/index/index' });
  //     } else {
  //       uni.showToast({
  //         title: '登录失败',
  //         icon: 'none'
  //       });
  //     }
  //   },
  //   fail: (err) => {
  //     console.error('登录失败:', err);
  //     uni.showToast({
  //       title: '请求失败，请检查网络',
  //       icon: 'none'
  //     });
  //   }
  // });
};

const handleFingerprintLogin = () => {
  uni.checkIsSupportSoterAuthentication({
    success: (res) => {
      console.log('支持的认证方式:', res.supportMode);
      if (res.supportMode.includes('fingerPrint')) {
        checkFingerprintEnrolled();
      } else {
        uni.showToast({
          title: '设备不支持指纹认证',
          icon: 'none'
        });
      }
    },
    fail: (err) => {
      console.error('检查支持的认证方式失败:', err);
      uni.showToast({
        title: '检查认证支持情况失败',
        icon: 'none'
      });
    }
  });
};

const checkFingerprintEnrolled = () => {
  uni.checkIsSoterEnrolledInDevice({
    checkAuthMode: 'fingerPrint',
    success: (res) => {
      console.log('指纹录入情况:', res);
      if (res.isEnrolled) {
        startFingerprintAuthentication();
      } else {
        uni.showToast({
          title: '未录入指纹',
          icon: 'none'
        });
      }
    },
    fail: (err) => {
      console.error('检查指纹录入情况失败:', err);
      uni.showToast({
        title: '检查指纹录入情况失败',
        icon: 'none'
      });
    }
  });
};

const startFingerprintAuthentication = () => {
  uni.startSoterAuthentication({
    requestAuthModes: ['fingerPrint'],
    challenge: '123456', // 挑战因子，随便填写即可
    authContent: '请验证指纹以登录',
    success: (res) => {
      console.log('指纹认证成功:', res);
      if (res.errCode === 0) {
        // 认证成功，模拟登录
        uni.showToast({
          title: '指纹登录成功',
          icon: 'success'
        });
        uni.navigateTo({ url: '/pages/index/index' });
      } else {
        uni.showToast({
          title: `指纹认证失败: ${res.errMsg}`,
          icon: 'none'
        });
      }
    },
    fail: (err) => {
      console.error('指纹认证失败:', err);
      uni.showToast({
        title: '指纹认证失败',
        icon: 'none'
      });
    }
  });
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  overflow-y: hidden; /* 保持此属性以隐藏垂直溢出内容 */
  height: 100vh; /* 将 min-height 更改为 height */
  background-color: #f0f2f5; /* 淡灰色背景 */
}

.login-form {
  background-color: #ffffff; /* 白色背景 */
  padding: 40rpx;
  border-radius: 16rpx; /* 圆角 */
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.1); /* 阴影效果 */
  width: 80%;
  max-width: 600rpx; /* 最大宽度 */
}

.title {
  font-size: 48rpx; /* 增大字体 */
  font-weight: bold;
  text-align: center;
  margin-bottom: 40rpx; /* 增加底部间距 */
  color: #333; /* 深灰色字体 */
}

.input-field {
  height: 88rpx; /* 增加高度 */
  line-height: 88rpx;
  border: 1rpx solid #dcdfe6; /* 边框颜色 */
  border-radius: 8rpx; /* 圆角 */
  padding: 0 20rpx; /* 内边距 */
  margin-bottom: 30rpx; /* 增加底部间距 */
  font-size: 28rpx; /* 字体大小 */
  width: calc(100% - 40rpx); /* 考虑内边距后的宽度 */
}

.input-placeholder {
  color: #c0c4cc; /* 占位符颜色 */
}

.submit-button {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background-color: #007aff; /* 主题蓝色 */
  color: #ffffff; /* 白色文字 */
  border: none;
  border-radius: 8rpx;
  font-size: 32rpx; /* 字体大小 */
  margin-top: 20rpx; /* 顶部间距 */
}

.submit-button:active {
  background-color: #0056b3; /* 点击时颜色变深 */
}

.fingerprint-button {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background-color: #28a745; /* 绿色背景，与登录按钮区分 */
  color: #ffffff;
  border: none;
  border-radius: 8rpx;
  font-size: 32rpx;
  margin-top: 20rpx; /* 与上方按钮的间距 */
}

.fingerprint-button:active {
  background-color: #1e7e34; /* 点击时颜色变深 */
}
</style>
