## 会议报表

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