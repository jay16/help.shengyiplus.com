## 会议报表

### 服务器端配置档

```
[
  {
    "support_choices": 1, // 该页是否支持筛选项
    "title": { // 会议报表标题
      "table_name": "report_data_068_main", // 查询数据表字段三兄弟
      "identifier": "info", // where part_name = ${identifier}
      "column_name": "dim_com" // 使用该字段的值
    },
    "top_background": "#000", // 页面背景色
    "bottom_background": "#000",
    "parts": [ // 页面内的控件列表 >= 1 && <= 4
      {
        "type": "single_value",
        "title": "品类总指标达成率",
        "top_background": "#5ebcef",
        "bottom_background": "#000",
        "config": {
          "table_name": "report_data_068_main",
          "identifier": "single_value",
          "main_data": {
            "label": "实际销售",
            "column_name": "mea_float1",
            "prefix": "￥", // 前缀
            "suffix": "元", // 后缀
            "thousand": 1 // 是否需要千位位处理
          },
          "sub_data": {
            "label": "周指标",
            "column_name": "mea_float2",
            "prefix": "￥",
            "suffix": "元",
            "thousand": 1
          },
          "per_data": {
            "column_name": "mea_float3"
          }
        }
      },
      {
        "type": "info",
        "config": {
          "table_name": "report_data_068_main",
          "identifier": "info",
          "column_name": "dim_com"
        }
      },
      {
        "type": "chart",
        "title": "销售环比",
        "top_background": "#280DF5",
        "bottom_background": "#40557D",
        "config": {
          "table_name": "report_data_068_main",
          "identifier": "chart1",
          "legend": [
            {
              "name": "本周销售额",
              "column_name": "mea_float1",
              "type": "bar"
            },
            {
              "name": "上周销售额",
              "column_name": "mea_float2",
              "type": "bar"
            },
            {
              "name": "本周指标",
              "column_name": "mea_float3",
              "type": "line"
            }
          ],
          "unit1": "元",
          "unit2": "元",
          "x_axis": {
            "table_name": "report_data_068_main",
            "identifier": "chart1",
            "column_name": "dim1"
          }
        }
      },
      {
        "type": "pie",
        "title": "品类占比",
        "config": {
          "table_name": "report_data_068_main",
          "identifier": "chart2",
          "legend": [
            {
              "name": "dim_com",
              "column_name": "mea_float1",
              "type": "pie"
            }
          ]
        }
      }
    ]
  },
  {
    "title": {
      "table_name": "report_data_068_main",
      "identifier": "info",
      "column_name": "dim_com"
    },
    "table_name": "report_data_068_main",
    "identifier": "filter1",
    "filter_name": "dim_com",
    "parts": [
      {
        "type": "tables",
        "title": "店汇总情况",
        "config": [
          {
            "table_name": "report_data_068_main",
            "identifier": "grid1",
            "table1": [
              {
                "head_name": "自身ID",
                "column_name": "dim_com"
              },
              {
                "head_name": "父ID",
                "column_name": "dim2"
              },
              {
                "head_name": "门店名",
                "column_name": "dim_com",
                "sortable": 1 // 是否需要前端运行筛选 
              },
              {
                "head_name": "周品类指标",
                "column_name": "mea_float1",
                "sortable": 1
              },
              {
                "head_name": "周品类销售",
                "column_name": "mea_float2",
                "order_weight_column_name": "mea_float2", // 排序用的字码，若未配置此字段，则使用 column_name 作为默认值
                "sortable": 1
              },
              {
                "head_name": "达成率",
                "column_name": "dim1",
                "hyperlink": "dim2", // 支持超链接，取该字段内容作为链接
                "hyperlink_target": "_blank", // 超链接跳转模式，默认为 _blank
                "sortable": 0
              }
            ]
          }
        ]
      }
    ]
  }
]

```

### 组件分解

`` 组件

```
{
  "type": "info",
  "config": {
    "table_name": "report_data_068_main",
    "identifier": "info",
    "column_name": "dim_com"
  }
}

return

{
  "type": "text",
  "data": "<div style='overflow: hidden; vertical-align: middle; padding-left: 2rem; padding-right: 2rem;'><div style=' width: 20%; height: 25%; margin: 0 auto; margin-top: -5%; text-align: center; background: #f2f2f2; border-radius: 50%;'><img style=' padding-top: 30px; ' src='http://ui.ibi.ren/imgs/icon.png'></div><p style='text-indent: 2rem; line-height: 36px; text-align: justify;'>恭喜！本周总销售额达到<span style='color: red;'>16,840</span>元，较上周总销售额<span style='color: red;'>13,560</span>元，环比增长了<span style='color: #f0ad3e;'>19.6%</span>。其中<sapn style='color: #55a3ff;'>徐汇区</sapn>总销售额达到<span style='color: red;'>7,920</span>元,区域销售占比<span style='color: #f0ad3e;'>47%</span>；所有品类中，<span style='color: #55a3ff;'>面包</span>销售总额达到<span style='color: red;'>2,657</span>元,品类销售占比<span style='color: #f0ad3e;'>15.8%</span>。</p></div></div>"
}   
```

`single_value` 组件

