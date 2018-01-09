## SQL/Procedure

- 原则上可以使用 SQL 时不用 Procedure
- Procedure 同时定义在主库中（sup_procedure.procontent)（会有定时任务或界面操作批量同步至各子库）

## Procedure

主库中定义存储过程的示例：

```
drop procedure if exists `pro_sales_params_p0`;
delimiter ;;

-- 业务存储过程
CREATE PROCEDURE `pro_sales_params_p0`(in var_name varchar(255) CHARSET utf8) 
BEGIN
    SELECT DISTINCT time_value AS `value`  FROM kpi_sales_analysis
    WHERE time_type = 'month';
END

;;
delimiter ;
```

已维护在子库中的存储过程如何维护在主库中：

1. 查看存储过程的定义

```
show create procedure pro_sales_params_p0;
```

2. 删除存储过程定义中的用户权限

```
-- 包含用户权限的示例，不允许
CREATE DEFINER=`saas000`@`%` PROCEDURE `pro_sales_params_p0`()

-- 无用户权限约束的示例，要求 
CREATE PROCEDURE `pro_sales_params_p0`()
```

3. 添加创建存储过程的基本操作

```
drop procedure if exists `存储过程名称`;
delimiter ;;

存储过程的定义代码

;;
delimiter ;
```

4. 传参的数据编码问题

强调设置传的数据编码，比遇到问题再解决更敏捷;

仅字符串型需要设置此项。

```
CREATE PROCEDURE `pro_sales_params_p0`(in var_name varchar(255) CHARSET utf8) 
```
