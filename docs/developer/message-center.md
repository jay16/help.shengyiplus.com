## 消息管理中心

业务场景：

- 报表预警
- 单点通知
- 异常通知

当前支持短信（sms）、移动端消息推送（push）两种通知方式，后续会支持邮件（email）通知类型。

消息通知的接收者只支持用户编号（user_num），短信通知时的手机号及邮件的邮箱地址都是从用户表获取；用户表以外的用户不在消息通知范围内。

### 界面操作

可以通过 『管理中心』 - 『消息中心』 新建消息通知；新建成功后立即执行任务。

![](/assets/images/消息中心-新建.jpeg)

*ps:『消息中心』在功能上包含了『消息推送』模块*

### 后台数操作

数据运维同事可以直接在数据库表中直接插入消息内容；定时任务每五分钟检测。

`sys_message_center`

字段 | 类型 | 必填 | 说明
:---|:---|:---|:---
content | text | Y | 通知的内容
notify_mode | string | 默认 sms| 通知的类型（sms/push）
notify_level |  int | 默认 1 | 通知的优先级（1-10）
receivers | text | Y | 接收者（用户编号，多个使用半角逗号分隔）
extra_params_string | string | 额外参数（JSON 格式）

当前消息类型为推送消息时，字段 `extra_params_string` 为跳转需要的参数：(字符串格式的 JSON 内容)

```
{
  type: kpi/app/analyse/message/thursday_say/report, // 必填
  title: '16年第三季度季报’, // 可选
  url: 'report-link’,      // 可选，与 API 链接格式相同
  obj_id: 1,               // 可选，目标ID
  obj_type: 1              // 可选，目标类型
}
```

- 通知类型为**推送消息**时，需判断接收者是否登录过客户端（注册推送消息的 token），以便测试客户端接收消息的推送状态。

    ```
    select sd.name
          , sd.platform
          , sd.os
          , sd.os_version
          , sd.push_device_token
      from sys_devices as sd
     where sd.id in (
      select sud.device_id
        from sys_user_devices as sud
       where sud.user_num in ('mock-user')
         and length(sd.push_device_token) > 0
    )
    ```

- 通知类型为**发短信**时，需判断接收者是否有配置手机号，以便测试短信的接收状态。

    ```
    select mobile
      from sys_users
     where user_num in ('mock-user')
    ```