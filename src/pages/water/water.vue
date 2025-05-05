<template>
  <view class="container">
    <qiun-data-charts
      type="map"
      :opts="opts"
      :chartData="chartData"
      canvasId="WaterSituationMap"
      id="WaterSituationMap"
      class="charts" />
  </view>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { chinaGeo } from '@/components/u-charts/map-china';
import QiunDataCharts from '@/components/qiun-data-charts/qiun-data-charts.vue';
const provinces = [
  '北京市',
  '天津市',
  '河北省',
  '山西省',
  '内蒙古自治区',
  '辽宁省',
  '吉林省',
  '黑龙江省',
  '上海市',
  '江苏省',
  '浙江省',
  '安徽省',
  '福建省',
  '江西省',
  '山东省',
  '河南省',
  '湖北省',
  '湖南省',
  '广东省',
  '广西壮族自治区',
  '海南省',
  '重庆市',
  '四川省',
  '贵州省',
  '云南省',
  '西藏自治区',
  '陕西省',
  '甘肃省',
  '青海省',
  '宁夏回族自治区',
  '新疆维吾尔自治区',
  '台湾省',
  '香港特别行政区',
  '澳门特别行政区'
];

// 生成随机水情数据
const generateRandomData = () => {
  return provinces.map((name) => ({
    name: name,
    value: Math.floor(Math.random() * 101)
  }));
};

const chartData = ref({});
const opts = ref({
  padding: [0, 0, 0, 0],
  dataLabel: true,
  enableScroll: false,
  fontSize: 8,
  fontColor: '#fff',
  extra: {
    map: {
      border: true,
      mercator: true,
      borderWidth: 1,
      borderColor: '#41D7FF',
      fillOpacity: 0.6,
      activeBorderColor: '#00BCFB',
      activeFillColor: '#00BCFB',
      activeFillOpacity: 1
    }
  }
});

function getChartData() {
  const mockData = generateRandomData();
  return chinaGeo.features.map((province) => {
    for (let i = 0; i < mockData.length; i++) {
      if (province.properties.name === mockData[i].name) {
        return {
          ...province,
          ...mockData[i],
          color: '#0D9FD8'
        };
      }
    }
    return {
      ...province,
      value: 0,
      color: '#ccc'
    };
  });
}

onMounted(() => {
  chartData.value = {
    series: getChartData()
  };
});
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100vh;
  background-color: #fff;
}

.charts {
  width: 100%;
  height: 90vh;
}
</style>
