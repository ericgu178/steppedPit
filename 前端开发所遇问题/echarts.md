# Echarts 百度开源图表

### 常用样式的记录

1. **axisLine** 代表轴线 里面的属性show 设置为false 为不显示轴线 其他样式具体官方查阅
2. **splitLine** 设置 网格虚线 实线 线之间宽度或者其他样式 
3. **legend** 是图例      
4. **areaStyle** 可以设置折线或者其他拥有其属性的样式 具体查阅官方文档
5. 饼图中的 **hoverAnimation** 悬停无动作 设置为false 即可
6. **color** <font size=4 color='skyblue'>最外层属性 调色盘颜色列表 图例颜色可从这里修改</font>
7. 调整图标位置一般使用grid 饼图 用radius和center

```js
yAxis: [{
    	    show:false, // y 轴 不显示
            type: 'value',
            name: '单位（%）',
            axisTick: { // 刻度线不显示
                show: false
            },
            axisLine: {show:false},//轴线不显示
            axisLabel: {
                margin: 10,
                textStyle: {
                    fontSize: 14
                }
            },
            splitLine: { // 网格虚线 实线 线之间宽度
                lineStyle: {
                    color: ['#216'],
                    width: 0.5,
                    type:'dashed'
                }
            }
        }],
```

## 折线图和饼图的例子

```js
window.xxx = function ()
{
    var myChart = echarts.init(document.getElementById(''));
    var option = {
        color:['#c23531','#2f4554', '#61a0a8', '#d48265', '#91c7ae','#749f83',  '#ca8622', '#bda29a','#6e7074', '#546570', '#c4ccd3'], // 调色盘颜色列表。如果系列没有设置颜色，则会依次循环从该列表中取颜色作为系列颜色。
        backgroundColor: '#fff',
        title: {
            text: '',
            textStyle: {
                fontWeight: 'bold',
                fontSize: 16,
                color: '#000'
            },
            left: '6%',
            padding:[10,0,0,380]
        },
        tooltip: {
            trigger: 'axis',
            axisPointer: {
                lineStyle: {
                    color: '#57617B'
                }
            }
        },
        legend: {
            icon: 'rect', // 改变图例样式
            itemWidth: 14,
            itemHeight: 5,
            itemGap: 13,
            data: ['移动', '电信', '联通'],
            right: '4%',
            textStyle: {
                fontSize: 12,
                color: '#292f39'
            }
        },
        grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
        },
        // 横轴
        xAxis: [{
            type: 'category',
            boundaryGap: false,
            axisLine: {show:false},//不显示轴线
            // x轴数据
            data: ['13:00', '13:05', '13:10', '13:15', '13:20', '13:25', '13:30', '13:35']
        }],
        // 竖轴
        yAxis: [{
            type: 'value',
            name: '（万元）',
            axisTick: {
                show: false
            },
            axisLine: {show:false},//轴线不显示
            axisLabel: {
                margin: 10,
                textStyle: {
                    fontSize: 14
                }
            },
            // 网格线
            splitLine: { 
                lineStyle: {
                    color: ['#216'],
                    width: 0.5,
                    type:'dashed'
                }
            }
        }],
        // 数据写入
        series: [{
            name: '移动',
            type: 'line',
            smooth: true,
            symbol: 'circle', // 控制什么样式的 点状物 线上的点
            symbolSize: 5, // 大小点状物
            showSymbol: false, //是否显示点状物
            lineStyle: { // 折线的线样式设置
                normal: {
                    width: 3
                }
            },
            areaStyle: { //折线的区域设置
                normal: {
                    // color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [{
                    //     offset: 0,
                    //     color: 'rgba(16,97,204, 0.3)'
                    // }, {
                    //     offset: 0.8,
                    //     color: 'rgba(17,235,210, 0)'
                    // }], false),
                    shadowColor: 'rgba(0, 0, 0, 0.5)',
                    shadowBlur: 10
                }
            },
            data: [220, 182, 191, 134, 150, 120, 110, 125 ]
        }]
    };
    myChart.setOption(option);
}


window.yyy = function () 
{
    var myChart = echarts.init(document.getElementById('circle_klfx'));
    var m2R2Data= [
               {value:40, legendname:'种类01',name:"1年  40%",itemStyle:{color:"#8d7fec"}},
               {value:10, legendname:'种类02',name:"种类02  310",itemStyle:{color:"#5085f2"}},
               {value:20, legendname:'种类03',name:"种类03  234",itemStyle:{color:"#e75fc3"}},
               {value:10, legendname:'种类04',name:"种类04  154",itemStyle:{color:"#f87be2"}},
               {value:20, legendname:'种类05',name:"种类05  335",itemStyle:{color:"#f2719a"}},
            ];


    var option = {
           title: {
               text: '',
               textStyle: {
                   fontSize: 16,
                   color: "black"
               },
               left: "2%",
               padding:[10,0,0,200]
           },
           tooltip: {
               trigger: 'item',
               formatter:function (parms){
                 var str=  parms.seriesName+"</br>"+
                   parms.marker+""+parms.data.legendname+"</br>"+
                   "数量："+ parms.data.value+"</br>"+
                   "占比："+ parms.percent+"%";
                   return  str ;
               }
           },
           legend: {
                icon:'circle',
               type:"scroll",
               orient: 'vertical',
               left:'70%',
               align:'left',
               top:'middle',
               textStyle: {
                   color:'#8C8C8C'
               },
               color:"#fff",
               height:250
           },
           series: [
                {
                    name:'标题',
                    type:'pie',
                    center: ['35%', '50%'], // 大小设置
                    radius: ['60%', '35%'], // 范围调整 扩大 缩小？？
                    clockwise: false, //饼图的扇区是否是顺时针排布
                    avoidLabelOverlap: false,
                    label: {
                        normal: {
                            show: false,
                        }
                    },
                    hoverAnimation:false, // 悬停无动作
                    itemStyle: { 
                        normal: {
                            // 设置扇形的阴影
                            shadowBlur: 10,
                            shadowColor: 'rgba(0,0,0,0.5)', 
                            shadowOffsetX: -5,
                            shadowOffsetY: 5
                        },
                    },
                    data:m2R2Data
                }
           ]
        };
    
    myChart.setOption(option);
}
```

