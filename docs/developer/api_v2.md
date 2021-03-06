## `API#V2` 接口

### API V1.1/V2 的区别：

* `API#V1.1` 生意人应用内部使用的接口，由于业务特殊性而无法给外部通用
* `API#V2` 生意人应用给第三方使用的通用接口，单一性强，为了达到某个业务场景的需求可以组合配合使用多个接口

### 接口列表

待实现、已实现的接口都在列表中，接口是否实现以该接口说明段落中的状态为主。

- 用户
    - 创建用户
    - 获取用户
    - 更新用户（基本信息，不关联权限逻辑）
    - 获取用户列表
    - 更新用户冻结状态
    - 更新用户登录密码
    - 重置用户登录密码

- 角色
    - 创建角色
    - 获取角色 *
    - 更新角色
    - 获取角色列表
    - 更新用户角色权限
    - 获取用户角色权限列表

- 群组
    - 创建群组
    - 获取群组
    - 更新群组
    - 更新群组权限
    - 获取群组列表
    - 获取用户群组权限列表
    - 更新用户群组权限列表

- 生意概况（仪表盘）
    - 创建仪表盘
    - 获取仪表盘
    - 更新仪表盘
    - 获取仪表盘列表
    - 获取仪表盘的角色权限列表
    - 更新仪表盘的角色权限列表
    - 获取角色的仪表盘权限列表
    - 更新角色的仪表盘权限列表

- 报表（分析）
    - 创建分析
    - 获取分析
    - 更新分析
    - 获取分析列表
    - 获取分析的角色权限列表
    - 更新分析的角色权限列表
    - 获取角色的分析权限列表
    - 更新角色的分析权限列表

- 专题（应用）
    - 创建应用
    - 获取应用 *
    - 更新应用 *
    - 获取应用列表
    - 获取应用的角色权限列表
    - 获取角色的应用权限列表
    - 更新应用的角色权限列表
    - 获取角色的应用权限列表
    - 更新角色的应用权限列表

- 工具箱
    - 获取工具箱列表
    - 获取工具箱的角色列表
    - 获取角色的工具箱列表
    - 更新工具箱的角色列表
    - 更新角色的工具箱列表

- 其他
    - 回调函数呼叫记录
    - 经纬度转地址
    - 回调函数呼叫记录

### 接口交互信息说明

