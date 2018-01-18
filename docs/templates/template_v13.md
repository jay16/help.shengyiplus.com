# PC 报表

## 配置须知

当前报表模板支持筛选项，图表，表格及其二层下钻，报表通过`baseUrl + repCode=REP_000006&dateSourceCode=&reportId=` 来获取配置 json

访问示例：[链接摸我](http://ui.ibi.ren/pc-report-template/index.html#/?reportId=1)

## 配置要点

1. 最外层 title 为该报表的名称，图表中的 title 则为子标题
2. 筛选条件或者图表的接口参数取决于 linkage 和 params 两项，通过 linkage 项来判断该项是否需要多余参数，通过遍历 params 项的 key 来充当接口参数的 key
3. 表格 target 参数 用来判断该表格是否需要下钻，需要，则将值配置为对应列的索引，不需要则配置为 `null`
4. 图表项可根据不同图表类型来配置 setting 项，比如双 Y 轴，两柱一线等
5. 未完待续 ···

## 配置示例

```json
{
  "title": "帐龄分析",
  "config": [{
      "type": "choice",
      "config": [{
          "label": "选择省",
          "REP": "REP_000025",
          "model": "choiceProvince",
          "props": "pname",
          "list": "provinceList",
          "linkage": [],
          "params": {},
          "type": "choice"
        },
        {
          "label": "选择市",
          "REP": "REP_000026",
          "model": "choiceCity",
          "props": "cname",
          "linkage": ["pname"],
          "params": {
            "pname": ""
          },
          "list": "cityList",
          "type": "choice"
        },
        {
          "label": "选择时间",
          "REP": "REP_000027",
          "model": "choiceMonth",
          "props": "cat1_name",
          "linkage": [],
          "params": {},
          "list": "monthList",
          "type": "choice"
        }
      ]
    },
    {
      "type": "table",
      "config": [{
        "title": "欠款帐龄项目汇总表",
        "REP": "REP_000028",
        "table": [],
        "linkage": ["pname", "cname", "cat1_name"],
        "params": {
          "pname": "",
          "cname": "",
          "cat1_name": ""
        },
        "loading": "loading",
        "type": "table",
        "target": 0,
        "columns": [{
            "label": "项目名称",
            "property": "store_name"
          },
          {
            "label": "欠款时间",
            "property": "time_value"
          }, {
            "label": "水费",
            "property": "sf_default_money"
          },
          {
            "label": "电费",
            "property": "df_default_money"
          }, {
            "label": "排水费",
            "property": "psf_default_money"
          },
          {
            "label": "管理费",
            "property": "glf_default_money"
          }, {
            "label": "专项维修基金",
            "property": "zxwx_default_money"
          }
        ]
      }]
    }
  ]
}
```