### 柱状图例子

```js
var myCharts = echarts.init(document.getElementById('bili'))
                var series = []; //数据
                var color = []; // 给下面的调色盘赋值
                var lengdedData = [{
                                name :'在库',
                                textStyle: {
                                    color: 'rgba(151,178,255,1)'
                                }
                            },{
                                name :'使用',
                                textStyle: {
                                    color: 'rgba(120,0,255,1)'
                                }
                            },{
                                name :'维修',
                                textStyle: {
                                    color: 'rgba(255,18,118,1)',
                                }
                            }];
                lengdedData.filter(res=>{
                    color.push(res.textStyle.color)
                })
                var ii = [{
                    title:'2018年1月12日资产购入',
                    name:'在库',
                    color:'rgba(151,178,255,1)',
                    bili:20,
                },{
                    title:'2018年2月20日盘点正常',
                    name:'使用',
                    color:'rgba(120,0,255,1)',
                    bili:10,
                },{
                    title:'2018年1月12日资产购入',
                    name:'在库',
                    color:'rgba(151,178,255,1)',
                    bili:40,
                },{
                    title:'2018年1月22日资产使用',
                    name:'维修',
                    color:'rgba(255,18,118,1)',
                    bili:30,
                },{
                    title:'2018年1月22日资产使用',
                    name:'维修',
                    color:'rgba(255,18,118,1)',
                    bili:30,
                },{
                    title:'2018年1月12日资产购入',
                    name:'在库',
                    color:'rgba(151,178,255,1)',
                    bili:40,
                }];
                ii.filter(res=>{
                    series.push({
                        name:res.name,
                        type:'bar',
                        barWidth:50,
                        stack: '统计',
                        label: {
                            normal: {
                                borderWidth: 20,
                                distance: 30,
                                align: 'center',
                                verticalAlign: 'middle',
                                borderRadius: 1,
                                borderColor: res.color,
                                backgroundColor: res.color,
                                show: true,
                                position: 'bottom',
                                formatter: res.title, // 格式显示的内容
                                color: '#000',
                                // formatter: (params)=>{
                                //     console.log(params)
                                // }
                            }
                        },
                        // itemStyle: {
                        //     // borderWidth: 1000,
                        //     color: res.color
                        // },
                        data:[{
                            value:res.bili,
                            itemStyle: {
                                normal: {
                                    color: {
                                        type: 'bar',
                                        colorStops: [{
                                            offset: 0,
                                            color: res.color // 0% 处的颜色
                                        }, {
                                            offset: 1,
                                            color: res.color // 100% 处的颜色
                                        }],
                                        globalCoord: false, // 缺省为 false
                                    }
                                }
                            }
                        }]
                    })
                })
                var option = {
                    color:color, // 调色盘颜色列表
                    backgroundColor:'rgba(244,247,252,1)',
                    title:{
                        show: false,
                        text:'',
                        textStyle:{
                          color:'#FFFFFF'  
                        },
                        left:'300',
                        top: '300'
                    },
                    
                    legend: {
                        icon: "circle", 
                        bottom: '43%',
                        left:'10%',
                        itemWidth: 7,
                        itemHeight: 7,
                        itemGap: 40,
                        textStyle:{
                            fontSize:'18'
                        },
                        data:lengdedData,
                        padding:[0,50,-100,760]
                    },
                    xAxis: [{
                        type :'value',
                        axisTick: {
                            show: false,
                        },
                        axisLine: {
                            show: false,
                        },
                        axisLabel: {
                            show: false
                        },
                        splitLine: {
                            show: false,
                        }
                    }],
                    yAxis: [{
                        //type: 'category',
                        data: [''],
                        axisTick: {
                            show: false,
                        },
                        axisLine: {
                            show: false,
                        },
                        axisLabel: {
                            textStyle: {
                                color: '#fff',
                            }
                        }
                    }],
                    series: series
                }
                myCharts.setOption(option)
```

