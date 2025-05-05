<template>
  <view class="map-container">
    <map
      id="map"
      style="width: 100%; height: 100%"
      :latitude="latitude"
      :longitude="longitude"
      :markers="markers"
      :scale="14"
      @markertap="onMarkerTap">
    </map>
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { onLoad } from '@dcloudio/uni-app';
import type { Device } from '@/pages/index/index.vue';
interface Marker {
  id: number;
  latitude: number;
  longitude: number;
  iconPath: string;
  width: number;
  height: number;
  callout?: {
    content: string;
    display: 'ALWAYS' | 'BYCLICK';
    padding: number;
    borderRadius: number;
  };
  deviceId?: string;
}

const latitude = ref(40.005683);
const longitude = ref(116.35175);
const markers = ref<Marker[]>([]);
const deviceList = ref<Device[]>([]);

onLoad(() => {
  const currentPage = getCurrentPages().pop() as any;
  const eventChannel = currentPage?.getOpenerEventChannel();
  if (eventChannel) {
    eventChannel.on('acceptDataFromOpenerPage', function (eventData: { data: Device[] }) {
      console.log('接收到设备列表:', eventData.data);
      deviceList.value = eventData.data;
      if (deviceList.value && deviceList.value.length > 0) {
        // 将地图中心设置为第一个设备的坐标
        const firstDevice = deviceList.value[0];
        latitude.value = parseFloat(firstDevice.latitude);
        longitude.value = parseFloat(firstDevice.longitude);

        // 格式化标记点数据
        markers.value = deviceList.value
          .filter((device) => device.latitude && device.longitude) // 过滤掉没有经纬度的设备
          .map((device, index) => ({
            id: index,
            latitude: parseFloat(device.latitude),
            longitude: parseFloat(device.longitude),
            iconPath: '/static/location.svg',
            width: 25,
            height: 25,
            deviceId: device.deviceId,
            callout: {
              content: `${device.deviceName} ID: ${device.deviceId}`,
              display: 'BYCLICK',
              padding: 8,
              borderRadius: 4
            }
          }));
        console.log('生成的标记点:', markers.value);
      } else {
        console.warn('接收到的设备列表为空或无效');
        uni.showToast({ title: '无设备数据显示', icon: 'none' });
      }
    });
  } else {
    console.error('获取 eventChannel 失败');
    uni.showToast({ title: '无法获取设备数据', icon: 'none' });
  }
});

const onMarkerTap = (e: any) => {
  console.log('点击了标记点:', e);
  const markerId = e.detail.markerId;
  const tappedMarker = markers.value.find((m) => m.id === markerId);
  if (tappedMarker) {
    console.log('对应设备ID:', tappedMarker.deviceId);
    uni.showToast({
      title: `点击了设备: ${tappedMarker.callout?.content.split('\n')[0]}`,
      icon: 'none'
    });
  }
};
</script>

<style>
.map-container {
  width: 100vw;
  height: 100vh;
}
</style>