代码的实现理念涉及 [HTTP 状态码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)、[RESTful API](http://www.csdn.net/article/2013-06-13/2815744-RESTful-API)，有实现不合理的地方欢迎交流讨论，确定问题后会第一时间修正。

* API 验证参数 `api_token`

  * 必填项，否则响应无权限 401
  * api_token = MD5 加密 `{key}{api_path}{key}`
  * 请联系开发人员申请key

* CRUD 分别对应的 HTTP 状态码

  * 无权限时响应 401
  * 创建、更新成功时响应 201
  * 获取数据成功时响应 200

* CRUD 分别对应的 HTTP 操作

  * 获取时 Get
  * 创建、更新、删除时 Post

* 接口响应字段

  * 创建时 \[code, message, data\]
  * 更新时 \[code, message, data\]
  * 获取时 \[code, message, data\]
  * 删除时 \[code, message\]

* 分页说明：起始页码为 0

### 接口交互

#### 用户

##### 创建用户

```
post /api/v2/user

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  user: {
    user_name: "必填，用户名称",
    user_num: "必填，用户编号，确保全表唯一",
    user_pass: "必填，登录密码，MD5 加密",
    email: "可选，邮箱",
    mobile: "可选，联系方式",
    tel: "可选，手机号",
    join_date: "可选，入职日期，格式 yyyy-mm-dd HH:MM:SS",
    position: "可选，职位"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 用户信息
    id: "数据表标识ID",
    user_name: "用户名称",
    user_num: "用户编号，确保全表唯一",
    user_pass: "登录密码，MD5 加密",
    email: "邮箱",
    mobile: "联系方式",
    tel: "手机号",
    join_date: "入职日期，格式 yyyy-mm-dd HH:MM:SS",
    position: "职位"
  }
}
```

##### 获取用户

```
get /api/v2/user/:user_num

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  id: "99901",
  gravatar: "",
  active_flag: "1",
  group_name": "大区(全部)商行(全部)",
  user_name": "李俊杰",
  join_date: "",
  login_duration: "2",
  dept_name: "胜因团队",
  mobile: "13564379606",
  role_id: "99",
  ...
}
```

##### 更新用户（基本信息，不关联权限逻辑）

```
post /api/v2/user/:user_num

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
  user: {
    user_name: "可选，用户名称",
    user_num: "可选，用户编号，确保全表唯一",
    email: "可选，邮箱",
    mobile: "可选，联系方式",
    tel: "可选，手机号",
    join_date: "可选，入职日期，格式 yyyy-mm-dd HH:MM:SS",
    position: "可选，职位"
  }
}

response:
{
  code: 201(成功)/200(失败原因)
  message: 成功提示/失败原因,
  data: { // 更新后的用户信息
    id: "数据表标识ID",
    user_name: "用户名称",
    user_num: "用户编号，确保全表唯一",
    user_pass: "登录密码，MD5 加密",
    email: "邮箱",
    mobile: "联系方式",
    tel: "手机号",
    join_date: "入职日期，格式 yyyy-mm-dd HH:MM:SS",
    position: "职位"
  }
}
```

##### 删除用户

```
post /api/v2/delete/user

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  user_num: "用户编号"
}

response:
{
  code: 201(成功)/200(失败原因)
  message: 删除成功
}
```

##### 获取用户列表

```
get /api/v2/users

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  page: "可选，第几页，默认 0",
  page_size: "可选，每页数据项数量，默认 15",
  user_num: "可选，筛选项，赋值时具有过滤功能",
  user_name: "可选，筛选项，赋值时具有过滤功能"
}

response:
{
  code: 200(成功)/401(接口权限验证失败)
  message: 成功提示/失败原因,
  current_page: 0, // 当前页码
  page_size: 15, // 每页数量
  total_page: 1, // 页数
  total_count: 15, // 总数量
  data: [ // 用户列表
    {
      id: "数据表标识ID",
      user_name: "用户名称",
      user_num: "用户编号，确保全表唯一",
      user_pass: "登录密码，MD5 加密",
      status: "冻结状态, true/false(过期)",
      email: "邮箱",
      mobile: "联系方式",
      tel: "手机号",
      join_date: "入职日期，格式 yyyy-mm-dd HH:MM:SS",
      position: "职位"
    }
  ]
}
```

#### 更新用户冻结状态(过期)

```
post /api/v2/user/:user_num/status

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  status: 1(正常)/0(冻结)
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 更新用户登录密码

```
post /api/v2/user/:user_num/password

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  password: MD5 加密 32 位字符串
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 重置用户登录密码

```
post /api/v2/user/:user_num/reset_password

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  mobile: "必填项，与用户编号关联的手机号，要求业务数据维护"
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

重置登录密码的操作：随机六位数字更新为用户登录密码，然后通过手机短信的形式送达至参数中的手机号。

**系统会根据配置回调第三方更新密码接口，请第三方更新密码接口特殊处理，勿再回调本系统更新密码接口，避免进入接口回调死循环**。

#### 刷新用户缓存

```
post /api/v2/refresh/user_cache

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  user_nums: "必填项，要重置的用户编号，多个以逗号分隔"
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  data: {
      success: [刷新成功的用户编号列表],
      failed: [刷新失败的用户编号列表]
  }
}
```

#### 角色

##### 创建角色

```
post /api/v2/role

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role: {
    role_name: "必填，角色名称",
    "memo": "可选，备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因)
  message: 成功提示/失败原因,
  data: { // 角色信息
    id: "数据表标识ID",
    role_id: "角色ID",
    role_name: "角色名称",
    "memo": "备注说明"
  }
}
```

##### 获取角色

```
get /api/v2/role/:role_id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role_id: 角色ID
}

