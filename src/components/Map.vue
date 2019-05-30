<template>
  <div ref="map" class="map"></div>
</template>

<script>
/* eslint-disable */
import echarts from 'echarts'
import 'echarts/extension/bmap/bmap'
import linesData from '../data/lines-data.json'
import mapConfig from '../data/map-config.json'

export default {
  name: 'Map',
  data () {
    return {
      chart: echarts.ECharts,
      bmap: {},
      linesSeries: [],
      scatterSeries: [],
      effectScatterSeries: [],
      mapZoom: 6,
      trainIcon: `path://M678.4 122.9c75.1-11.3 145.5 11.7 202.6 65.8 58 54.9 85.7 127.4 77.9 203.3-7.9 75-48.8 149-117.8 214.4-19.3 18.3-71 67.5-145.1 138.2-33.3 31.8-68.9 65.7-104.6 99.8l-34.8 33.1-13.8 13.2c-16.6 15.8-43 15.8-59.6 0L363.1 775.9l-25.4-24.2c-50.9-48.5-101.9-97-152.8-145.4C116 541 75 466.9 67.1 391.8c-7.9-76 19.9-148.3 77.9-203.3 57.1-54.1 127.6-77.1 202.6-65.8 55.8 8.5 112.3 35.4 165.4 78.5 53.2-43 109.7-70 165.4-78.3z`
    }
  },
  mounted () {
    this.initMap()
  },
  methods: {
    initMap () {
      this.chart = echarts.init(this.$refs.map)
      this.getLineSeries()
      this.getScatterSeries()
      this.getEffectScatterSeries()

      this.chart.setOption({
        animation: false,
        legend: {
          orient: 'vertical',
          top: 30,
          left: 30,
          data: linesData.map(v => v.name),
          textStyle: {
            color: '#222222'
          },
          selectedMode: 'multiple'
        },
        bmap: { // 加载 bmap 组件
          center: [104.114129, 37.550339],
          zoom: 6, // 地图当前的缩放比例
          roam: true, // 开启鼠标缩放和平移漫游
          // scaleLimit: { min: 6, max: 12 }, // echarts设置地图最小最大缩放比例的接口不起作用，要使用百度地图的接口设置
          mapStyle: {
            styleJson: mapConfig
          }
        },
        series: [
          ...this.linesSeries, // 带有起点和终点信息的线数据的绘制
          ...this.scatterSeries, // 散点（气泡）图
          ...this.effectScatterSeries // 带有涟漪特效动画的散点（气泡）图
        ]
      })
      // 获取百度地图实例，使用百度地图自带的控件
      this.bmap = this.chart.getModel().getComponent('bmap').getBMap()
      this.bmap.setMinZoom(6) // 设置地图最小缩放比例
      this.bmap.setMaxZoom(12) // 设置地图最大缩放比例
      // this.bmap.addControl(new BMap.MapTypeControl({ mapTypes: [] })) // 不显示地图右上方的控件
      this.bmap.addControl(new BMap.ScaleControl({ anchor: BMAP_ANCHOR_BOTTOM_LEFT })) // 在左下角显示比例尺控件
      const _this = this
      // 监听地图比例缩放， 可以根据缩放等级控制某些图标的显示
      this.bmap.addEventListener('zoomend', function () {
        _this.mapZoom = _this.bmap.getZoom()
      })
    },
    getLineSeries () {
      let series = []
      linesData.forEach(line => {
        series.push({
          name: line.name,
          type: 'lines',
          coordinateSystem: 'bmap',
          silent: true, // 图形不响应和触发鼠标事件
          polyline: true,
          lineStyle: { color: line.primaryColor },
          z: 3,
          data: [
            { // 浅色底线
              coords: line.stations.map(v => v.value),
              lineStyle: { // 单个数据（单条线）的样式设置。
                normal: {
                  type: 'solid',
                  color: line.bgColor,
                  width: 4,
                  opacity: 0.6
                }
              }
            },
            { // 深色虚线
              coords: line.stations.map(v => v.value),
              lineStyle: { // 单个数据（单条线）的样式设置。
                normal: {
                  type: 'dashed',
                  color: line.primaryColor,
                  width: 4
                }
              }
            }
          ]
        })
      })

      this.linesSeries = series
    },
    getScatterSeries () {
      let series = []
      linesData.forEach(line => {
        series.push({
          name: line.name,
          type: 'scatter',
          coordinateSystem: 'bmap',
          symbol: 'circle',
          symbolSize: 10,
          z: 4,
          label: {
            normal: {
              formatter: '{b}',
              position: 'right',
              color: '#777777',
              show: true
            },
            emphasis: {
              show: true
            }
          },
          itemStyle: {
            normal: {
              color: '#FFFFFF',
              borderColor: '#777777',
              borderWidth: 2
            }
          },
          data: line.stations
        })
      })

      this.scatterSeries = series
    },
    getEffectScatterSeries () {
      let series = []
      const points = [
        {
          id: 1,
          name: '兔子',
          lineId: 1,
          pre: { 'id': 8, 'name': '西宁', 'value': [101.82139605321802, 36.62634069485973] },
          next: { 'id': 9, 'name': '乌鲁木齐', 'value': [87.53582115349052, 43.847009771041094] },
          travlled: 0.3
        },
        {
          id: 2,
          name: '乌龟',
          lineId: 1,
          pre: { 'id': 5, 'name': '太原', 'value': [112.61726434350447, 37.79736828479828] },
          next: { 'id': 4, 'name': '石家庄', 'value': [114.4712305290683, 38.07207484076872] },
          travlled: 0.3
        },
        {
          id: 22,
          name: '火箭',
          lineId: 2,
          pre: { "id": 18, "name": "长沙", "value": [113.071503, 28.152942] },
          next: { "id": 19, "name": "广州", "value": [113.264437, 23.154981] },
          travlled: 0.3
        },
        {
          id: 3,
          name: '带鱼',
          lineId: 2,
          pre: { 'id': 11, 'name': '哈尔滨', 'value': [126.71808, 45.794479] },
          next: { 'id': 12, 'name': '长春', 'value': [125.330802, 43.917542] },
          travlled: 0.3
        }
      ]

      const getMiddlePoint = (start, end, percent) => {
        const x = start[0] + (end[0] - start[0]) * percent
        const y = start[1] + (end[1] - start[1]) * percent
        return [x, y]
      }

      linesData.forEach(line => {
        const  pointsOnLine = points.filter(v => v.lineId === line.id)
        if ( pointsOnLine &&  pointsOnLine.length > 0) {
          const data =  pointsOnLine.map(train => {
            const formatter = `{p2|${train.name}}
                              {p3|\n当前车速：400km/h}
                              {p4|\n即将到达：${train.next.name}}`
            return {
              itemStyle: {
                normal: {
                  color: 'red'
                },
                emphasis: {
                  color: 'red'
                }
              },
              label: {
                normal: {
                  formatter,
                  rich: {
                    img: {
                      backgroundColor: {
                        image: train.screenshot
                      }
                    }
                  }
                }
              },
              value: getMiddlePoint(train.pre.value, train.next.value, train.travlled)
            }
          })
          series.push({
            name: line.name,
            type: 'effectScatter', // 带有涟漪特效动画的散点（气泡）图
            coordinateSystem: 'bmap',
            symbol: this.trainIcon, // 使用自定义的SVG图标
            symbolSize: [10, 10],
            legendHoverLink: false,
            z: 6,
            effectType: 'ripple',
            rippleEffect: {
              period: 2, // 涟漪特效的动画周期
              scale: 5, // 涟漪特效动画中波纹的最大缩放比例
              brushType: 'fill' // 涟漪特效的波纹绘制方式
            },
            label: { // 鼠标浮动到特效点上时会显示标签
              normal: {
                show: false,
                position: ['500%', '300%'],
                distance: 20,
                color: '#222222',
                align: 'center',
                backgroundColor: '#FFFFFF',
                borderRadius: 5,
                padding: 20,
                shadowColor: 'rgba(0,0,0,0.16)',
                shadowBlur: 6,
                shadowOffsetX: 0,
                shadowOffsetY: 3,
                width: 200,
                rich: { // 富文本标签样式 
                  p2: {
                    fontSize: 16,
                    color: '#222222',
                    fontWeight: 'bolder',
                    lineHeight: 40
                  },
                  p3: {
                    fontSize: 14,
                    color: '#222222',
                    lineHeight: 18
                  },
                  p4: {
                    fontSize: 14,
                    color: '#222222',
                    lineHeight: 18
                  }
                }
              },
              emphasis: {
                show: true
              }
            },
            data: data
          })
        }
      })
      this.effectScatterSeries = series
    }
  }
}
</script>

<style scoped>
.map {
  width: 100%;
  height: 100%;
}
</style>
