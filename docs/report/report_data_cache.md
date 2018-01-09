## 报表数据缓存

报表数据的缓存涉及以下几点：

1. 报表维表(sys_template_reports)
2. 报表-群组关联列(sys_group_reports)
3. 报表数据表(`report_data_<REPORT_ID>_<PART_NAME>`)
4. 报表缓存时间戳(sys_timestamp_manager)

手工刷新报表缓存:

```
# REPORT_IDS 多个报表 ID 时以逗号分隔
# GROUP_IDS 多个群组 ID 时以逗号分隔
$ bundle exec rake report:cache:report_ids DATABASE=db001 REPORT_IDS=1 GROUP_IDS=1
```

### 报表维表(sys_template_reports)

报表包含以下几项重要列：

1. 报表 ID
2. 模板 ID [报表模板列表](/docs/templates/index.md)
3. 报表 JSON 配置（与模板 ID 相关）

```
select 
  report_id
  , template_id
  , content
from sys_template_reports
```

### 报表-群组关联列(sys_group_reports)

数据同事会根据业务需要更新此数据表，同时在数据表中导入对应群组权限的业务数据.

查询用户的群组、角色信息：

```
call pro_query_user(<USER_NUM>);
```

查看报表关联的群组列表：

```
select 
  str.title
  , str.report_id
  , str.template_id
  , sg.group_name
  , sg.group_id
from sys_template_reports as str
left join sys_group_reports as sgr on sgr.report_id = str.report_id
left join sys_groups as sg on sg.id = sgr.group_id
where str.report_id = <REPORT_ID>;
```

### 报表 JSON 配置（与模板 ID 相关）

[报表模板列表](/docs/templates/index.md)

### 报表缓存时间戳(sys_timestamp_manager)

服务器端有定时任务会扫描报表缓存时间戳表，当前缓存的时间戳与数据表中的时间戳不同时，则执行刷新报表数据缓存，并更新缓存中的时间戳为数据表中的时间戳。


```
select 
  timestamp 
from sys_timestamp_manager 
where obj_type = 'report#data' 
  and obj_id = <REPORT_ID>;
```
