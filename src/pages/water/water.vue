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
import mapData from '@/components/u-charts/map.json';

const provinces = [
  '北京',
  '天津',
  '河北',
  '山西',
  '内蒙古',
  '辽宁',
  '吉林',
  '黑龙江',
  '上海',
  '江苏',
  '浙江',
  '安徽',
  '福建',
  '江西',
  '山东',
  '河南',
  '湖北',
  '湖南',
  '广东',
  '广西',
  '海南',
  '重庆',
  '四川',
  '贵州',
  '云南',
  '西藏',
  '陕西',
  '甘肃',
  '青海',
  '宁夏',
  '新疆',
  '台湾',
  '香港',
  '澳门'
];

// 生成随机水情数据
const generateRandomData = () => {
  return provinces.map((name) => ({
    name: name,
    value: Math.floor(Math.random() * 101) // 生成 0-100 的随机整数
  }));
};

const chartData = ref({});
const opts = ref({
  title: {
    text: '全国水情模拟图',
    offsetCenter: [0, '45%'] // 调整标题位置避免遮挡南海诸岛
  },
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
  dataLabel: true, // 显示数据标签（省份名称）
  enableScroll: false, // 禁止滚动
  extra: {
    map: {
      border: true, // 显示边界线
      borderWidth: 1,
      borderColor: '#666666',
      fillOpacity: 0.8, // 地图区域透明度
      activeBorderColor: '#f04864', // 选中区域边界颜色
      activeFillColor: '#facc14', // 选中区域填充颜色
      activeFillOpacity: 1, // 选中区域透明度
      mercator: true // 使用墨卡托投影，可能需要额外配置或地图数据支持
    }
  },
  legend: {
    show: false // 通常地图不需要图例，依赖 visualMap
  },
  visualMap: {
    // 视觉映射组件
    show: true,
    min: 0,
    max: 100,
    left: '10', // 调整位置到左侧
    bottom: '10', // 调整位置到底部
    text: ['高（涝）', '低（旱）'], // 两端文本
    calculable: true, // 是否显示拖拽用的手柄
    inRange: {
      color: ['#ffeda0', '#feb24c', '#f03b20'] // 从黄到红的颜色过渡，可自定义
      // 或者使用 ['#313695', '#4575b4', '#74add1', '#abd9e9', '#e0f3f8', '#ffffbf', '#fee090', '#fdae61', '#f46d43', '#d73027', '#a50026'] 等更丰富的色阶
    },
    orient: 'vertical', // 垂直显示
    itemWidth: 20,
    itemHeight: 140,
    align: 'bottom',
    textStyle: {
      color: '#333'
    }
  }
});

onMounted(() => {
  // 模拟异步获取数据
  setTimeout(() => {
    const seriesData = generateRandomData();
    chartData.value = {
      series: [
        {
          data: seriesData
        }
      ]
    };
    console.log('Chart Data:', JSON.stringify(chartData.value));
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
  height: 100vh; /* 使容器占满整个视口高度 */
  background-color: #fff;
}

.charts {
  width: 100%;
  height: 90vh; /* 让图表占据大部分高度 */
}
</style>
