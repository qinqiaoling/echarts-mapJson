<template>
  <div class="map-spread">
    <div class="map-main">
	    <div class="chartmap">
	    	<div class="title"><span class="text">产业分布</span></div>
	      	<div id="container" class="map" ref="map"></div>
	      	<!-- <span class="text" v-show="showRetrun" @click="retrunMap">返回</span> -->
	    </div>
    </div>
  </div>
</template>

<script>
import { getLocalJson } from '@/serve/localjson'
import moment from 'moment'
export default {
  data() {
    return {
      rankLoading: false,
      rankData: [],
      echarts: null,
      showRetrun: false,
      areaId: '',
      areaName: '全国',
      myChart:null,
      textdata:'',
      allData:require('@/common/json/chinaprovincecode.json'),//各省份的数据
      whiledata:require('@/common/json/data-1527045631990-r1dZ0IM1X.json'),//全国
      areacode:require('@/common/json/areacode.json'),//全国
     //  provinces:{
     //  	'上海':   require('@/common/json/data-1482909900836-H1BC_1WHg.json'),
	    // '河北':   require('@/common/json/data-1482909799572-Hkgu_yWSg.json'),
	    // '山西':   require('@/common/json/data-1482909909703-SyCA_JbSg.json'),
	    // '内蒙古': require('@/common/json/data-1482909841923-rkqqdyZSe.json'),
	    // '辽宁':   require('@/common/json/data-1482909836074-rJV9O1-Hg.json'),
	    // '吉林':   require('@/common/json/data-1482909832739-rJ-cdy-Hx.json'),
	    // '黑龙江': require('@/common/json/data-1482909803892-Hy4__J-Sx.json'),
	    // '江苏':   require('@/common/json/data-1482909823260-HkDtOJZBx.json'),
	    // '浙江':   require('@/common/json/data-1482909960637-rkZMYkZBx.json'),
	    // '安徽':   require('@/common/json/data-1482909768458-HJlU_yWBe.json'),
	    // '福建':   require('@/common/json/data-1478782908884-B1H6yezWe.json'),
	    // '江西':   require('@/common/json/data-1482909827542-r12YOJWHe.json'),
	    // '山东':   require('@/common/json/data-1482909892121-BJ3auk-Se.json'),
	    // '河南':   require('@/common/json/data-1482909807135-SJPudkWre.json'),
	    // '湖北':   require('@/common/json/data-1482909813213-Hy6u_kbrl.json'),
	    // '湖南':   require('@/common/json/data-1482909818685-H17FOkZSl.json'),
	    // '广东':   require('@/common/json/data-1482909784051-BJgwuy-Sl.json'),
	    // '广西':   require('@/common/json/data-1482909787648-SyEPuJbSg.json'),
	    // '海南':   require('@/common/json/data-1482909796480-H12P_J-Bg.json'),
	    // '四川':   require('@/common/json/data-1482909931094-H17eKk-rg.json'),
	    // '贵州':   require('@/common/json/data-1482909791334-Bkwvd1bBe.json'),
	    // '云南':   require('@/common/json/data-1482909957601-HkA-FyWSx.json'),
	    // '西藏':   require('@/common/json/data-1482927407942-SkOV6Qbrl.json'),
	    // '陕西':   require('@/common/json/data-1482909918961-BJw1FyZHg.json'),
	    // '甘肃':   require('@/common/json/data-1482909780863-r1aIdyWHl.json'),
	    // '青海':   require('@/common/json/data-1482909853618-B1IiOyZSl.json'),
	    // '宁夏':   require('@/common/json/data-1482909848690-HJWiuy-Bg.json'),
	    // '新疆':   require('@/common/json/data-1482909952731-B1YZKkbBx.json'),
	    // '北京':   require('@/common/json/data-1482818963027-Hko9SKJrg.json'),
	    // '天津':   require('@/common/json/data-1482909944620-r1-WKyWHg.json'),
	    // '重庆':   require('@/common/json/data-1482909775470-HJDIdk-Se.json'),
	    // '香港':   require('@/common/json/data-1461584707906-r1hSmtsx.json'),
	    // '澳门':   require('@/common/json/data-1482909771696-ByVIdJWBx.json')
     //  },//各省份的地图json文件
    }
  },
  created() {

  },
  mounted(){
  	// console.log(this.provinces['湖北'])
  	this.whileeara()
  	// window.onresize = function() {
   //  	this.myChart.resize()
   //  }
  },
  methods: {
  	whileeara(){
  		this.myChart = this.$echarts.init(document.getElementById('container'))
		for (var i = 0; i < this.allData.length; i++) {
		    this.allData[i].value = Math.round(Math.random() * 100);
		}
		console.log(this.allData,this.whiledata)
		this.loadMap(this.whiledata, 'china', this.allData); //初始化全国地图
		var timeFn = null;
		//单击切换到省级地图，当mapCode有值,说明可以切换到下级地图
		this.myChart.on('click', (params) => {
			console.log('点击参数',params)
		    clearTimeout(timeFn);
		    //由于单击事件和双击事件冲突，故单击的响应事件延迟250毫秒执行
		    let _this = this
		    timeFn = setTimeout(function() {
		        var name = params.name; //地区name
		        // var mapCode = _this.provinces[name]; //地区的json数据
		        var mapCode = '';
		        // if(mapCode == undefined){
		        	_this.areacode.map((item,index)=>{
		        		if(item.name == name&&params.data.level){
		        			mapCode = require(`@/common/json/geometryCouties/${item.code}.json`)
		        		}
		        	})
		        // }
		        console.log(mapCode)
		        if (!mapCode) {
		            // alert('无此区域地图显示');
		            // return;
		        }else{
		        	let op = {},opd = [];
			        for (var i = 0; i < mapCode.features.length; i++) {
			        	op = {}
			        	op.name = mapCode.features[i].properties.name
			        	op.value = Math.round(Math.random() * 100)
			        	op.id = mapCode.features[i].properties.id||mapCode.features[i].properties.adcode
			        	if(mapCode.features[i].properties.level){
			        		op.level = mapCode.features[i].properties.level
			        	}
					    opd.push(op)
					}
			        _this.loadMap(mapCode, name, opd);
		        }
		    }, 300);
		});
		// 绑定双击事件，返回全国地图
		this.myChart.on('dblclick', (param) => {
		    //当双击事件发生时，清除单击事件，仅响应双击事件
		    clearTimeout(timeFn);
		    //返回全国地图
		    let _this = this
		    _this.loadMap(_this.whiledata, 'china', this.allData); //初始化全国地图
		});
  	},
  	loadMap(mapCode, name, seriesdata) {
  		let data = mapCode
        if (data) {
        	this.myChart = this.$echarts.init(document.getElementById('container'))
            this.$echarts.registerMap(name, data);
            let sortdata = []
            if(seriesdata){
            	seriesdata.map((item)=>{
            		sortdata.push(item.value)
            	})
            }
			console.log(seriesdata)
            var option = {
                tooltip: {
                    show: true,
                    formatter: function(params) {
                        if (params.data) return params.name + '：' + params.data['value']
                    },
                },
                visualMap: {
                    // type: 'continuous',
                    // showLabel: true,
                    // left: '50',
                    min: sortdata.length>0?Math.min(...sortdata):0,
                    max: sortdata.length>0?Math.max(...sortdata):100,
                    // min: min,
		            // max: max,
		            left: '3%',
		            bottom: '5%',
		            calculable: true,
		            realtime: true,
		            seriesIndex: [0],
		            itemHeight: 70,
		            itemWidth: 10,
                    text: ['高', '低'],
		            inRange: {
		              color: ['#D4E9FD', '#7BB4FE', '#368DFF']
		            },
		            textStyle: {
		              color: '#6E6E6E'
		            },
                    splitNumber: 0
                },
                series: [{
                    name: 'MAP',
                    type: 'map',
                    mapType: name,
                    // data: this.allData,
                    data:seriesdata,
                    selectedMode: 'false', //是否允许选中多个区域
                    label: {
                        normal: {
                            show: true,
                            fontSize: 12,
                            color: 'rgb(22, 22, 22)', //省份标签字体颜色
                            // formatter: function(val) {
                            //     // console.log(val, 9999999999)
                            //     var area_content = '{a|' + val.name.substring(0,2) + '}' 
                            //     					// + '-' + 
                            //     					// '{b|' + val.value + '}';
                            //     return area_content.split("-").join("\n");
                            // }, //让series 中的文字进行换行
                            rich: {
                                a: {
                                    color: '#000'
                                },
                                b: {
                                    color: '#fff',
                                    fontFamily: 'Microsoft YaHei',
                                    fontSize: 14,
                                    width: 16,
                                    height: 16,
                                    borderRadius: 10,
                                    borderWidth: 1,
                                    // borderColor: '#f00',
                                    textAlign: 'center',
                                    padding: 2
                                }
                            }, //富文本样式,就是上面的formatter中'{a|'和'{b|'
                        },
                        emphasis: {
                            show: false
                        }
                    }, //地图中文字内容及样式控制
                    itemStyle: {
		              normal: {
		                areaColor: '#D8EBFD',
		                borderWidth: 0.5,
		                borderColor: '#fff',
		                color: '#D8EBFD'
		              },
		              emphasis: {
		                areaColor: '#0075FF',
		                borderWidth: 1,
		                borderColor: '#FFF',
		                shadowBlur: 3,
		                shadowOffsetx: -3,
		                shadowColor: '#0070fd',
		                label: {
		                  show: true,
		                  textStyle: {
		                    color: '#fff'
		                  }
		                }
		              }
		            },
		            select:{
		            	itemStyle:{
		            		normal: {
		            			show:false,
				                areaColor: '#D8EBFD',
				                borderWidth: 0.5,
				                borderColor: '#fff',
				                color: '#D8EBFD'
				            },
				            emphasis: {
				            	show:false,
				                areaColor: '#0075FF',
				                borderWidth: 1,
				                borderColor: '#FFF',
				                shadowBlur: 3,
				                shadowOffsetx: -3,
				                shadowColor: '#0070fd',
				                label: {
				                  show: true,
				                  textStyle: {
				                    color: '#fff'
				                  }
				                }
				            }
		            	},
		            },
                }]
            };
            this.myChart.setOption(option);
            this.myChart.resize()
            // curMap = {
            //     mapCode: mapCode,
            //     mapName: name
            // };
        } else {
            alert('无法加载该地图');
        }
	},
  }
}
</script>

<style lang="scss" scoped>
	.chartmap{
		flex: 1;
		.map{
			width: 100%;
			height: 500px;
		}
	}
</style>
