<template>
  <view class="monitor-container">
    <!-- 头部操作栏 -->
    <view class="header-bar">
      <uni-forms ref="form" :modelValue="searchParams" label-position="left" label-width="80px">
        <uni-forms-item label="设备编号" name="deviceId">
          <uni-easyinput type="text" v-model="searchParams.DeviceID" placeholder="请输入设备编号" />
        </uni-forms-item>
        <uni-forms-item label="开始日期" name="startDate">
          <uni-datetime-picker type="datetime" v-model="searchParams.StartDate" />
        </uni-forms-item>
        <uni-forms-item label="结束日期" name="endDate">
          <uni-datetime-picker type="datetime" v-model="searchParams.EndDate" />
        </uni-forms-item>
      </uni-forms>
      <view class="action-buttons">
        <button type="primary" size="mini" @click="handleQueryData">
          <uni-icons type="search" size="14" color="#fff"></uni-icons> 查询数据
        </button>
        <button type="default" size="mini" @click="prepareChartData">
          <uni-icons type="refreshempty" size="14"></uni-icons> 绘制曲线
        </button>
        <button type="default" size="mini" @click="handleExportExcel">
          <uni-icons type="download" size="14"></uni-icons> 导出Excel
        </button>
        <button type="default" size="mini" @click="handleExportCsv">
          <uni-icons type="download" size="14"></uni-icons> 导出CSV
        </button>
      </view>
    </view>

    <!-- 数据表格 -->
    <view class="table-wrapper">
      <view class="table-header-row">
        <view class="th checkbox-column">
          <view
            class="custom-checkbox"
            :class="{ checked: allChecked }"
            @click="toggleAllChecked"></view>
        </view>
        <view class="th index-column">序号</view>
        <view class="th">设备编号</view>
        <view class="th">设备别名</view>
        <view class="th">剩余电量</view>
        <view class="th">信号质量</view>
        <view class="th">采集时间</view>
        <view class="th">土壤温度(℃)</view>
        <view class="th">土壤湿度(%)</view>
        <view class="th">土壤电导(ms/cm)</view>
        <view class="th">累积水分</view>
        <view class="th">大气压强(kPa)</view>
        <view class="th">空气温度(℃)</view>
        <view class="th">空气湿度(%)</view>
      </view>
      <scroll-view scroll-y scroll-x class="table-body-scroll">
        <view class="tr" v-for="(item, index) in tableDataList" :key="item.ID">
          <view class="td checkbox-column">
            <view
              class="custom-checkbox"
              :class="{ checked: item.checked }"
              @click="toggleRowChecked(index)"></view>
          </view>
          <view class="td index-column">{{ index + 1 }}</view>
          <view class="td">{{ item.DeviceID }}</view>
          <view class="td">{{ item.AliasName }}</view>
          <view class="td">{{ item.POWER }}</view>
          <view class="td">{{ item.CSQ }}</view>
          <view class="td">{{ item.TIME }}</view>
          <view class="td">{{ item.DataTEMPStr }}</view>
          <view class="td">{{ item.DataSWCStr }}</view>
          <view class="td">{{ item.DataECStr }}</view>
          <view class="td">{{ item.SWCSUM }}</view>
          <view class="td">{{ item.DataATM }}</view>
          <view class="td">{{ item.DataATS }}</view>
          <view class="td">{{ item.AirHumidity || 0 }}</view>
          <!-- AirHumidity is a placeholder -->
        </view>
        <view v-if="tableDataList.length === 0" class="empty-data">暂无数据</view>
      </scroll-view>
    </view>

    <!-- 图表 -->
    <view class="chart-wrapper">
      <qiun-data-charts type="line" :opts="chartOptions" :chartData="chartData" />
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue';
import qiunDataCharts from '@/components/qiun-data-charts/qiun-data-charts.vue';

// Type definitions based on the prompt
type Request = {
  DeviceID?: string[];
  EndDate?: string;
  StartDate?: string;
};

type Datum = {
  AliasName: string;
  CSQ: number;
  DataAT: number;
  DataATM: number;
  DataATS: number;
  DataEC: string[];
  DataECStr: string;
  DataSWC: string[];
  DataSWCStr: string;
  DataTEMP: string[];
  DataTEMPStr: string;
  DeviceID: string;
  DeviceTypeName: string;
  ID: number;
  PH: number;
  POWER: number;
  SWCSUM: number;
  TIME: string;
};

// Extended Datum for local table state
interface MonitorDatum extends Datum {
  checked: boolean;
  AirHumidity?: number;
}

const searchParams = reactive<Request>({
  DeviceID: ['862635065106254'],
  StartDate: '2023-05-10 23:27:21',
  EndDate: '2025-05-28 23:27:27'
});

const tableDataList = reactive<MonitorDatum[]>([]);
const allChecked = ref(false);

const chartData = ref({});
const chartOptions = ref({
  padding: [15, 15, 0, 15],
  legend: {},
  xAxis: {
    disableGrid: true
  },
  yAxis: {
    gridType: 'dash',
    dashLength: 2
  },
  extra: {
    line: {
      type: 'straight'
    }
  }
});

