<template>
  <div>
    <div id="gaugeContainer" style="width:100%; height:8rem"></div>
  </div>
</template>
        
<script>
const echarts = require("echarts");
// 导入数据
import { option } from "../../assets/js/option-guage";
export default {
  name: "Guage",
  data() {
    return {};
  },
  mounted() {
    let myChart = echarts.init(document.getElementById("gaugeContainer"));
    myChart.setOption(option);
  }
};
</script>
        
<style scoped>
#gaugeContainer {
  width: 100%;
  height: 400px;
  background: linear-gradient(to left bottom, #ff898f, #fff);
}
</style>
