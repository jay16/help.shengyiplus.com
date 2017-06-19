## `API#v2` 接口

### API v1/v2 的区别：

* `API#v1` 生意人应用内部使用的接口，由于业务特殊性而无法给外部通用
* `API#v2` 生意人应用给第三方使用的通用接口，单一性强，为了达到某个业务场景的需求可以组合配合使用多个接口

### 接口列表

待实现、已实现的接口都在列表中，接口是否实现以该接口说明段落中的状态为主。

* 创建用户
* 获取用户列表
* 更新用户（基本信息，不关联权限逻辑）
* 删除用户
* 创建角色
* 获取角色列表
* 更新角色
* 删除角色
* 创建群组
* 获取群组列表
* 更新群组
* 删除群组
* 获取用户角色权限列表
* 更新用户角色权限
* 获取用户群组权限列表
* 更新用户群组权限
* 更新用户冻结状态
* 更新用户登录密码
* 重置用户登录密码
* 回调函数呼叫记录

### 接口交互信息说明

代码的实现理念涉及 [HTTP 状态码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)、[RESTful API](http://www.csdn.net/article/2013-06-13/2815744-RESTful-API)，有实现不合理的地方欢迎交流讨论，确定问题后会第一时间修正。

* API 验证参数 `api_token`

  * 必填项，否则响应无权限 401
  * 当前状态未对该参数作判断，只要有值就可以（TODO: API 接口验证机制）

* CRUD 分别对应的 HTTP 状态码

  * 无权限时响应 401
  * 创建、更新成功时响应 201
  * 获取数据成功时响应 200

* CRUD 分别对应的 HTTP 操作

  * 创建、更新时 Post
  * 获取时 Get
  * 删除时 Delete

* 接口响应字段

  * 创建时 \[code, message, data\]
  * 更新时 \[code, message, data\]
  * 获取时 \[code, message, data\]
  * 删除时 \[code, message\]

* 分页说明：起始页码为 0

### 接口交互

#### 创建用户

```
post /api/v2/user

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
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

#### 获取用户列表

```
get /api/v2/users

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  page: '可选，第几页，默认 0',
  limit: '可选，每页数据项数量，默认 15'
}

response:
{
  code: 200(成功)/401(接口权限验证失败)
  message: 成功提示/失败原因,
  current_page: 0,
  page_size: 15,
  total_page: 438,
  data: [ // 用户列表
    {
      id: "数据表标识ID",
      user_name: "用户名称",
      user_num: "用户编号，确保全表唯一",
      user_pass: "登录密码，MD5 加密",
      status: "冻结状态, true/false",
      email: "邮箱",
      mobile: "联系方式",
      tel: "手机号",
      join_date: "入职日期，格式 yyyy-mm-dd HH:MM:SS",
      position: "职位"
    }
  ]
}
```

#### 更新用户（基本信息，不关联权限逻辑）

```
post /api/v2/user/:user_num

params:
{
  api_token: '必填项，具体机制可参考上述相关说明'
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

#### 删除用户

```
delete /api/v2/user

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  user_num: "用户编号"
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 创建角色

```
post /api/v2/role

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  role: {
    role_name: "必填，角色名称",
    memo: "可选，备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因)
  message: 成功提示/失败原因,
  data: { // 角色信息
    id: "数据表标识ID",
    role_name: "角色名称",
    memo: "备注说明"
  }
}
```

#### 获取角色列表

```
get /api/v2/roles

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  page: '可选，第几页，默认 0',
  limit: '可选，每页数据项数量，默认 15'
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  current_page: 0,
  page_size: 15,
  total_page: 13,
  data: [ // 角色列表
    {
      id: "数据表标识ID",
      role_name: "角色名称",
      memo: "备注说明"
    }
  ]
}
```

#### 更新角色

```
post /api/v2/role/:id

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  role: {
    role_name: "角色名称",
    memo: "备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 更新后的角色信息
    id: "数据表标识ID",
    role_name: "角色名称",
    memo: "备注说明
  }
}
```

#### 删除角色

```
delete /api/v2/role

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  id: "数据表标识ID"
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 创建群组

```
post /api/v2/group

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  group: {
    group_name: "必填，群组名称",
    memo: "可选，备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 群组信息
    id: "数据表标识ID",
    group_name: "群组名称",
    memo: "备注说明"
  }
}
```