// Mock data generation (replace with actual API call)
const generateMockData = (): MonitorDatum[] => {
  const mocks: MonitorDatum[] = [];
  const baseTime = new Date(searchParams.StartDate || Date.now()).getTime();
  for (let i = 0; i < 20; i++) {
    mocks.push({
      ID: i + 1,
      DeviceID: searchParams.DeviceID ? searchParams.DeviceID[0] : `DEV${1000 + i}`,
      AliasName: `农大-${searchParams.DeviceID ? searchParams.DeviceID[0] : `DEV${1000 + i}`}`,
      POWER: 90 + Math.floor(Math.random() * 10),
      CSQ: 20 + Math.floor(Math.random() * 10),
      TIME: new Date(baseTime + i * 30 * 60 * 1000)
        .toISOString()
        .replace('T', ' ')
        .substring(0, 19),
      DataTEMPStr: (22 + Math.random() * 5).toFixed(2),
      DataSWCStr: (0.7 + Math.random() * 0.1).toFixed(5),
      DataECStr: (1.2 + Math.random() * 0.1).toFixed(3),
      SWCSUM: Math.floor(Math.random() * 10),
      DataATM: Math.floor(1000 + Math.random() * 10),
      DataATS: Number((20 + Math.random() * 5).toFixed(2)),
      AirHumidity: Math.floor(50 + Math.random() * 20), // Mock Air Humidity
      DataAT: 0,
      DataEC: [],
      DataSWC: [],
      DataTEMP: [],
      DeviceTypeName: 'TypeX',
      PH: 0,
      checked: false
    });
  }
  return mocks;
};

const handleQueryData = () => {
  console.log('Querying data with params:', searchParams);
  // Simulate API call
  tableDataList.splice(0, tableDataList.length, ...generateMockData());
  allChecked.value = false;
  prepareChartData();
};

const prepareChartData = () => {
  if (tableDataList.length === 0) {
    chartData.value = { categories: [], series: [] };
    return;
  }

  const categories = tableDataList.map((item) => item.TIME.substring(11, 19)); // Show HH:MM:SS for brevity
  const soilTempSeries = {
    name: '土壤温度',
    data: tableDataList.map((item) => parseFloat(item.DataTEMPStr))
  };
  const soilHumiditySeries = {
    name: '土壤湿度',
    data: tableDataList.map((item) => parseFloat(item.DataSWCStr))
  };

  chartData.value = {
    categories,
    series: [soilTempSeries, soilHumiditySeries]
  };
  console.log('Chart data prepared:', chartData.value);
};

const toggleAllChecked = () => {
  allChecked.value = !allChecked.value;
  tableDataList.forEach((item) => (item.checked = allChecked.value));
};

const toggleRowChecked = (index: number) => {
  tableDataList[index].checked = !tableDataList[index].checked;
  allChecked.value = tableDataList.every((item) => item.checked);
};

const handleExportExcel = () => {
  uni.showToast({ title: '导出Excel（待实现）', icon: 'none' });
};

const handleExportCsv = () => {
  uni.showToast({ title: '导出CSV（待实现）', icon: 'none' });
};

onMounted(() => {
  handleQueryData();
});
</script>

<style scoped>
.monitor-container {
  display: flex;
  flex-direction: column;
  padding: 10px;
  font-size: 14px;
  background-color: #f5f7fa;
  height: 100vh;
  box-sizing: border-box;
}

.header-bar {
  background-color: #ffffff;
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.header-bar .uni-forms-item {
  margin-bottom: 10px;
}

.action-buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 10px;
}
.action-buttons button {
  display: flex;
  align-items: center;
  gap: 5px;
}

.table-wrapper {
  flex: 1; /* Takes up remaining space */
  display: flex;
  flex-direction: column;
  background-color: #ffffff;
  border-radius: 5px;
  overflow: hidden; /* Important for scroll-view */
  margin-bottom: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.table-header-row,
.tr {
  display: flex;
  border-bottom: 1px solid #ebeef5;
  width: fit-content; /* For horizontal scroll */
  min-width: 100%; /* Ensure it takes at least full width if content is less */
}

.table-header-row {
  background-color: #f8f8f9;
  font-weight: bold;
  position: sticky; /* Optional: sticky header */
  top: 0; /* Optional: sticky header */
  z-index: 1; /* Optional: sticky header */
}

.th,
.td {
  padding: 12px 8px;
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  /* Define specific widths for columns */
  min-width: 120px; /* Default min-width */
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: center;
}

.checkbox-column {
  min-width: 50px;
  flex-grow: 0;
  flex-shrink: 0;
}
.index-column {
  min-width: 60px;
  flex-grow: 0;
  flex-shrink: 0;
}
.th:nth-child(3),
.td:nth-child(3) {
  min-width: 150px;
} /* 设备编号 */
.th:nth-child(4),
.td:nth-child(4) {
  min-width: 180px;
} /* 设备别名 */
.th:nth-child(7),
.td:nth-child(7) {
  min-width: 160px;
} /* 采集时间 */

.table-body-scroll {
  flex: 1; /* Allows the scroll view to take available space */
  height: 300px; /* Or calculate based on available height */
}

.custom-checkbox {
  width: 18px;
  height: 18px;
  border: 1px solid #dcdfe6;
  border-radius: 3px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  transition: background-color 0.2s, border-color 0.2s;
}

.custom-checkbox.checked {
  background-color: #007aff;
  border-color: #007aff;
}

.custom-checkbox.checked::after {
  content: '';
  display: block;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg) translate(-1px, -1px);
}

.empty-data {
  text-align: center;
  padding: 20px;
  color: #909399;
}

.chart-wrapper {
  background-color: #ffffff;
  padding: 10px;
  border-radius: 5px;
  height: 250px; /* Adjust as needed */
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

/* Scrollbar styling */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

::-webkit-scrollbar-thumb {
  background-color: #c0c4cc;
  border-radius: 3px;
}
</style>
