## 支持第三方服务的接口

### 扫码接口

假设第三方提供的接口链接为：`https://third-part.com/api`

```
post https://third-part.com/api

参数:
{
  "store_id": "门店ID",
  "code_info": "条形码值"
}

响应：
{
  "chart_data": {
    "title": "销售金额",
    "data": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
  },
  "tabs": [
    {
      "title": "概况",
      "data": [
        ["key", "value"]
      ]
    },
    {
      "title": "同区",
      "data": [
        ["key", "value"]
      ]
    }
  ]
}
```

### 字段明细

* 呼叫第三方 API 使用的 HTTP Method 为: **POST**
* 呼叫第三方 API 时会传两个参数: 门店ID、条形码值
* 第三方 API 的响应体
  * `Content-Type: application/json`
  * 响应体结构如上述简介，后面会描述业务需求

### API 响应体

扫描商品形码后用户看到结果界面两部分：

* 过去十五天的销售金额趋势图

  ```
  "chart_data": {
    "title": "销售金额",
    "data": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
  }
  ```

* 扫码商品信息及同区数据

  ```
  "tabs": [
    {
      "title": "概况",
      "data": [
        ["key", "value"]
      ]
    },
    {
      "title": "同区",
      "data": [
        ["key", "value"]
      ]
    }
  ]
  ```



