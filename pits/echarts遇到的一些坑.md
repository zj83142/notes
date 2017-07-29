## 记录echarts使用过程中遇到的那些坑

### - x轴数据是字符串日期格式且不连续时，间隔问题

> 刚开始遇到这个需求的时候感觉要从数据上处理，数据处理成连续的日期，没有的补0，后台人员说太后台补数据，后来才发现，前端果然强大，只有你想不到的，没有做不到的，默默的告诉自己，虽然我不是专业的前端开发，但是英雄不问出处，我可以通过努力和积累变得专业。**以后遇到问题切忌不可轻易言弃！！！！！！**

```
function getOption(x, y) {
    let option = {
      title : {
          text: '',
      },
      tooltip : {
          trigger: 'item',
          formatter : function (params) { // 格式化显示问题
              var date = new Date(params.value[0]);
              date = date.getFullYear() + '-'
                     + (date.getMonth() + 1) + '-'
                     + date.getDate() + ' '
              return "日期：" + date + "<br/>测试次数：" +  params.value[1];
          }
      },
      toolbox: {
          show : true,
          feature : {
              mark : {show: true},
              dataView : {show: true, readOnly: false},
              restore : {show: true},
              saveAsImage : {show: true}
          }
      },
      dataZoom: { // 显示缩放轴，且从70% 开始
          show: true,
          start : 70
      },
      legend : {
          data : ['series1']
      },
      xAxis : [
          {
              type : 'time', // 非常重要，设置x轴类型是time. (类型有：value, category, time, log)
              splitNumber:10,
              axisLabel: {
                  rotate: 30, // 标签旋转角度
                  interval:0, // 默认auto（自动隐藏显示不下的） 0： 全部显示
              }
          }
      ],
      yAxis : [
          {
              type : 'value',
              minInterval: 1,  
          }
      ],
      series : [
          {
            name: 'series1',
            type: 'line',
            showAllSymbol: true,
            smooth:true,
            areaStyle: {normal: {}},
            symbol: 'circle',
            symbolSize: 6,
            data: (function() {
              let data = []; // 这个是在react项目中，防止数据从有到无变化时会报错的问题,每次获取新数据的时候要提前清空数据
              if(x.length !== 0) {
                data = [];
                for(let i = 0; i < x.length; i ++) {
                  var date = new Date(Date.parse(x[i].replace(/-/g,   "/")));  
                  data.push([date, y[i]]); // 此处需要注意，data里面存放的是数组(时间，y轴数据-可以是多个)
                }
              }
              return data;
            })()
          }
      ]

    };
    return option;
  }
```

### - echart 柱状图显示数据 

series 下添加
```
label: {
    normal: {
        show: true,
        position: 'left' // 默认在柱状图上显示数据
    }
},
```