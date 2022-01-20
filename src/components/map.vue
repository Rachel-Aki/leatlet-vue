<template>
  <el-container id="container">
  <el-header>犯罪预测系统</el-header>
  <el-container>
    <el-aside width="200px">
      <el-menu default-active="2" class="el-menu-vertical-demo" :collapse-transition="true"
         unique-opened :collapse="false" :router="true" :default-openeds="opends"
         background-color="#1F66AC" text-color="#fff" active-text-color="#ffd04b">
    <el-submenu index="1" >
    <template slot="title">
      <i class="el-icon-location"></i>
      <span>短期预测</span>
    </template>
    <el-menu-item index="1-1">
      <i class="el-icon-menu"></i>
      <span slot="title">时空核密度预测</span>
    </el-menu-item>
    <el-menu-item index="1-2">
      <i class="el-icon-menu"></i>
      <span slot="title">随机森林预测</span>
    </el-menu-item>
    <el-menu-item index="1-3">
      <i class="el-icon-menu"></i>
      <span slot="title">余震模型预测</span>
    </el-menu-item>
    <el-menu-item index="1-4">
      <i class="el-icon-menu"></i>
      <span slot="title">风险地形预测</span>
    </el-menu-item>
  </el-submenu>
  <el-submenu index="2" >
    <template slot="title">
      <i class="el-icon-location"></i>
      <span>长期预测</span>
    </template>
    <el-menu-item index="2-1">
      <i class="el-icon-menu"></i>
      <span slot="title">犯罪空间转移</span>
    </el-menu-item>
    <el-menu-item index="2-2">
      <i class="el-icon-menu"></i>
      <span slot="title">LSTM模型</span>
    </el-menu-item>
  </el-submenu>
  <el-submenu index="3" >
    <template slot="title">
      <i class="el-icon-location"></i>
      <span>专题图输出</span>
    </template>
    <el-menu-item index="3-1">
      <i class="el-icon-menu"></i>
      <span slot="title">犯罪热点专题图</span>
    </el-menu-item>
    <el-menu-item index="3-2">
      <i class="el-icon-menu"></i>
      <span slot="title">巡逻路线专题图</span>
    </el-menu-item>
  </el-submenu>
    <el-submenu index="4" >
    <template slot="title">
      <i class="el-icon-location"></i>
      <span>基本功能</span>
    </template>
    <el-menu-item index="4-1">
      <i class="el-icon-menu"></i>
      <span slot="title">人口密度</span>
    </el-menu-item>
    <el-menu-item index="4-2">
      <i class="el-icon-menu"></i>
      <span slot="title">警察局分布</span>
    </el-menu-item>
    <el-menu-item index="4-3">
      <i class="el-icon-menu"></i>
      <span slot="title">各类POI</span>
    </el-menu-item>
     <el-menu-item index="4-4">
      <i class="el-icon-menu"></i>
      <span slot="title">案件分布</span>
    </el-menu-item>
  </el-submenu>
</el-menu>
    </el-aside>
    <el-main>
      <template>
        <div>
          <div id="Map"></div>
        </div>
    </template>
    </el-main>
  </el-container>
</el-container>
</template>

<script>
// import L from 'leaflet'
import * as L from 'leaflet'
import 'leaflet.markercluster/dist/MarkerCluster.css'
import 'leaflet.markercluster/dist/MarkerCluster.Default.css'
import 'leaflet.markercluster'
import HeatmapOverlay from 'heatmap.js/plugins/leaflet-heatmap'
import * as echarts from 'echarts'

