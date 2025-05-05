<template>
  <view class="container">
    <!-- 标题栏 -->
    <view class="header">
      <view class="tab-active">设备管理</view>
      <view class="tab" @click="navigateToMap">分布图</view>
      <view class="tab" @click="navigateToWater">水情图</view>
    </view>

    <!-- 操作按钮区 -->
    <view class="operation-bar">
      <view class="operation-btn" @click="handleEquationSettings">
        <uni-icons type="compose" size="16" color="#007aff"></uni-icons>
        <text>方程设置</text>
      </view>
      <view class="operation-btn" @click="handleReplySame">
        <uni-icons type="refresh" size="16" color="#007aff"></uni-icons>
        <text>回复同前</text>
      </view>
      <view class="operation-btn" @click="handleModifyDevice">
        <uni-icons type="gear" size="16" color="#007aff"></uni-icons>
        <text>修改设备</text>
      </view>
      <view class="operation-btn" @click="handleViewLatestData">
        <uni-icons type="download" size="16" color="#007aff"></uni-icons>
        <text>查看设备最新数据</text>
      </view>
      <view class="operation-btn" @click="handleGetLatestLocation">
        <uni-icons type="location" size="16" color="#007aff"></uni-icons>
        <text>获取最新位置</text>
      </view>
    </view>

    <!-- 表格区域 -->
    <view class="table">
      <!-- 表头 -->
      <view class="table-header">
        <view class="th check-column">
          <checkbox :checked="allChecked" value="all" />
        </view>
        <view class="th">设备ID</view>
        <view class="th">设备别名</view>
        <view class="th">产品类型</view>
        <view class="th">CCID</view>
        <view class="th">纬度</view>
        <view class="th">经度</view>
        <view class="th">定位有效</view>
      </view>

      <!-- 表格内容 -->
      <scroll-view scroll-y scroll-x class="table-body">
        <view class="tr" v-for="(item, index) in deviceList" :key="index">
          <view class="td check-column">
            <checkbox :checked="item.checked" />
          </view>
          <view class="td">{{ item.deviceId }}</view>
          <view class="td">{{ item.deviceName }}</view>
          <view class="td">{{ item.productType }}</view>
          <view class="td">{{ item.ccid }}</view>
          <view class="td">{{ item.latitude }}</view>
          <view class="td">{{ item.longitude }}</view>
          <view class="td">{{ item.valid }}</view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue';
import { onLoad } from '@dcloudio/uni-app';
export interface Device {
  checked: boolean;
  deviceId: string;
  deviceName: string;
  productType: string;
  ccid: string;
  latitude: string;
  longitude: string;
  valid: string;
}

const navigateToMap = () => {
  uni.navigateTo({
    url: '/pages/map/map',
    success: function (res) {
      res.eventChannel.emit('acceptDataFromOpenerPage', { data: deviceList });
    },
    fail: function (err) {
      console.error('导航到地图页面失败:', err);
      uni.showToast({
        title: '页面跳转失败',
        icon: 'none'
      });
    }
  });
};
const navigateToWater = () => {
  uni.navigateTo({
    url: '/pages/water/water',
    fail: function (err) {
      console.error('导航到水资源页面失败:', err);
      uni.showToast({
        title: '页面跳转失败',
        icon: 'none'
      });
    }
  });
};
const mockList = () => {
  const baseDeviceId = 862635065082100;
  const baseCcid = 898608411924401707000;
  for (let i = 1; i <= 50; i++) {
    const newDeviceId = (baseDeviceId + i).toString();
    const newCcid = (baseCcid + i).toString();
    deviceList.push({
      productType: 'CS1H-50BE',
      valid: '1',
      checked: false,
      deviceId: newDeviceId,
      deviceName: `农大-${newDeviceId}`,
      ccid: newCcid,
      latitude: (40.005683 + (Math.random() - 0.5) * 0.01).toFixed(6),
      longitude: (116.35175 + (Math.random() - 0.5) * 0.01).toFixed(6)
    });
  }
};
onLoad(() => mockList());
const deviceList = reactive<Device[]>([]);

// 是否全选
const allChecked = ref(false);

// 切换全选
const toggleAllChecked = () => {
  console.log('Before toggleAllChecked:', allChecked.value, JSON.stringify(deviceList));
  allChecked.value = !allChecked.value;
  deviceList.forEach((item) => (item.checked = allChecked.value));
  console.log('After toggleAllChecked:', allChecked.value, JSON.stringify(deviceList));
};

// 切换单选
const toggleChecked = (index: number) => {
  deviceList[index].checked = !deviceList[index].checked;
  // 检查是否所有项都被选中
  allChecked.value = deviceList.every((item) => item.checked);
};
const handleEquationSettings = () => {
  console.log('点击了 方程设置');
  // 在这里添加实际的业务逻辑
  uni.showToast({ title: '点击了 方程设置', icon: 'none' });
};

const handleReplySame = () => {
  console.log('点击了 回复同前');
  uni.showToast({ title: '点击了 回复同前', icon: 'none' });
};

const handleModifyDevice = () => {
  console.log('点击了 修改设备');
  uni.showToast({ title: '点击了 修改设备', icon: 'none' });
};

const handleViewLatestData = () => {
  console.log('点击了 查看设备最新数据');
  uni.showToast({ title: '点击了 查看设备最新数据', icon: 'none' });
};

const handleGetLatestLocation = () => {
  console.log('点击了 获取最新位置');
  uni.showToast({ title: '点击了 获取最新位置', icon: 'none' });
};
onLoad(() => {
  console.log('页面加载完成');
});
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  padding: 0;
  font-size: 28rpx;
  background-color: #f5f7fa;
  height: 100vh;
}

.header {
  display: flex;
  background-color: #ffffff;
  padding: 20rpx 30rpx;
  box-shadow: 0 2rpx 6rpx rgba(0, 0, 0, 0.1);
}

.tab {
  padding: 10rpx 30rpx;
  margin-right: 20rpx;
}

.tab-active {
  padding: 10rpx 30rpx;
  margin-right: 20rpx;
  color: #007aff;
  border-bottom: 4rpx solid #007aff;
}

.operation-bar {
  display: flex;
  flex-wrap: wrap;
  padding: 20rpx;
  background-color: #ffffff;
  margin-top: 20rpx;
}

.operation-btn {
  display: flex;
  align-items: center;
  background-color: #f2f6fc;
  padding: 10rpx 20rpx;
  margin-right: 20rpx;
  margin-bottom: 10rpx;
  border-radius: 6rpx;
}

.operation-btn text {
  margin-left: 10rpx;
  font-size: 24rpx;
}

.table {
  flex: 1;
  margin: 20rpx;
  background-color: #ffffff;
  border-radius: 10rpx;
  overflow: hidden;
}

.table-header {
  display: flex;
  background-color: #ebeef5;
  font-weight: bold;
}

.table-body {
  height: calc(100vh - 300rpx);
}

.tr {
  display: flex;
  border-bottom: 2rpx solid #ebeef5;
}

.th,
.td {
  flex: 1;
  padding: 20rpx 10rpx;
  text-align: center;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  min-width: 180rpx;
}

.check-column {
  flex: 0.3;
  min-width: 60rpx;
}

/* 确保滚动效果正常 */
::-webkit-scrollbar {
  width: 10rpx;
  height: 10rpx;
}

::-webkit-scrollbar-thumb {
  background-color: #c0c4cc;
  border-radius: 5rpx;
}
</style>
