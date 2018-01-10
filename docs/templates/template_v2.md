# 模板二配置

## 功能控件

1. banner 标题
2. info 副标题
3. single_value 单值
4. bargraph 条状图
5. chart#v2 图（折线，柱）
6. tables#v4 新表格

## 已完成功能

1. 增加控件
2. 删除控件
3. 本地缓存
4. 弹框优化
5. 多套控件
6. 控件标识
7. 标题与tab绑定

## 使用提要

1. 将右侧的空间图标拖拽至左边的内容区域
2. 点击拖拽的空间进行填写编辑
3. 点击添加属性，可在弹出框表格中进行参数的动态添加
4. 填写完毕或者修改完毕，点击确定可在右侧json栏查看生成的json
5. 控件支持新增，修改以及删除

## 控件配置项

**banner**

```javascript
"type": "banner",
"config": {
  "title": "", //标题
  "table_name": "report_data_104_main”,  // 数据表
  "identifier": "grid1”,  //标识符
  "date": {
  	"column_name": "period" // 日期
  },
  "info": "说明<br/>1、毛利=销售-成本+促销扣款<br/>      //说明
  "subtitle": []
  }
}
     
```

**info**

```javascript
{
  "type": "info",
    "config": {
      "table_name": "report_data_104_main”,   //数据表
      "identifier": "sum”,   //标识符
      "subtitle": [
        {
        "column_name": "period”,  //值
        "label": //标签
      	},
        {
          "column_name": "mea_float1",
          "label": "   总分:",
          "precision": 0
        }
      ]
    }
}
```



**single_value**

```javascript
{
  "type": "single_value",
    "config": {
      "table_name": "report_data_104_main”,  //数据表
      "identifier": "single_value”,   //标识符
      "state": {
        "color": {
          "column_name": "dim2” //颜色
        }
      },
        "main_data": { //主值
          "label": "销售额”,    //标签
          "column_name": "mea_int1”,   //值
          "percentage": 1,
            "format": "float"
        },
          "sub_data": { //对比值
            "label": "对比数据”,   //标签
            "column_name": "mea_int2”,    //值
            "percentage": 1,
              "format": "float"
          }
    }
}
```



**bargraph**

```javascript
{
  "type": "bargraph",
    "config": {
      "table_name": "report_data_104_main”,  //数据表
      "identifier": "chart1”,   //标识符
      "series": { //数据列
        "name": "得分”,   //标签名
        "column_name": "mea_float1”,  //数值
        "percentage": 0
      },
        "x_axis": { //轴标签
          "name": "项目”,  //标签名
          "column_name": "dim_com” //数值
        },
          "order": { //排序
            "column_name": "dim_com”
          }
    }
}
```

**chart#v2**

```javascript
{
  "type": "chart#v2",
    "config": {
      "table_name": "report_data_031_main”,   //数据表
      "identifier": "chart1”,           //标识符
      "legend": [ //Y轴
        {
          "name": "本周”,  //名称
          "column_name": "mea_float1”,  //值
          "precision": 1,
          "type": "line" //类型
        },
        {
          "name": "上周”, //名称
          "column_name": "mea_float2”, //值
          "precision": 1,
          "type": "line"  //类型
        }
      ],
        "x_axis": { //X轴
          "column_name": "dim_com"
        },
          "unit": "万" //类型
    }
}
```



**tables#v4**

```javascript
{
  "type": "tables#v4",
    "config": [{
      "name": "", //表页签名
      "table_name": "report_data_104_main”,  //数据表
      "identifier": "grid1”,     //标识符
      "table1": [ //第一层
      {
      "head_name": "自身ID”,  //列名
      "column_name": "dim1” //值
    },
               {
                 "head_name": "父ID",
                 "column_name": "dim_com"
               },
               {
                 "head_name": "区域",
                 "column_name": "dim2"
               },
               {
                 "head_name": "面积",
                 "column_name": "mea_float1",
                 "precision": 1,
                 "plus_minus": [ //负数标红
                   "red"
                 ]
               },
               {
                 "head_name": "箱子数",
                 "column_name": "mea_int1"
               }
              ],
      "table2": [ //第二层
        {
          "head_name": "自身ID",
          "column_name": "dim1"
        },
        {
          "head_name": "父ID",
          "column_name": "dim_com"
        },
        {
          "head_name": "门店",
          "column_name": "dim2"
        },
        {
          "head_name": "面积",
          "column_name": "mea_float1",
          "precision": 1
        },
        {
          "head_name": "箱子数",
          "column_name": "mea_int1"
        }
      ],
        "table3": [ //第三层
          {
            "head_name": "自身ID",
            "column_name": "dim1"
          },
          {
            "head_name": "父ID",
            "column_name": "dim_com"
          },
          {
            "head_name": "商行",
            "column_name": "dim2"
          },
          {
            "head_name": "面积",
            "column_name": "mea_float1",
            "precision": 1
          },
          {
            "head_name": "箱子数",
            "column_name": "mea_int1"
          }
        ]
}]
}
```

## Tips

除了每个控件基本的数据配置下选项之外，一些控件会有可选值，如下

`precision`:   所属数据可能是负数的情况下 ，配置该项设置保留小数位数，

`percentage`:   所属数据可能是百分比的情况下 ，配置该项设置保留小数位数，

`plus_minus: ["red" `: 所属数据可能是负数的情况下 ，配置该项让负数标红