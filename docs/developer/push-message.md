- [友盟推送平台](http://mobile.umeng.com/push)
- [开发文档：服务器端](http://dev.umeng.com/push/android/api-doc)
- [开发文档：iOS 端](http://dev.umeng.com/push/ios/integration)
- [开发文档：Android 端](http://dev.umeng.com/push/android/integration)

## 消息推送

各家消息推送服务的调用方式都是相似的，目前生意+ 线上使用的是**友盟消息推送服务**，后续会在当前的架构基础上继续支持极光、个推等推送服务（*主要是安卓平台的消息推送到达率不稳定，谷歌消息称会统一安卓的消息推送服务，期待*）

消息推送的原理：**移动端设备调用友盟 SDK 在友盟服务器注册获取 token, 服务器端通过该 token 向移动端设备推送内容。**

开发调用推送服务时需要注意：

1. 移动端设备注册友盟推送服务 token 的必须性
2. 移动端设备在自身平台注册时的唯一性
3. 建立用户 <-> 设备 <-> token 的关联性
4. 服务器端推送消息时需要区分 ios/android 系统（分别调用对应的 API）
5. 推送消息时以设备 token 为单位(广播时不需要)

![消息推送-注册机制](/assets/images/生意+ 消息推送/消息推送-注册机制.png)

## 接口配置

1. 需要把服务器端 IP 配置在友盟消息推送平台对应应用的 IP 白名单中
2. 服务器端需要友盟平台消息推送应用的配置信息：
    - iOS: Appkey/App Master Secret
    - Android: Appkey/App Master Secret
    
3. 移动端需要友盟平台消息推送应用的配置信息：
    - iOS: Appkey
    - Android: Appkey/Umeng Message Secret

![消息推送-API接口](/assets/images/生意+ 消息推送/友盟推送-iOS配置服务器IP地址.png)

### 查询消息推送状态

查询实时消息的推送状态：

```
select 
  title as '标题'
  , content as '内容'
  , push_type as '推送类型'
  , ios_device_tokens as 'iOS 设备'
  , ios_ret as 'iOS 推送状态'
  , ios_response as 'iOS 推送响应'
  , android_device_tokens as 'Android 设备'
  , android_ret as 'Android 推送状态'
  , android_response as 'Android 推送响应'
from sys_push_messages 
order by id desc;
```

查询非实时消息的推送状态：

```
select 
  smc.receivers as '接收者员工编号列表'
  , smc.created_at as '任务创建时间'
  , spm.created_at as '消息推送时间'
  , spm.title as '标题'
  , spm.content as '内容'
  , spm.push_type as '推送类型'
  , spm.ios_device_tokens as 'iOS 设备'
  , spm.ios_ret as 'iOS 推送状态'
  , spm.ios_response as 'iOS 推送响应'
  , spm.android_device_tokens as 'Android 设备'
  , spm.android_ret as 'Android 推送状态'
  , spm.android_response as 'Android 推送响应'
from sys_message_center as smc
left join sys_push_messages as spm on spm.id = replace(smc.send_result, 'push_message#', '')
where smc.notify_mode = 'push'
order by smc.id desc;
```

## API 接口

实时接口与定时任务推送消息时使用的同一套推送逻辑，仅仅是业务场景上的使用区别。

推送消息需要的参数：

1. 用户编号（可以标识用户的字段即可），获取关联的设备 token 列表
2. 推送标题、内容
3. 额外的参数（设备接收到推送消息点击后的业务操作）

![消息推送-API接口](/assets/images/生意+ 消息推送/消息推送-API接口.png)

### 实时 API

客户操作的业务模块中调用实时接口推送消息以便用户间的交流。

推送消息的操作在 `sys_push_message` 中执行：先调用推送接口、记录推送任务ID(服务商接口响应)、实时扫描该任务 ID 的状态、推送完成（成功、或失败）再更新回数据表`sys_push_message` 中。

#### 用户验证接口

仅起示例效果，表明用户编号是标识用户的字段。

```
post /api/v1.1/user/authentication
params:
{
    user_num: 用户编号,
    password: 登录密码
}
response:
{
    id: 用户 ID
    ...  
}
```

#### 注册设备/用户-设备关联接口

1. 移动端需要确认 uuid/push_token 注册成功后再提交服务器
2. 移动需要存储注册设备成功后的 `user_device_id`, 根据此值判断是否需要注册设备
3. APP 升级时需要作自身版本控制，清理 `user_device_id` 缓存重新注册设备
4. 服务器端根据 uuid 判断是否需要插入数据库或更新

涉及表：`sys_devices`/`sys_user_devices`

```
post /api/v1.1/app/device
params: 
{
    user_num: 用户编号
    uuid: 设备标识，移动端同事手写代码生成,
    push_token: 在推送服务商注册的 token
    push_platform: 推送服务商
    name: 设备的用户名称
    platform: android/ios, 固定值
    os: 设备系统
    os_version: 设备系统版本
}
response: 
{
    device_uuid: 设备标识
    user_device_id: 用户-设备关联表中的 ID
}
```

#### 提交消息推送接口

根据用户编号关联出登录过的设备列表，调用友盟消息推送接口传入设备 token 列表（逗号分隔）及推送内容。

调用友盟推送接口后，友盟也是把该推送任务加入排程，需要异步任务一直扫描该任务的状态，最后把推送状态更新该数据行。

```
post /api/v1.1/user/push_message
params:
{
    user_num: 用户编号
    title: 推送标题
    content: 推送内容
}
response:
{
    push_message_id: 推送消息实例行 ID
}
```


#### 查询消息推送状态接口

其实就是获取指定数据行的状态列值。（很少使用，实时推送后查询意义不大，结果只会是*加入排程*）

```
get /api/v1.1/query/push_message
params:
{
    push_message_id: 推送消息实例行 ID
}
response: 
{
    ios_status: ios 推送状态
    android_status: android 推送状态
}
```

#### 登出接口

登出时，更新或删除用户-设备关联表中的状态；（该设备被 A 用户登录时，仍存在登录状态的 B/C/D 用户表示也登出了）

```
post /api/v1.1/user/logout
params:
{
    user_num: 用户编号
    user_device_id: 用户-设备关联 ID
}
response:
{
    data: successfully
}
```

### 定时任务

场景比较多：

1. 数据运维过程中监控的业务字段值超出预警线时
2. 定时监控内存、磁盘、缓存刷新任务过程中捕获异常时
3. 刻意延迟推送（目前还不支持定时定点推送）

上述场景中可直接向消息中心表 `sys_message_center` 插入内容，更新目标用户及预期文字等字段，定时任务会每分钟扫描消息中心表中未发送的消息，根据通知类型做不同的操作。

需要强调一点：**定时任务代码逻辑要严谨**


## 基础数据表

用户维表：（主要使用到了 user_num, 设计时根据需要调整标识用户的字段）

```
CREATE TABLE `sys_users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_name` varchar(50) NOT NULL comment '用户名称',
  `user_num` varchar(50) NOT NULL comment '用户编号',
  `user_pass` varchar(50) NOT NULL comment '登录密码',
  `email` varchar(28) DEFAULT '' comment '邮件',
  `mobile` varchar(18) DEFAULT '' comment '联系方式',
  `tel` varchar(16) DEFAULT '',
  `join_date` datetime DEFAULT NULL,
  `position` varchar(50) DEFAULT NULL,
  `dept_id` int(11) DEFAULT NULL,
  `dept_name` varchar(50) DEFAULT NULL,
  `active_flag` int(11) DEFAULT '1',
  `create_user` int(11) DEFAULT NULL,
  `update_user` int(11) DEFAULT NULL,
  `memo` varchar(300) DEFAULT NULL,
  `load_time` datetime DEFAULT NULL,
  `last_login_at` datetime DEFAULT '2016-01-15 06:19:44',
  `last_login_ip` varchar(255) DEFAULT '',
  `last_login_browser` varchar(255) DEFAULT '',
  `sign_in_count` int(11) DEFAULT '0',
  `last_login_version` varchar(255) DEFAULT '',
  `access_token` varchar(255) DEFAULT '',
  `store_ids` text,
  `coordinate` varchar(255) DEFAULT NULL,
  `coordinate_location` varchar(255) DEFAULT NULL,
  `created_at` datetime DEFAULT CURRENT_TIMESTAMP,
  `updated_at` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `index_sys_users_on_user_num` (`user_num`)
) ENGINE=InnoDB AUTO_INCREMENT=9243 DEFAULT CHARSET=utf8
```

设备维表：（建议采用所有业务字段）

```
CREATE TABLE `sys_devices` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL comment '设备用户名',
  `platform` varchar(255) DEFAULT NULL comment '系统类型ios/android',
  `os` varchar(255) DEFAULT NULL comment '系统名称',
  `os_version` varchar(255) DEFAULT NULL comment '系统版本',
  `uuid` varchar(255) DEFAULT NULL comment '设备 UUID',
  `push_device_token` varchar(255) DEFAULT '' comment '推送 token',
  `alias` varchar(255) DEFAULT NULL comment '设备别名',
  `push_platform` varchar(255) DEFAULT 'umeng' comment '消息推送商，移动端代码中会根据不同系统使用不同的推送服务商',
  `created_at` datetime DEFAULT CURRENT_TIMESTAMP,
  `updated_at` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `index_sys_devices_on_platform_and_uuid` (`platform`,`uuid`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8
```

用户-设备关联：（只需要用户编号、设备 ID）

```
CREATE TABLE `sys_user_devices` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_num` varchar(255) DEFAULT NULL comment '用户编号',
  `device_id` int(11) DEFAULT NULL comment '设备 ID',
  `state` tinyint(1) DEFAULT '1' comment '设备状态',
  `screen_lock_state` tinyint(1) DEFAULT '0' comment '设备锁屏状态',
  `screen_lock_type` varchar(255) DEFAULT NULL comment '设备锁屏类型',
  `screen_lock` varchar(255) DEFAULT NULL comment '设备锁屏密码',
  `created_at` datetime DEFAULT CURRENT_TIMESTAMP,
  `updated_at` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8
```

消息推送表：（核心字段 title/content/extra_params_string/push_type/ios|android_device_tokens, 其他字段都是用来监控推送任务的状态）

```
CREATE TABLE `sys_push_messages` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(255) DEFAULT NULL comment '标题',
  `content` text comment '内容',
  `extra_params_string` varchar(255) DEFAULT NULL comment '额外参数',
  `push_type` varchar(255) DEFAULT NULL comment '推送类型',
  `ios_device_tokens` text comment 'ios token 列表',
  `android_device_tokens` text comment 'android token 列表',
  `ios_task_id` varchar(255) DEFAULT NULL comment 'ios 推送任务 id',
  `ios_ret` varchar(255) DEFAULT NULL comment 'ios 推送结果',
  `ios_status` varchar(255) DEFAULT NULL comment 'ios 推送状态',
  `ios_response` varchar(255) DEFAULT NULL comment 'ios 推送响应',
  `ios_timestamp` varchar(255) DEFAULT NULL comment 'ios 时间戳',
  `is_send_ios` tinyint(1) DEFAULT '1' comment 'ios 推送任务是否执行',
  `android_task_id` varchar(255) DEFAULT NULL comment 'android 推送任务 id',
  `android_ret` varchar(255) DEFAULT NULL comment 'android 推送结果',
  `android_status` varchar(255) DEFAULT NULL comment 'android 推送状态',
  `android_response` varchar(255) DEFAULT NULL comment 'android 推送响应',
  `android_timestamp` varchar(255) DEFAULT NULL comment 'android 时间戳',
  `is_send_android` tinyint(1) DEFAULT '1' comment 'android 推送任务是否执行',
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8
```

消息中心表（定时任务使用, 需要把用户标识字段关联业务表转化推送 token）

```
CREATE TABLE `sys_message_center` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(255) DEFAULT NULL comment '标题',
  `content` text NOT NULL comment '内容',
  `extra_params_string` varchar(255) DEFAULT NULL comment '额外参数',
  `notify_mode` varchar(255) DEFAULT 'sms' comment '通知类型 sms/push/email',
  `notify_level` int(11) DEFAULT '0' comment '通知类型 sms/push/email',
  `receivers` text NOT NULL comment '接收人列表',
  `send_state` tinyint(1) DEFAULT '0' comment '通知状态',
  `send_result` text comment '通知结果',
  `remark` text,
  `created_at` datetime NOT NULL,
  `updated_at` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8
```