```
{
  "type": "single_value",
  "title": "品类总指标达成率",
  "config": {
    "table_name": "report_data_068_main",
    "identifier": "single_value",
    "main_data": {
      "label": "实际销售",
      "column_name": "mea_float1"
    },
    "sub_data": {
      "label": "周指标",
      "column_name": "mea_float2"
    },
    "per_data": {
      "column_name": "mea_float3"
    }
  }
}

return

{
  "id": "report-01-01",
  "pages": [{
    "title": "页面的标题",
    "topBackground": "#5ebcef",
    "bottomBackground": "#000",
    "options": [
      {"label":"名称1", "value": "0"},
      {"label":"名称2", "value": "1"},
      {"label":"名称3", "value": "2"}
    ],
    "charts": [{
      "type": "singleValue",
      "title": "| 品类总指标达成率",
      "data": [
        { "percent": 94.7, "centerText": "94.7%", "leftTitle": "实际销售", "leftText": "￥3,979", "rightTitle": "周指标", "rightText": "￥4,200" }
      ]
    }]
  }]
}

```

`chart` 组件

```

{
  "type": "chart",
  "title": "销售环比",
  "config": {
    "table_name": "report_data_068_main",
    "identifier": "chart1",
    "legend1": [
      {
        "name": "本周销售额",
        "column_name": "mea_float1",
        "type": "bar"
      },
      {
        "name": "上周销售额",
        "column_name": "mea_float2",
        "type": "bar"
      }
    ],
    "legend2": [
      {
        "name": "本周指标",
        "column_name": "mea_float3",
        "type": "line"
      }
    ],
    "unit1": "元",
    "unit2": "元",
    "x_axis": {
      "column_name": "dim1"
    }
  }
}

return

{
  "type": "lineHistogram",
  "title": "| 销售环比",
  "data": [{
    "chartData": {
      "columns": ["日期", "上周总销售", "本周总销售", "销售环比"],
      "rows": [
        { "日期": "周一", "上周总销售": 2710, "本周总销售": 2700, "销售环比": 2670 },
        { "日期": "周二", "上周总销售": 2100, "本周总销售": 2400, "销售环比": 2490 },
        { "日期": "周三", "上周总销售": 2270, "本周总销售": 2400, "销售环比": 2400 },
        { "日期": "周四", "上周总销售": 2610, "本周总销售": 2700, "销售环比": 2490 },
        { "日期": "周五", "上周总销售": 2530, "本周总销售": 2700, "销售环比": 2558 },
        { "日期": "周六", "上周总销售": 2250, "本周总销售": 2100, "销售环比": 2070 },
        { "日期": "周日", "上周总销售": 2370, "本周总销售": 2400, "销售环比": 2190 }
      ]
    },
    "chartSettings": {
      "metrics": ["上周总销售", "本周总销售", "销售环比"],
      "showLine": ["销售环比"],
      "axisSite": {, # 双Y轴
         "right": ["指标完成率"]
       },
       "yAxisType": ["KMB", "percent"],
       "yAxisName": ["销售额", "指标完成率"]
    }
  }]
}
```

`pie` 组件

```
{
  "type": "pie",
  "title": "品类占比",
  "config": {
    "table_name": "report_data_068_main",
    "identifier": "chart2",
    "legend": {                           
      "key_column_name": "dim_com",
      "value_column_name": "mea_float1"
    }
  }
}

return

{
  "type": "pie",
  "title": "品类占比",
  "data": [{
    "chartData": {
      "columns": ["name", "value"],
      "rows": [
        { "name": "面包", "value": 8657 },
        { "name": "串串锅", "value": 5669 },
        { "name": "其他", "value": 3669 }
      ]
    },
    "chartSettings": {
      "dimension": "name",
      "metrics": "value",
      "dataType": "KMB" // 13444 => KMB: 13.44K  MB: 13444
    }
  }]
}
```

`tables` 组件

```
{
  "type": "tables",
  "title": "店汇总情况",
  "config": [
    {                   
      "table_name": "report_data_068_main",
      "identifier": "grid1",
      "table1": [
        {
          "head_name": "自身ID",
          "column_name": "dim_com",
          "order_weight_column_name": "dim_com"
        },
        {
          "head_name": "父ID",
          "column_name": "dim2",
          "order_weight_column_name": "dim2"
        },
        {
          "head_name": "门店名",
          "column_name": "dim_com",
          "order_weight_column_name": "dim_com"
        },
        {
          "head_name": "周品类指标",
          "column_name": "mea_float1",
          "order_weight_column_name": "mea_float1"
        },
        {
          "head_name": "周品类销售",
          "column_name": "mea_float2",
          "order_weight_column_name": "mea_float2"
        },
        {
          "head_name": "达成率",
          "column_name": "dim1",
          "order_weight_column_name": "dim1",
          "hyperlink":"dim2",
          "hyperlink_target":"_blank"
        }
      ]
    }
  ]
}

return

{
  "type": "table",
  "title": "周店汇总概况",
  "data": {
    "table": {
      "loadingText": "拼命加载中"
    },
    "column": [
      { "id": 0, "property": "index", "label": "序号", "sortable": true },
      { "id": 1, "property": "name", "label": "门店", "sortable": true },
      { "id": 5, "property": "weekindex", "label": "周指标", "sortable": true },
      { "id": 7, "property": "ringRatio", "label": "品类环比", "sortable": true }
    ],
    "data": [
      [
        {"index": 1, "index-order-weight": 1, "name": "安永店", "name-order-weight": "1", "weekindex": 5800, "weekindex-order-weight": 5800, "ringRatio": "45.6%", "ringRatio-order-weight": "45.6"},
        {"index": 2, "index-order-weight": 2, "name": "佳惠店", "name-order-weight": "2", "weekindex": 5800, "weekindex-order-weight": 5800, "ringRatio": "100.5%", "ringRatio-order-weight": "100.5"},
        {"index": 3, "index-order-weight": 3, "name": "光明店", "name-order-weight": "3", "weekindex": 1400, "weekindex-order-weight": 5800, "ringRatio": "92%", "ringRatio-order-weight": "92"}
      ]
    ],
    "pagination": {}
  }
}
```