response:
{
  code: 200(成功)/其他(失败原因)
  message: 成功提示/失败原因,
  data: { // 角色信息
    id: "数据表标识ID",
    role_id: "角色ID",
    role_name: "角色名称",
    "memo": "备注说明"
  }
}
```

##### 更新角色

```
post /api/v2/role/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role: {
    role_name: "角色名称",
    "memo": "备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 更新后的角色信息
    id: "数据表标识ID",
    role_id: "角色ID",
    role_name: "角色名称",
    "memo": "备注说明
  }
}
```

##### 获取角色列表

```
get /api/v2/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  page: "可选，第几页，默认 0",
  page_size: "可选，每页数据项数量，默认 15",
  role_name: "可选，筛选项，赋值时具有过滤功能"
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  current_page: 0, // 当前页码
  page_size: 15, // 每页数量
  total_page: 1, // 页数
  total_count: 15, // 总数量
  data: [ // 角色列表
    {
      id: "数据表标识ID",
      role_id: "角色ID",
      role_name: "角色名称",
      "memo": "备注说明"
    }
  ]
}
```

#### 获取用户角色权限列表

```
get /api/v2/user/:user_num/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  lazy_load: 加载所有角色（默认可不传该参数）
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  current_page: 0, // 当前页码
  page_size: 15, // 每页数量
  total_page: 1, // 页数
  total_count: 15, // 总数量
  data: [
    {
      id: 99,
      "role_id": 29,
      "role_name": "胜因团队",
      "memo": null,
      "created_at": "2017-01-18T11:03:25.000+08:00"
    }
  ]
}
```

#### 更新用户角色权限

```
post /api/v2/user/:user_num/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role_ids: [角色ID 列表]
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 群组

##### 创建群组

```
post /api/v2/group

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  group: {
    "group_id": "可选，群组ID",
    "group_name": "必填，群组名称",
    "memo": "可选，备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 群组信息
    id: "数据表标识",
    "group_id": "群组ID",
    "group_name": "群组名称",
    "memo": "备注说明"
  }
}
```

###### 获取群组

```
post /api/v2/group/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  id: 数据表标识
}

response:
{
  code: 201(成功)/200(失败原因)
  message: 成功提示/失败原因,
  data: { // 角色信息
    id: "数据表标识",
    "group_id": "群组ID",
    "group_name": "群组名称",
    "memo": "备注说明"
  }
}
```

#### 更新群组权限

```
post /api/v2/group/resources

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  group: {
    group_id: 群组id,
    dept_ids: 门店id,
    class_ids: 商行id,
    gids: 大区(区域)id,
    province_ids: 集群id,
    district_ids: 大区(区域)
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 更新后的群组信息
    "group_id": 165,
    "dept_ids": null,
    "class_ids": "all",
    "gids": null,
    "province_ids": 2,
    "district_ids": "all"
  }
}
```

#### 更新群组

```
post /api/v2/group/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  group: {
    "group_id": "群组ID",
    "group_name": "群组名称",
    "memo": "备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 更新后的群组信息
    id: "数据表标识",
    "group_id": "群组ID",
    "group_name": "群组名称",
    "memo": "备注说明
  }
}
```

##### 获取群组列表

```
get /api/v2/groups

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  page: "可选，第几页，默认 0",
  page_size: "可选，每页数据项数量，默认 15",
  group_name: "可选，筛选项，赋值时具有过滤功能"
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  current_page: 0, // 当前页码
  page_size: 15, // 每页数量
  total_page: 1, // 页数
  total_count: 15, // 总数量
  data: [ // 群组列表
    {
      id: "数据表标识",
      "group_id": "群组ID",
      "group_name": "群组名称",
      "memo": "备注说明"
    }
  ]
}
```

##### 获取用户群组权限列表

```
get /api/v2/user/:user_num/groups


response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  current_page: 0, // 当前页码
  page_size: 15, // 每页数量
  total_page: 1, // 页数
  total_count: 15, // 总数量
  data: [
    {
      id: 165,
      "group_id": "群组ID",
      "group_name": "大区",
      "memo": null,
      "created_at": "2016-02-03T11:30:20.000+08:00"
    }
  ]
}
```

##### 更新用户群组权限

```
post /api/v2/user/:user_num/groups

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  group_ids: [群组ID 列表]
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 生意概况（仪表盘）

##### 创建仪表盘

```
post /api/v2/kpi

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  kpi: {
    kpi_id: "必填,业务ID",
    kpi_group: "分类",
    kpi_name: "标题",
    link: "必填,报表ID",
    publicly: 是否公开通用（无权限设置，所有角色可看）
  }
}

response:
{
  code: 201,
  message: "创建成功"
}
```

配置**仪表盘新菜单项**时需要配置已存在的报表ID，若系统无此报表应该先配置报表，获取报表ID 再来配置仪表盘菜单项。

原因：仪表盘菜单项仅是 APP 模块中的功能快速入口，入口后的报表才是用户使用的重点，业务需求此配置为必填选。

##### 获取仪表盘

```
get /api/v2/kpi/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  id: 数据库标识
}

