## 开发规范

- REP/SQL/PRO 编码一致性维护
- PRO 存储过程方法体维护在超管后台中（定时任务会批量同步至各子库）
- 新增、修改、删除的操作都应该使用 POST 方式（必须无结果集响应），其他使用 GET 方式
- 查询功能的需求可以使用 SQL 满足时不建议使用存储过程
- 存储过程名称规范(避免命名冲突): `pro_{report_id}_{action}_{bussiness}` 比如 `pro_report001_query_provinces`
- 端口分配：（所有项目环境统一下述配置）
    - 8081 REP 接口
    - 8082 SaaS 运营后台
    - 8083 普通管理后台
    - 8084 图片查看服务
    - 8085 图片上传接口

## SaaS 图片服务

假设 nginx 映射路径：`/root/www/saas_images`

图片位置

```
/root/www/saas_images/saas_images/world/hello/4d529d62-d239-4c8d-840e-bb7c58349513.png
```

以生意+ SaaS 服务来说明：

```
# 使用 IP + 端口访问图片，不需要指明根目录，使用相对路径
# http://47.96.170.148:8084 + application_code + module_name + 图片名称
http://47.96.170.148:8084/world/hello/4d529d62-d239-4c8d-840e-bb7c58349513.png

# 使用域名访问图片，需要根据根目录
# http://shengyiplus.idata.mobi/saas_images/ + application_code + module_name + 图片名称
http://shengyiplus.idata.mobi/saas_images/world/hello/4d529d62-d239-4c8d-840e-bb7c58349513.png
https://shengyiplus.idata.mobi/saas_images/world/hello/4d529d62-d239-4c8d-840e-bb7c58349513.png
```

具体图片上传、图片预览功能的实现请阅读示例源码：[ajax 示例](upload-images-api-examples/ajax.html.md)、[vue 示例](upload-images-api-examples/vue.md)
  
## REP 接口开发

### POST 方式

#### 原则

- 新增、修改、删除的操作都应该使用 POST 方式
- 接口调用完成后，**不需要有结果集返回**（指的是 SQL 脚本或者存储过程执行完后，不需要有结果集返回前端）

#### 参数的传输

- 必须是放在请求的 body 中
- 必须不可以拼接在 URL 后面
- 参数的命名要避开敏感字段："master", "truncate", "insert", "select", "delete", "update", "declare", "alert", "create", "drop"
- 必须的两个参数：repCode、dataSourceCode

接口访问格式:

```
POST http://server-aip:8081/saas-api/api/portal/custom
params:
{
  repCode: REP 编码,
  dataSourceCode: 数据源 ID,
  ...(业务字段)
}
```

#### 执行 INSERT 操作实例

SQL 代码：

```
INSERT INTO `user`
(`account`, `name`, `roleId`, `pass`, `jobNumber`, `storeNumber`, `createTime`, `status`, `created_at`, `updated_at`)
VALUES
('#account#', '#name#', '#roleId#', '#pass#', '#jobNumber#', '#storeNumber#', NOW(), '#status#', NOW(), NOW());
```

后台配置示例：

![图一](/assets/images/培训文档-配置 REP 接口/REP配置-POST方式-INSERT 实例1.png)

字段解释:

- SQL 脚本中的参数必须是以单引号以及 `#` 组合：`#参数一#`、 `#参数二#`
- SQL 脚本中的参数名称要与**“执行参数”**输入框中的参数名称一致，并且数量也必须一致
- 在图形化页面中操作时候，“执行参数”输入框中：参数之间以 `@@` 符号隔开，如图所示

#### REP 接口配置

点击“执行 SQL”单选框，找到需要执行的 SQL,如图二

![图一](/assets/images/培训文档-配置 REP 接口/REP配置-POST方式-INSERT 实例2.png)

#### REP 接口调用

针对上述配置 POST 实例的 REP 接口调用示例：

```
POST http://server-aip:8081/saas-api/api/portal/custom
Params:
{
  repCode: REP_002018,
  dataSourceCode: DATA_00001, // 根据业务需要在针对数据源中操作
  account: account,
  name: name,
  roleId: roleId,
  pass: pass,
  jobNumber: jobNumber,
  storeNumber: storeNumber,
  status: status
}
Reponse:
{
  msg: "请求数据成功!",
  code: 0, // 0 表示接口调用成功；1 表示接口调用失败
  data: []
}
```

以上就是 REP POST 方式执行 SQL 脚本的标准配置：

- 注意 REP 执行后，data 中是没有结果集返回的
- URL 上不能传参数，否则将无法获取到参数

### GET 方式

#### 原则

没有原则，**只要响应结果集**就应该使用 GET 方式。

#### 参数的传输

- 拼接在 URL 后面
- 参数的命名要避开敏感字段："master", "truncate", "insert", "select", "delete", "update", "declare", "alert", "create", "drop"
- 必须的两个参数：repCode、dataSourceCode

接口访问格式:

