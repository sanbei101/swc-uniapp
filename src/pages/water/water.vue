<template>
  <view class="container">
    <qiun-data-charts type="map" :opts="opts" :chartData="chartData" class="charts" />
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
  color: [
    '#1890FF',
    '#91CB74',
    '#FAC858',
    '#EE6666',
    '#73C0DE',
    '#3CA272',
    '#FC8452',
    '#9A60B4',
    '#ea7ccc'
  ],
  padding: [0, 0, 0, 0],
  dataLabel: true,
  enableScroll: false,
  extra: {
    map: {
      border: true,
      borderWidth: 1,
      borderColor: '#666666',
      fillOpacity: 0.6,
      activeBorderColor: '#F04864',
      activeFillColor: '#FACC14',
      activeFillOpacity: 1
    }
  }
});
const mockData = generateRandomData();
function getChartData() {
  const dataMap = new Map(mockData.map((item) => [item.name, item.value]));
  return chinaGeo.features.map((province) => {
    const value = dataMap.get(province.properties.name);
    if (value !== undefined) {
      return {
        ...province,
        value
      };
    } else {
      return {
        ...province,
        value: 0
      };
    }
  });
}

onMounted(() => {
  setTimeout(() => {
    chartData.value = {
      series: getChartData()
    };
  }, 500);
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