## 调整图表位置 

```
grid: [{
       left: '10%',
       bottom: '10%',
       top: '10%',
       right: '10%'
   }],
   
 饼图 用
 radius : [ '30%', '50%' ],//内外圆的大小
center : [ '45%', '60%' ],//距离左右，上下距离的百分比

```

## 调整图例文字样式

```js
axisLabel: {
            interval: 0,
            margin:10,
            inside: true,
            show: true,
            formatter : function(v) {
            	return "{title|-----------------} {value|" + (v) +"}"
            },
            textStyle:{
            	rich:{
            		title: {
                        fontSize: 16,
                        lineHeight: 15,
                        color: "rgb(1, 137, 219)"
                    },
                    value: {
                        fontSize: 14,
                        lineHeight: 20,
                        color: "#fff"
                    }
            	}
            }
        },
```



## 证书过期怎么办 acme 更新证书

`acme --renew -d example.com --force --ecc` 这里我把acme 做成命令 放入~/.bashrc 文件里 指向 acme.sh

<font size=4 color="red">如果报错说端口被占用 把对应的端口关闭即可 开着 nginx 的 有可能 nginx 占用了</font>

`nginx -s stop` 关掉

`lsof -i :80` 查看占用端口 kill 掉相应进程

**自动更新**

```shell
# shell 脚本文件
certificate=("example.com") # 数组
# 关闭占用的80端口
nginx -s stop
date=$(date +%Y-%m-%d)
echo "证书开始更新时间 ${date}" >> ~/certificate.log
for url in ${certificate[@]};do
# 更新证书
    /acme.sh --renew -d $url --force --ecc >> ~/certificate.log
done
date=$(date +%Y-%m-%d)
echo "证书结束更新时间 ${date}" >> ~/certificate.log
# 开启80端口
nginx
```

```shell
crontab -e
01 01 01 * * sh ~/x.sh # 每月一日凌晨一点一分 执行脚本文件
```

