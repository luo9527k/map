<template>
    <div id="container"></div>
</template>
  
<script>
import AMapLoader from '@amap/amap-jsapi-loader'
import EventBus from '@/EventBus/EventBus'
window._AMapSecurityConfig = {
    securityJsCode: 'dd4832530a24b87c5e5771658d19edf7'
}
export default {
    data() {
        return {
            map: null,
            autoptions: {
                input: ''
            },
            searchPlaceInput: '',
            auto: null,
            placeSearch: null,
            district: null,
            showHeatOrHot: false,
            polygons: []
        }
    },
    methods: {
        initMap() {
            AMapLoader.load({
                key: '8d226312bfdfdfefdafdf8c074e50497', // 申请好的Web端开发者Key，首次调用 load 时必填
                version: '2.0', // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
                plugins: ['AMap.ToolBar', 'AMap.Scale', 'AMap.HawkEye', 'AMap.MapType', 'AMap.Geolocation', 'AMap.AutoComplete','AMap.PlaceSearch'] // 需要使用的的插件列表，如比例尺'AMap.Scale'等
            })
                .then(AMap => {
                    this.map = new AMap.Map('container', {
                        //设置地图容器id
                        viewMode: '3D', //是否为3D地图模式
                        zoom: 10, //初始化地图级别
                        center: [121.473667, 31.230525] //初始化地图中心点位置
                    })
                    this.map.addControl(new AMap.Scale()) //比例尺
                    this.map.addControl(new AMap.ToolBar())
                    this.map.addControl(new AMap.HawkEye())
                    this.map.addControl(new AMap.MapType())
                    this.map.addControl(new AMap.Geolocation())
                    this.auto = new AMap.AutoComplete(this.autoptions)
                    this.placeSearch = new AMap.PlaceSearch({
                        map: this.map
                    }) //构造地点查询类
                    this.auto.on('select', this.select)
                    this.map.setZoom(10, true)

                    // 添加固定点标记
                    let marker1 = new AMap.Marker({
                        position: new AMap.LngLat(121.473667, 31.230525),
                        title: '上海'
                    })
                    //添加点标记
                    this.map.add(marker1)

                    //添加自定义的点标记
                    let defineMarker1 = new AMap.marker({
                        position: new AMap.LngLat(115.27, 29),
                        title: '南昌',
                        icon: require("@/img/ip.png")
                    })
                    this.map.add(defineMarker1)

                    //圆点标记
                    let circleMarker1 = new AMap.CircleMarker({
                        map: this.map,
                        center: new AMap.LngLat(121.473667, 31.230525),
                        radius: 20,
                    })
                    circleMarker1.setMap(this.map)
                })
                .catch(e => {
                    console.log(e)
                })
        },
        select(e) {
            this.placeSearch.setCity(e.poi.adcode)
            this.placeSearch.search(e.poi.name) //关键字查询查询
        },
        // 行政区区域划分
        drawBounds(newValue) {
            //加载行政区划插件
            if (!this.district) {
                //实例化DistrictSearch
                var opts = {
                    subdistrict: 0, //获取边界不需要返回下级行政区
                    extensions: 'all', //返回行政区边界坐标组等具体信息
                    level: 'district' //查询行政级别为 市
                }

                this.map.plugin(['AMap.DistrictSearch'], () => {
                    this.district = new AMap.DistrictSearch(opts)
                })
                // this.district = new AMap.DistrictSearch(opts)
            }
            //行政区查询
            this.district.search(newValue, (status, result) => {
                if (result != null) {
                    this.callBack('搜索成功', 'success')
                    if (this.polygons != null) {
                        this.map.remove(this.polygons) //清除上次结果
                        this.polygons = []
                    }
                    var bounds = result.districtList[0].boundaries
                    if (bounds) {
                        for (var i = 0, l = bounds.length; i < l; i++) {
                            //生成行政区划polygon
                            var polygon = new AMap.Polygon({
                                strokeWeight: 1,
                                path: bounds[i],
                                fillOpacity: 0.4,
                                fillColor: '#80d8ff',
                                strokeColor: '#0091ea'
                            })
                            this.polygons.push(polygon)
                        }
                    }
                    this.map.add(this.polygons)
                    this.map.setFitView(this.polygons) //视口自适应
                } else {
                    this.callBack('搜索失败', 'error')
                }
            })
        },
        callBack(msg, callBackType) {
            this.$message({
                message: msg,
                type: callBackType
            });
        },
    },
    mounted() {
        //DOM初始化完成进行地图初始化
        this.initMap()
    },
    created() {
        EventBus.$on('sharUserInput', value => {
            this.autoptions.input = value.InputId
            this.searchPlaceInput = value.userInput
        })
        EventBus.$on('shareHeatMapShow', value => {
            this.showHeatOrHot = value

        })
    },
    watch: {
        searchPlaceInput(newValue) {
            if (newValue != null) {
                this.placeSearch.search(newValue)
                this.map.setZoom(16, true, 1)
                this.drawBounds(newValue)
            }
        }, showHeatOrHot(newValue) {
            if (newValue) {
                this.showHeatMap
            }
        }
    }
}
</script>
  
<style lang="less">
#container {
    padding: 0px;
    margin: 0px;
    width: 100%;
    height: 100%;
}
</style>
  
  