response:
{
  "code": 200,
  "message": "查询成功",
  "data": {
    id: 8,
    "kpi_id": 8,
    "kpi_name": "第二集群销售额",
    "kpi_group": "生意概况",
    "chart_type": "number3",
    "url": "/mobile/v2/group/%@/template/4/report/8",
    "publicly": null,
    "group_order": 16,
    "item_order": 1
  }
}
```

##### 更新仪表盘

```
post /api/v2/kpi/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  kpi: {
    "kpi_id": 8,
    "kpi_name": "第二集群销售额",
    "kpi_group": "生意概况",
    "chart_type": "number3",
    "url": "/mobile/v2/group/%@/template/4/report/8",
    "publicly": null,
    "group_order": 16,
    "item_order": 1
  }
}

response:
{
  "code": 201,
  "message": "更新成功"
}
```

可以通过更新仪表盘字段 `group_order` 调整每个父项的排序。（按数字大小逆序）（更新该父项相同的所有仪表盘）

可以通过更新仪表盘字段 `item_order` 调整每个子项在父项中的排序。（按数字大小逆序）

##### 获取仪表盘列表

```
get /api/v2/kpis

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  kpi_group: "选择，筛选项，赋值时具有过滤功能",
  kpi_name: "可选，筛选项，赋值时具有过滤功能"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 19,
  "data": [
    {
      id: 8,
      "kpi_id": 8,
      "kpi_name": "第二集群销售额",
      "kpi_group": "生意概况",
      "chart_type": "number3",
      "url": "/mobile/v2/group/%@/template/4/report/8",
      "publicly": null,
      "group_order": 16,
      "item_order": 1
    }
  ]
}
```

多个筛选项时，使用 `AND` 逻辑过滤数据。

#### 获取仪表盘的角色权限列表

```
get /api/v2/kpi/:id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  lazy_load: "加载所有角色（默认可不传该参数）"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 13,
      "role_id": 26,
      "role_name": "筹建",
      "memo": null,
      "created_at": "2017-06-07T14:28:29.000+08:00"
    }
  ]
}
```

#### 更新仪表盘的角色权限列表

```
post /api/v2/kpi/:id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role_ids: [sys_roles.role_id]
}

response:
{
  code: 201,
  message: "数据更新成功"
}
```

#### 获取角色的仪表盘权限列表

```
get /api/v2/role/:role_id/kpis

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 8,
      "kpi_id": 8,
      "kpi_name": "第二集群销售额",
      "kpi_group": "生意概况",
      "chart_type": "number3",
      "url": "/mobile/v2/group/%@/template/4/report/8",
      "publicly": null,
      "group_order": 16,
      "item_order": 1
    }
  ]
}
```

#### 更新角色的仪表盘权限列表

```
post /api/v2/role/:role_id/kpis

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  kpi_ids: []
}

response:
{
  code: 201,
  message: "数据更新成功"
}
```


#### 获取报表列表

```
get /api/v2/reports

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 3,
  "total_count": 38,
  data: [
    {
      id: 8,
      "title": "第二集群销售额",
      "report_id": 8,
      "template_id": 4,
      "has_audio": false,
      "health_value": 0,
      "remark": "无备注",
      "created_at": "16-01-07 08:28:45",
      "updated_at": "17-06-06 14:56:18"
    }
  ]
}
```

#### 报表（分析）

#### 创建分析

```
post /api/v2/analyse

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  analyse: {
    name: "标题",
    group_id: "业务ID", // 角色资源关联时使用（若不设置，则与自增型 id 一致）
    category: "一级分类",
    group_name: "二级分组",
    report_id: "报表ID",
    publicly: 0/1, // 是否公开通用（无权限设置，所有角色可看）
  }
}

response:
{
  code: 201,
  message: "创建成功"
}
```

『分析』『应用』实例本质上是相同的，只是业务层面上概念不同，在 APP 对应的模板中显示。

配置**新菜单项**时需要配置已存在的报表ID或第三方链接，若系统无此报表应该先配置报表获取到报表ID 再来配置菜单项。

原因：此菜单项仅是 APP 模块中的功能快速入口，入口后的报表才是用户使用的重点，业务需求此配置为必填选。

关于业务ID(group_id): 若无明确业务ID, 创建分析时可留空，接口中创建成功后更新更新 group_id = id

#### 获取分析

```
get /api/v2/analyse/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  id: 数据库标识
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 16,
  data: {
    id: 13,
    "name": "第二集群目标",
    "category": "销售分析",
    "group_name": "Bravo目标管理",
    "link_path": "/mobile/v2/group/%@/template/4/report/13",
    "publicly": false,
    "icon": "icon-default.png",
    "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
    "group_id": 13,
    "health_value": 0,
    "group_order": 45,
    "item_order": 1,
    "created_at": "2017-05-14T17:09:41.000+08:00"
  }
}
```

##### 更新分析

```
post /api/v2/analyse/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  analyse: {
    name: "标题",
    group_id: "业务ID", // 角色资源关联时使用（若不设置，则与自增型 id 一致）
    category: "一级分类",
    group_name: "二级分组",
    report_id: "报表ID",
    publicly: 是否公开通用（无权限设置，所有角色可看）
  }
}

