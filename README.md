## vue + echarts + bmap

![](https://user-gold-cdn.xitu.io/2019/5/30/16b07f1f0ba3ea1e?w=1756&h=1238&f=png&s=359820)

### 1. 安装echarts(使用3.x, 4.x版本)

 `npm install echarts@3.0.0 --save` 

如果需要[在线定制](!https://echarts.baidu.com/builder.html)适合自己项目的echarts包，可以使用`<script>`引入。建议开发环境使用`echarts.js`

`<script src="echarts.min.js"></script>`

在调用echarts的组件内引入，也可以在mian.js里面全局引入
`import echarts from 'echarts'`

如果项目使用typescript， 需要`npm install @types/echarts --save`

ECharts 目前暂停地图下载，所以使用百度地图作为底图，需要安装百度地图扩展

### 2. 安装百度地图扩展
 
 `bmap.js` 下载地址`https://github.com/apache/incubator-echarts/tree/master/extension/bmap`
 
 如果安装的是echarts完整包，里面包含百度地图扩展，路径为 `echarts/extension/bmap/bmap.js`
 
 在项目里引入:

`import 'echarts/extension/bmap/bmap'`
或者
`<script src="dist/extension/bmap.min.js"></script>`

### 3. 引入百度地图jssdk

使用[百度地图JavaScript API](http://lbsyun.baidu.com/index.php?title=jspopular3.0/guide/getkey)，需要先注册申请秘钥：
`http://lbsyun.baidu.com/apiconsole/key/create`

```
<script type="text/javascript" src="http://api.map.baidu.com/api?v=3.0&ak=秘钥"></script>
```

百度地图的`个性地图`支持用户使用JavaScript API设置地图底图的样式风格以及控制组成地图底图的元素类的显示和隐藏。可以在[个性化编辑平台](http://lbsyun.baidu.com/customv2/index.html)定制适合自己项目的地图。编辑生成的JSON就是`bmap.mapStyle.styleJson`的值

![百度地图个性化编辑器](https://user-gold-cdn.xitu.io/2019/5/26/16af351af4d0faf7?w=2557&h=1281&f=png&s=1716647)

### 4. 绘制地图

项目代码里面包括echarts地图常用的带有起点和终点信息的`线路图`、`散点图`、`带有涟漪特效动画的散点图`。