export default{
  name: 'Map',
  components: {

  },
  data () {
    return {
      opends: ['1', '2', '3'],
      uniqueOpened: false,
      map: ''
    }
  },
  mounted () {
    this.initDate()
    this.cluster1()
    this.getRandomLatLng()
    this.createMakerByLatlng()
    // this.cluster1()
  },
  methods: {
    getRandomLatLng () {
      let bounds = this.map.getBounds()
      let southWest = bounds.getSouthWest()
      let northEast = bounds.getNorthEast()
      let lngSpan = northEast.lng - southWest.lng
      let latSpan = northEast.lat - southWest.lat

      return L.latLng(
        southWest.lat + latSpan * Math.random(),
        southWest.lng + lngSpan * Math.random()
      )
    },

    createMakerByLatlng (latlng, options) {
      return L.marker(latlng, options)
    },
    initDate () {
      this.map = L.map('Map', {
        center: [39.90896, 116.391779], // 地图中心
        zoom: 15, // 缩放比列
        zoomControl: true, // 禁用 + - 按钮
        doubleClickZoom: false, // 禁用双击放大
        attributionControl: false // 移除右下角leaflet标识
      })
      L.tileLayer(
        'http://{s}.tile.osm.org/{z}/{x}/{y}.png'
      ).addTo(this.map)
      //   this.map.removeLayer(name)  // 移除图层
      this.map.pm.addControls({
        position: 'topleft',
        drawPolygon: false, // 添加绘制多边形
        drawMarker: false, // 添加按钮以绘制标记
        drawCircleMarker: false, // 添加按钮以绘制圆形标记
        drawPolyline: false, // 添加按钮绘制线条
        drawRectangle: true, // 添加按钮绘制矩形
        drawCircle: false, //  添加按钮绘制圆圈
        editMode: true, //  添加按钮编辑多边形
        dragMode: true, //   添加按钮拖动多边形
        cutPolygon: true, // 添加一个按钮以删除图层里面的部分内容
        removalMode: true // 清除图层
      })
      // 设置绘制后的线条颜色等
      this.map.pm.setPathOptions({
        color: 'orange',
        fillColor: 'green',
        fillOpacity: 0.4
      })
      this.map.pm.setLang('zh')
      this.map.on('click', function (e) {
        console.log(e)
        alert('纬度：' + e.latlng.lat + '\n经度：' + e.latlng.lng)
      })
      var cfg = {
        'radius': 0.2,
        'maxOpacity': 0.8,
        'scaleRadius': true,
        'useLocalExtrema': true,
        latField: 'lat',
        lngField: 'lng',
        valueField: 'count'
      }
      var testData = {
        max: 4,
        data: [{ lat: 41.53, lng: -87.61, count: 3 },
          { lat: 41.51, lng: -87.31, count: 1 },
          { lat: 41.52, lng: -87.41, count: 9 },
          { lat: 41.61, lng: -87.21, count: 8 },
          { lat: 41.31, lng: -87.43, count: 7 },
          { lat: 41.63, lng: -87.51, count: 6 },
          { lat: 41.22, lng: -87.46, count: 5 }
        ]
      }
      this.heatmapLayer = new HeatmapOverlay(cfg)
      this.heatmapLayer.addTo(this.map)
      this.heatmapLayer.setData(testData)

      var chart = L.control({position: 'bottomright'})
      chart.onAdd = function () {
        var div = L.DomUtil.create('div', 'info chart')
        div.id = 'chatrdemo'
        return div
      }
      chart.addTo(this.map)

      // 基于准备好的dom，初始化echarts实例
      var myChart = echarts.init(document.getElementById('chatrdemo'), 'light')
      // 指定图表的配置项和数据
      var option = {
        tooltip: {
          trigger: 'axis'
        },
        xAxis: [{
          type: 'category',
          data: ['1月', '2月', '3月', '4月']
        }],
        yAxis: [{
          type: 'value',
          name: '水量',
          min: 0,
          max: 50,
          interval: 50,
          axisLabel: {
            formatter: '{value} ml'
          }
        }, {
          type: 'value',
          name: '温度',
          min: 0,
          max: 10,
          interval: 5,
          axisLabel: {
            formatter: '{value} °C'
          }
        }],
        series: [{
          name: '蒸发量',
          type: 'bar',
          data: [2.0, 4.9, 7.0, 23.2]
        }, {
          name: '降水量',
          type: 'bar',
          data: [2.6, 5.9, 9.0, 26.4]
        }, {
          name: '平均温度',
          type: 'line',
          yAxisIndex: 1,
          data: [2.0, 2.2, 3.3, 4.5]
        }]
      }
      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option)
    },
    cluster1 () {
      let CreateMakerCluster = L.markerClusterGroup()
      for (let i = 0; i < 1000; i++) {
        let latlng = this.getRandomLatLng()
        let maker = this.createMakerByLatlng(latlng)
        CreateMakerCluster.addLayer(maker)
      }
      this.map.addLayer(CreateMakerCluster)
    }
  }
}
</script>

<style  scoped>
  .el-header {
    background-color: #1e3247;
    color: #333;
    text-align: center;

  }

  .el-aside {
    background-color: #1e3247;
    color: rgb(55, 40, 82);
    text-align: center;

  }

  .el-main {
    background-color: #1e3247;
    color: #333;
    text-align: center;
    /* line-height: 160px; */
    height: 919px;
    padding: 10px;
  }
  #Map {
        width: 100%;
        height: calc(100vh);
        z-index: 1;
  }
  .chart {
    width: 250px;
    height: 150px;
    background-color: rgb(124, 64, 64);
}

</style>