response:
{
  code: 201,
  message: "更新成功",
  data: {
    id: 13,
    group_id: "业务ID", // 角色资源关联时使用
    "name": "第二集群目标",
    "category": "销售分析",
    "group_name": "Bravo目标管理",
    "link_path": "/mobile/v2/group/%@/template/4/report/13",
    "publicly": false,
    "icon": "icon-default.png",
    "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
    "group_id": 13,
    "health_value": 0,
    "group_order": 45,
    "item_order": 1,
    "created_at": "2017-05-14T17:09:41.000+08:00"
  }
}
```

可以通过更新分析字段 `group_order` 调整每个父项的排序。（按数字大小逆序）（更新该父项相同的所有仪表盘）

可以通过更新分析字段 `item_order` 调整每个子项在父项中的排序。（按数字大小逆序）

#### 获取分析列表

```
get /api/v2/analyses

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  group_name: "可选，筛选项，赋值时具有过滤功能",
  name: "可选，筛选项，赋值时具有过滤功能"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 16,
  data: [
    {
      id: 13,
      "name": "第二集群目标",
      "category": "销售分析",
      "group_name": "Bravo目标管理",
      "link_path": "/mobile/v2/group/%@/template/4/report/13",
      "publicly": false,
      "icon": "icon-default.png",
      "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
      "group_id": 13,
      "health_value": 0,
      "group_order": 45,
      "item_order": 1,
      "created_at": "2017-05-14T17:09:41.000+08:00"
    }
  ]
}
```

#### 获取分析的角色权限列表

```
get /api/v2/analyse/:analyse_id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  lazy_load: 加载所有角色（默认可不传该参数）
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 13,
      "role_id": 26,
      "role_name": "筹建",
      "memo": null,
      "created_at": "2017-06-07T14:28:29.000+08:00"
    }
  ]
}
```

#### 更新分析的角色权限列表

```
post /api/v2/analyse/:analyse_id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role_ids: [sys_roles.role_id]
}

response:
{
  code: 201,
  message: "数据更新成功"
}
```

#### 获取角色的分析权限列表

```
get /api/v2/role/:role_id/analyses

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 13,
      "name": "第二集群目标",
      "category": "销售分析",
      "group_name": "Bravo目标管理",
      "link_path": "/mobile/v2/group/%@/template/4/report/13",
      "publicly": false,
      "icon": "icon-default.png",
      "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
      "group_id": 13,
      "health_value": 0,
      "group_order": 45,
      "item_order": 1,
      "created_at": "2017-05-14T17:09:41.000+08:00"
    }
  ]
}
```

#### 更新角色的分析权限列表

```
post /api/v2/role/:role_id/analyses

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  analyse_ids: []
}

response:
{
  code: 201,
  message: "数据更新成功"
}
```

#### 专题（应用）

#### 创建应用

```
post /api/v2/app

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  app: {
    name: "标题",
    group_id: "业务ID", // 角色资源关联时使用（若不设置，则与自增型 id 一致）
    category: "一级分类",
    group_name: "二级分组",
    url_path: "第三方链接", // url_path/report_id 二选一配置即可
    report_id: "报表ID", // url_path/report_id 二选一配置即可
    publicly: 是否公开通用（无权限设置，所有角色可看）
  }
}

response:
{
  code: 201,
  message: "创建成功"
}
```

『分析』『应用』实例本质上是相同的，只是业务层面上概念不同，在 APP 对应的模板中显示。

配置**新菜单项**时需要配置已存在的报表ID或第三方链接，若系统无此报表应该先配置报表获取到报表ID 再来配置菜单项。

原因：此菜单项仅是 APP 模块中的功能快速入口，入口后的报表才是用户使用的重点，业务需求此配置为必填选。

关于业务ID(group_id): 若无明确业务ID, 创建分析时可留空，接口中创建成功后更新更新 group_id = id

#### 获取应用

```
get /api/v2/app/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  id: 数据库标识
}

