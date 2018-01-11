## 报表数据表

报表的数据表设计方面有以下几个特点：

1. 数据表结构统一
2. 表名、列名的命名规范与业务场景无关

快捷创建报表数据表的存储过程：

```
call pro_create_report_data_table(报表ID, 'main');
```

更多系统存储过程[入口](/docs/report/procedures.sql.md)

### 数据表结构统一

报表业务场景的复杂性不应该也不允许影响数据表的设计，从而达到规范化、流程化开发报表的目的。

数据表结构的设计：

1. 必须存在的生意+ 业务字段：report_id/group_id/period/num/part_name/level
2. 分别 8 个字符串(dim)、整型(mea_int)、浮点型(mea_float) 列，根据需要可手工按照规范添加列

```
CREATE TABLE `report_data_0nn_main` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `report_id` bigint(20) NOT NULL,
      `group_id` bigint(20) DEFAULT NULL,
      `period` varchar(50) DEFAULT NULL,
      `num` varchar(100) DEFAULT NULL,
      `part_name` varchar(20) DEFAULT NULL,
      `level` int(11) DEFAULT NULL,
      `dim_date` datetime DEFAULT NULL,
      `dim_com` varchar(200) DEFAULT NULL,
      `dim1` varchar(200) DEFAULT NULL,
      `dim2` varchar(200) DEFAULT NULL,
      `dim3` varchar(200) DEFAULT NULL,
      `dim4` varchar(200) DEFAULT NULL,
      `dim5` varchar(200) DEFAULT NULL,
      `dim6` varchar(200) DEFAULT NULL,
      `dim7` varchar(200) DEFAULT NULL,
      `dim8` varchar(200) DEFAULT NULL,
      `mea_int1` bigint(20) DEFAULT NULL,
      `mea_int2` bigint(20) DEFAULT NULL,
      `mea_int3` bigint(20) DEFAULT NULL,
      `mea_int4` bigint(20) DEFAULT NULL,
      `mea_int5` bigint(20) DEFAULT NULL,
      `mea_int6` bigint(20) DEFAULT NULL,
      `mea_int7` bigint(20) DEFAULT NULL,
      `mea_int8` bigint(20) DEFAULT NULL,
      `mea_float1` double DEFAULT NULL,
      `mea_float2` double DEFAULT NULL,
      `mea_float3` double DEFAULT NULL,
      `mea_float4` double DEFAULT NULL,
      `mea_float5` double DEFAULT NULL,
      `mea_float6` double DEFAULT NULL,
      `mea_float7` double DEFAULT NULL,
      `mea_float8` double DEFAULT NULL,
      `load_time` datetime DEFAULT NULL,
      `created_at` datetime DEFAULT CURRENT_TIMESTAMP,
      `updated_at` datetime DEFAULT CURRENT_TIMESTAMP,
      PRIMARY KEY (`id`),
      KEY `app_ids` (`report_id`,`group_id`) USING BTREE
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 表名、列名的命名规范与业务场景无关

报表的数据表名依然与业务场景无关，而与报表 ID 关联。

1. 数据表名的命名规范: report_data_<REPORT_ID>_<PART_NAME>, PART_NAME 在关联多个数据表开发一支报表时才起作用。（可根据控件名命名，默认为 main）
2. 列名命名规范：8 个字符串(dim)、整型(mea_int)、浮点型(mea_float) 列，根据需要可手工按照规范添加列