#### 获取群组列表

```
get /api/v2/groups

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  page: '可选，第几页，默认 0',
  limit: '可选，每页数据项数量，默认 15'
}

response:
{
  code: 200(成功)/401(接口权限验证失败),
  message: 成功提示/失败原因,
  current_page: 0,
  page_size: 15,
  total_page: 21,
  data: [ // 群组列表
    {
      id: "数据表标识ID",
      group_name: "群组名称",
      memo: "备注说明"
    }
  ]
}
```

#### 更新群组

```
post /api/v2/group/:id

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  group: {
    group_name: "群组名称",
    memo: "备注说明"
  }
}

response:
{
  code: 201(成功)/200(失败原因),
  message: 成功提示/失败原因,
  data: { // 更新后的群组信息
    id: "数据表标识ID",
    group_name: "群组名称",
    memo: "备注说明
  }
}
```

#### 删除群组

```
delete /api/v2/group

params:
{
  api_token: '必填项，具体机制可参考上述相关说明'
  id: "数据表标识ID"
}

response:
{
  code: 200(成功)/401(接口权限验证失败)
  message: 成功提示/失败原因
}
```

#### 获取用户角色权限列表

```
get /api/v2/user/:user_num/roles


response:
{
  "code": 200(成功)/401(接口权限验证失败),
  "current_page": 0,
  "page_size": 15,
  "total_page": 1,
  "message": "获取用户角色列表成功",
  "data": [
    {
      "id": 99,
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
  api_token: '必填项，具体机制可参考上述相关说明',
  role_ids: [角色ID 列表]
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 获取用户群组权限列表

```
get /api/v2/user/:user_num/groups


response:
{
  "code": 200(成功)/401(接口权限验证失败),
  "current_page": 0,
  "page_size": 15,
  "total_page": 1,
  "message": "获取用户群组列表成功",
  "data": [
    {
      "id": 165,
      "group_name": "大区",
      "memo": null,
      "created_at": "2016-02-03T11:30:20.000+08:00"
    }
  ]
}
```

#### 更新用户群组权限

```
post /api/v2/user/:user_num/groups

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
  group_ids: [群组ID 列表]
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

#### 更新用户冻结状态

```
post /api/v2/user/:user_num/status

params:
{
  api_token: '必填项，具体机制可参考上述相关说明',
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
  api_token: '必填项，具体机制可参考上述相关说明',
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
  api_token: '必填项，具体机制可参考上述相关说明',
  mobile: '必填项，与用户编号关联的手机号，要求业务数据维护'
}

response:
{
  code: 201(成功)/200(失败)/401(接口权限验证失败),
  message: 成功提示/失败原因
}
```

重置登录密码的操作：随机六位数字更新为用户登录密码，然后通过手机短信的形式送达至参数中的手机号。

**系统会根据配置回调第三方更新密码接口，请第三方更新密码接口特殊处理，勿再回调本系统更新密码接口，避免进入接口回调死循环**。


#### 回调函数呼叫记录

根据业务需要会在部分接口中执行第三方的回调函数，此接口数据只为方便调试。

```
get /api/v2/callback_action_logs

params:
{
  api_token: '必填项，具体机制可参考上述相关说明'
}

response:
{
  "code": 200,
  "message": "获取回调函数呼叫记录列表成功",
  "current_page": 0,
  "page_size": 15,
  "total_page": 1,
  "data": [
    {
      "id": 7,
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
        "code": 200,
        "message": "",
        "body": { // 回调接口响应体
          "msg": "修改密码成功",
          "code": 0,
          "data": ""
        }
      },
      "backtrace": { // 回调接口整个过程的记录信息，实际响应为 JSON 字符串
        "callback": "YH::Callback.update_user_password",
        "params": {
          "user_num": "80715104",
          "user_pass": "-"
        },
        "response_hash": {
          "code": 201,
          "message": "回调函数执行成功",
          "method": "post",
          "callback_url": "http://testapi.yonghui.cn/yhportal/app/api/user/updatePassword",
          "params": {
            "openApiCode": "OPENAPI_000005",
            "jobNumber": "80715104",
            "password": "-",
            "sign": "-"
          },
          "response": {
            "code": 200,
            "message": "",
            "body": {
              "msg": "修改密码成功",
              "code": 0,
              "data": ""
            }
          }
        }
      }
    }
  ]
}
```