```
GET http://server-aip:8081/saas-api/api/portal/custom?repCode=REP编码&dataSourceCode=数据源ID&业务字段1=业务数据1
```

#### 执行 SELECT 操作实例

SQL 代码：

```
select 
  title
  , report_id
  , template_id
  , content
  , remark
  , created_at
  , updated_at
from sys_template_reports
where report_id = #reportId#;
```

后台配置示例：

![图一](/assets/images/培训文档-配置 REP 接口/REP配置-GET方式-SELECT 实例1.png)

字段解释:

- SQL 脚本中的参数必须是以单引号以及 `#` 组合：`#参数一#`、 `#参数二#`
- SQL 脚本中的参数名称要与**“执行参数”**输入框中的参数名称一致，并且数量也必须一致
- 在图形化页面中操作时候，“执行参数”输入框中：参数之间以 `@@` 符号隔开，如图所示

#### REP 接口配置

点击“执行 SQL”单选框，找到需要执行的 SQL，如图所示

![图一](/assets/images/培训文档-配置 REP 接口/REP配置-GET方式-SELECT 实例2.png)

#### REP 接口调用

针对上述配置 GET 实例的 REP 接口调用示例：

```
GET http://server-ip:8081/saas-api/api/portal/custom?repCode=REP_000058&dateSourceCode=DATA_000001&reportId=68
Reponse:
{
  "msg": "请求数据成功!",
  "code": 0,
  "data": [
    {
      "updated_at": "2017-12-27 19:48:21",
      "report_id": 68,
      "created_at": "2017-12-27 19:48:21",
      "template_id": 11,
      "remark": "2017W52(12.24-12.30)",
      "title": "区域 面包 周会（52周）"
    }
  ]
}
```

## SQL 基本知识

主库中定义存储过程的示例：

```
drop procedure if exists pro_sales_params_p0;
delimiter ;;
create procedure `pro_sales_params_p0`(in var_name varchar(255) charset utf8)
begin
    -- 业务 SQL 脚本，开始
    select distinct time_value as `value` from kpi_sales_analysis
    where time_type = 'month';
    -- 业务 SQL 脚本，结束
end
;;
delimiter ;
```

注意事项：

1. 创建存储过程时必须遵从上述格式
2. 业务 SQL 脚本可自定义
3. 参数中传入字符串时需要设置字符集 `charset utf8`
4. 查看存储过程方法体的定义：

  ```
  show create procedure pro_sales_params_p0;
  ```
5. 参数类型一致性，若数据库字段为 `datetime` 型，请按指定格式化数据传参

### REP 接口调试

调用 REP 接口时会出现各种可能的报错，服务器端已存储报错信息在数据库，并把对应行的 UUID 返回，可以调用调用该接口查看报表明细：（其实也是在主库配置的 REP 接口）

```
GET http://server-ip:8081/saas-api/api/portal/custom?dataSourceCode=DATA_000000&repCode=REP_000003&uuid=55b1d5ad74a64d84affeb2decbf69c36
```

参数列表：

- dataSourceCode, 主库, 固定值
- repCode, 固定值 REP_000003
- uuid: 接口响应的报错行 uuid

## 图片服务器

```
POST http://server-ip:8085/api/v1.1/saas/upload/images?api_token=0b7171311a17956a8025aff1f1ead15a

Params: 
{
  application_code: "world", // 数据源，对应数据源库 dataSrouceCode
  module_name: "hello",业务模块路径，可自定义，比如 user/gravatar
  image0:  图片一 multipart/form-data  
  image1:  图片一 multipart/form-data
  imagen:  图片一 multipart/form-data
}

Response: 
{
    "message": "上传成功",
    "data": [
        "dca27bad-b42d-49c2-ae73-a777a83eb691.png",
        "4d529d62-d239-4c8d-840e-bb7c58349513.png"
    ],
    "code": 201
}
```

- 链接公式：http://server-ip:8084 + application_code + module_name + image_name
- 链接示例：http://server-ip:8084/world/hello/4d529d62-d239-4c8d-840e-bb7c58349513.png


## 自定义开发三步曲：

### 1. 通过账号获取数据源  

传参列表：

- repCode=REP_000000, 固定值
- dataSourceCode=DATA_000000, 固定值
- user_num=wxmall01001
- password=94c5c735437438b5bcb6e7b8a8de1867

### 2. 在指定数据源中验证用户信息

传参列表：

- repCode=REP_000001, 固定值
- dataSourceCode=DATA_000001, 接口 1 中获取的 datasource_id
- user_num=wxmall01001
- password=94c5c735437438b5bcb6e7b8a8de1867

### 3. 在指定数据源中使用用户权限进行业务操作

传参列表：（根据实际场景调整）

- dataSourceCode=DATA_000001, 接口 1 中获取的 datasource_id
- repCode=业务 REP 值
- 业务字段1=
- 业务字段2=
- 业务字段3=
- ...