response:
{
  "code": 200,
  "message": "获取数据成功",
  "data": {
    id: 1,
    "name": "2017赛马指标计算规则",
    "category": null,
    "group_name": "学习文档",
    "link_path": "http://123.59.75.85:8080/yhportal/appClientReport/pdf/horseRacingRules.pdf",
    "publicly": false,
    "icon": "icon-default.png",
    "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
    "group_id": 1,
    "health_value": 0,
    "group_order": null,
    "item_order": null,
    "created_at": "2017-06-19T09:57:55.000+08:00"
  }
}
```

#### 更新应用

```
post /api/v2/app/:id

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",（若不设置，则与自增型 id 一致）
  app: {
    name: "标题",
    group_id: "业务ID", // 角色资源关联时使用
    category: "一级分类",
    group_name: "二级分组",
    url_path: "第三方链接", // url_path/report_id 二选一配置即可
    report_id: "报表ID", // url_path/report_id 二选一配置即可
    publicly: 是否公开通用（无权限设置，所有角色可看）
  }
}

response:
{
  code: 201,
  message: "更新成功",
  data: {
    id: 1,
    "name": "2017赛马指标计算规则",
    "category": null,
    "group_name": "学习文档",
    "link_path": "http://123.59.75.85:8080/yhportal/appClientReport/pdf/horseRacingRules.pdf",
    "publicly": false,
    "icon": "icon-default.png",
    "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
    "group_id": 1,
    "health_value": 0,
    "group_order": null,
    "item_order": null,
    "created_at": "2017-06-19T09:57:55.000+08:00"
  }
}
```

可以通过更新应用字段 `group_order` 调整每个父项的排序。（按数字大小逆序）（更新该父项相同的所有仪表盘）

可以通过更新应用字段 `item_order` 调整每个子项在父项中的排序。（按数字大小逆序）

#### 获取应用列表

```
get /api/v2/apps

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  group_name: "可选，筛选项，赋值时具有过滤功能",
  name: "可选，筛选项，赋值时具有过滤功能"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 1,
      "name": "2017赛马指标计算规则",
      "category": null,
      "group_name": "学习文档",
      "link_path": "http://123.59.75.85:8080/yhportal/appClientReport/pdf/horseRacingRules.pdf",
      "publicly": false,
      "icon": "icon-default.png",
      "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
      "group_id": 1,
      "health_value": 0,
      "group_order": null,
      "item_order": null,
      "created_at": "2017-06-19T09:57:55.000+08:00"
    }
  ]
}
```

#### 获取应用的角色权限列表

```
get /api/v2/app/:app_id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  lazy_load: 加载所有角色（默认可不传该参数）
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 6,
      "role_id": 2,
      "role_name": "信息部",
      "memo": null,
      "created_at": null
    }
  ]
}
```

#### 更新应用的角色权限列表

```
post /api/v2/app/:app_id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role_ids: [sys_roles.role_id]
}

response:
{
  code: 201,
  message: "数据更新成功"
}
```

#### 获取角色的应用权限列表

```
get /api/v2/role/:role_id/apps

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 25,
  data: [
    {
      id: 1,
      "name": "2017赛马指标计算规则",
      "category": null,
      "group_name": "学习文档",
      "link_path": "http://123.59.75.85:8080/yhportal/appClientReport/pdf/horseRacingRules.pdf",
      "publicly": false,
      "icon": "icon-default.png",
      "icon_link": "http://yonghui-test.idata.mobi/images/icon-default.png",
      "group_id": 1,
      "health_value": 0,
      "group_order": null,
      "item_order": null,
      "created_at": "2017-06-19T09:57:55.000+08:00"
    }
  ]
}
```

#### 更新角色的应用权限列表

```
post /api/v2/role/:role_id/apps

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  app_ids: []
}

