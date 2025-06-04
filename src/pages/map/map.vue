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

interface Device {
  AliasName: string | null;
  devID: string;
  CCID: string;
  devType: string;
  LAT: number;
  LNG: number;
  devActiTime: string;
  devTermTime: string;
}

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
  // 直接使用提供的设备数据
  const devList: Device[] = [
    {
      AliasName: null,
      devID: '862635065082109',
      CCID: '89860841192440170710',
      devType: 'CS1H-50BE',
      LAT: 40.005683,
      LNG: 116.35175,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065093353',
      CCID: '89860841192440170692',
      devType: 'CS1H-50BE',
      LAT: 40.005391,
      LNG: 116.35009,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065106254',
      CCID: '89860841192440170693',
      devType: 'CS1H-50BE',
      LAT: 40.004662,
      LNG: 116.34987,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065115099',
      CCID: '89860841192440170709',
      devType: 'CS1H-50BE',
      LAT: 40.005177,
      LNG: 116.35009,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065126468',
      CCID: '89860841192440170694',
      devType: 'CS1H-50BE',
      LAT: 40.005251,
      LNG: 116.3497,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065140147',
      CCID: '89860841192440170691',
      devType: 'CS1H-50BE',
      LAT: 40.005307,
      LNG: 116.35003,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065146318',
      CCID: '89860841192440170708',
      devType: 'CS1H-50BE',
      LAT: 40.005292,
      LNG: 116.34974,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065146367',
      CCID: '89860841192440170695',
      devType: 'CS1H-50BE',
      LAT: 40.005316,
      LNG: 116.35013,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    },
    {
      AliasName: null,
      devID: '862635065168502',
      CCID: '89860841192440170706',
      devType: 'CS1H-50BE',
      LAT: 40.005133,
      LNG: 116.34982,
      devActiTime: '2025-02-25',
      devTermTime: '1970-01-01'
    }
  ];

  deviceList.value = devList;
  console.log('设备列表:', deviceList.value);

  if (deviceList.value && deviceList.value.length > 0) {
    // 将地图中心设置为第一个设备的坐标
    const firstDevice = deviceList.value[0];
    latitude.value = firstDevice.LAT;
    longitude.value = firstDevice.LNG;

    // 格式化标记点数据
    markers.value = deviceList.value
      .filter((device) => device.LAT && device.LNG) // 过滤掉没有经纬度的设备
      .map((device, index) => ({
        id: index,
        latitude: device.LAT,
        longitude: device.LNG,
        iconPath: '/static/location.svg',
        width: 25,
        height: 25,
        deviceId: device.devID,
        callout: {
          content: `设备ID: ${device.devID}\n类型: ${device.devType}`,
          display: 'BYCLICK',
          padding: 8,
          borderRadius: 4
        }
      }));
    console.log('生成的标记点:', markers.value);
  } else {
    console.warn('设备列表为空');
    uni.showToast({ title: '无设备数据显示', icon: 'none' });
  }
});

const onMarkerTap = (e: any) => {
  console.log('点击了标记点:', e);
  const markerId = e.detail.markerId;
  const tappedMarker = markers.value.find((m) => m.id === markerId);
  if (tappedMarker) {
    console.log('对应设备ID:', tappedMarker.deviceId);
    uni.showToast({
      title: `点击了设备: ${tappedMarker.deviceId}`,
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