response:
{
  code: 201,
  message: "数据更新成功"
}
```

### 工具箱

工具箱是用户的应用集，即用户便捷使用应用的快速入口；工具箱是特殊场景下的应用。

工具箱的增删改查即对应用的操作。

#### 获取工具箱列表

获取所有的工具箱应用列表。

```
post /api/v2/list/toolbox

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 1,
  "total_count": 5,
  "data": [
    {
      id: 9911,
      "name": "门店巡检",
      "category": "1",
      "group_name": "测试",
      "link_path": "http://development.shengyiplus.com/websites/shop-patrol/inspection/default.html",
      "publicly": false,
      "icon": "icon-icon_xunjian.png",
      "icon_link": "http://localhost:4567/images/icon-icon_xunjian.png",
      "group_id": 9911,
      "health_value": 0,
      "group_order": null,
      "item_order": null,
      "created_at": "2017-07-31T11:45:28.000+08:00"
    }
  ]
}
```

#### 获取工具箱的角色列表

```
get /api/v2/toolbox/:app_id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 2,
  "total_count": 30,
  "data": [
    {
      id: 2,
      "role_id": 18,
      "role_name": "会员店",
      "memo": null,
      "created_at": null
    }
  ]
}
```

#### 获取角色的工具箱列表

```
get /api/v2/role/:role_id/toolbox

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  "code": 200,
  "message": "获取数据列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 3,
  "total_count": 36,
  "data": [
    {
      id: 1,
      "name": "2017赛马指标计算规则",
      "category": "1",
      "group_name": "学习文档",
      "link_path": "http://123.59.75.85:8080/yhportal/appClientReport/pdf/horseRacingRules.pdf",
      "publicly": false,
      "icon": "icon-default.png",
      "icon_link": "http://localhost:4567/images/icon-default.png",
      "group_id": 1,
      "health_value": 0,
      "group_order": 15,
      "item_order": 1,
      "created_at": "2017-06-19T09:57:55.000+08:00"
    }
  ]
}
```

#### 更新工具箱的角色列表

```
post /api/v2/toolbox/:app_id/roles

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  role_ids: [角色ID]
}

response:
{
  "message": "更新成功",
  "code": 201
}
```

#### 更新角色的工具箱列表

```
post /api/v2/role/:role_id/toolbox

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  app_ids: [应用ID]
}

response:
{
  "message": "更新成功",
  "code": 201
}
```

#### 回调函数呼叫记录
#### 地址转经纬度

```
get /api/v2/address_to_location

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  address: "具体地址"
}

response:
{
  code: 200,
  message: "successfully",
  data: "经度,纬度"
}
```

#### 经纬度转地址

```
get /api/v2/location_to_address

params:
{
  api_token: "必填项，具体机制可参考上述相关说明",
  location: 经度,纬度"
}

response:
{
  code: 200,
  message: "successfully",
  data: "具体地址"
}
```

根据业务需要会在部分接口中执行第三方的回调函数，此接口数据只为方便调试。

```
get /api/v2/callback_action_logs

params:
{
  api_token: "必填项，具体机制可参考上述相关说明"
}

response:
{
  code: 200,
  message: 成功提示/失败原因,
  current_page: 0, // 当前页码
  page_size: 15, // 每页数量
  total_page: 1, // 页数
  total_count: 15, // 总数量
  data: [
    {
      id: 7,
      "callback": "YH::Callback.update_user_password", // 本地回调接口封装函数名称
      "url": "http://testapi.yonghui.cn/yhportal/app/api/user/updatePassword", // 回调接口链接
      "method": "post", // 回调接口呼叫方式
      "params": { // 回调接口传参，实际响应为 JSON 字符串
        "openApiCode": "OPENAPI_000005",
        "jobNumber": "80715104",
        "password": "-",
        "sign": "-"
      },
      "response": { // 回调接口呼叫后响应信息，实际响应为 JSON 字符串
        code: 200,
        message: "",
        "body": { // 回调接口响应体
          "msg": "修改密码成功",
          code: 0,
          data: ""
        }
      },
      "backtrace": { // 回调接口整个过程的记录信息，实际响应为 JSON 字符串
        "callback": "YH::Callback.update_user_password",
        "params": {
          "user_num": "80715104",
          "user_pass": "-"
        },
        "response_hash": {
          code: 201,
          message: "回调函数执行成功",
          "method": "post",
          "callback_url": "http://testapi.yonghui.cn/yhportal/app/api/user/updatePassword",
          "params": {
            "openApiCode": "OPENAPI_000005",
            "jobNumber": "80715104",
            "password": "-",
            "sign": "-"
          },
          "response": {
            code: 200,
            message: "",
            "body": {
              "msg": "修改密码成功",
              code: 0,
              data: ""
            }
          }
        }
      }
    }
  ]
}
```



