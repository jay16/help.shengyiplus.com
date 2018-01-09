## 系统存储过程

```
-- -----------------------------------------------------
-- 生意+ APP 业务层常用存储过程
--
-- 说明：
-- 1. 该存储过程不止在命令中执行，会也被前端接口呼叫使用数据，别名（as）请使用英文
-- 2. 更新此文档时，请勿必更新或补充存储过程 pro_version/pro_pro_list
-- -----------------------------------------------------

-- -----------------------------------------------------
-- 存储过程版本
--
-- @var_user_num 用户编号 
-- -----------------------------------------------------
drop procedure if exists pro_version;
delimiter ;;
create procedure pro_version()
begin
  -- select now() as version
  select '171220202038' as 'version';
end
;;
delimiter ;

-- -----------------------------------------------------
-- 存储过程列表
--
-- @var_user_num 用户编号
-- -----------------------------------------------------
drop procedure if exists pro_pro_list;
delimiter ;;
create procedure pro_pro_list()
begin
  select 
    routine_schema as database_name
    , routine_name as procedure_name
  from information_schema.routines 
  where routine_schema = database()
    and routine_name like 'pro_%'
    and routine_type = 'PROCEDURE'
  order by routine_name asc;
end
;;
delimiter ;


-- -----------------------------------------------------
-- 查询用户信息
--
-- @var_user_num 用户编号 
-- -----------------------------------------------------
drop procedure if exists pro_query_user;
delimiter ;;
create procedure pro_query_user(in var_user_num char(50))
begin
  select
    su.id,
    su.user_name,
    su.user_num,
    su.user_pass,
    sr.role_name,
    sr.id as roleid,
    sr.role_id,
    sg.group_name,
    sg.id as groupid,
    sg.group_id,
    sg.data_group_id
  from sys_users as su
  left join sys_user_roles as sur on su.id = sur.user_id
  left join sys_roles as sr on sr.id = sur.role_id
  left join sys_user_groups as sug on su.id = sug.user_id
  left join sys_groups as sg on sg.id = sug.group_id
  where su.user_num = var_user_num
  union 
  select 
    '说明'
    , '结果2，为关联该用户的设备列表'
    , ''
    , ''
    , ''
    , ''
    , ''
    , ''
    , ''
    , ''
    , ''
  ;

  select
    su.user_name
    , su.user_num
    , sd.name as device_name
    , sud.state as device_state
    , sd.platform
    , sd.os
    , sd.uuid
    , sd.push_device_token
    , sd.push_platform
    , sd.created_at
    , sud.created_at as 'related_at'
  from sys_users as su
  left join sys_user_devices as sud on sud.user_num = su.user_num
  left join sys_devices as sd on sd.id = sud.device_id
  where su.user_num = var_user_num;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 查询无用设备列表（uuid/push_device_token 为空）
--
-- -----------------------------------------------------
drop procedure if exists pro_query_useless_devices;
delimiter ;;
create procedure pro_query_useless_devices()
begin
  select 
    sd.id
    , sd.name
    , sd.platform
    , sd.os
    , sd.os_version
    , sd.uuid
    , sd.push_device_token
    , sud.user_num
    , sud.id as user_device_id
    , sd.created_at
    , sud.created_at as 'related_at'
  from sys_devices as sd
  left join sys_user_devices as sud on sd.id = sud.device_id
  where sd.uuid is null or sd.push_device_token is null;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 清理无用设备列表（uuid/push_device_token 为空）
--
-- -----------------------------------------------------
drop procedure if exists pro_clean_useless_devices;
delimiter ;;
create procedure pro_clean_useless_devices()
begin
  start transaction;

  -- 清理无用的用户-设备关联记录
  delete 
    sud
  from sys_user_devices as sud
  left join sys_devices as sd on sd.id = sud.device_id
  where sd.uuid is null or sd.push_device_token is null;

  -- 清理无用的设备记录
  delete
    sd
  from sys_devices as sd
  where sd.uuid is null or sd.push_device_token is null;

  commit;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 添加用户
--
-- @var_user_num 用户编号 
-- @v_role_name  角色名称（确认角色存在）
-- @v_group_name 群组名称（确认群组存在）
-- -----------------------------------------------------
drop procedure if exists pro_add_user;
delimiter ;;
create procedure pro_add_user(in par_user_num varchar(50), in par_role_name varchar(100), in par_group_name varchar(100))
label_pro: begin
    declare var_user_id int default null;
    declare var_role_id int default null;
    declare var_group_id int default null;
    declare var_user_role_id int default null;
    declare var_user_group_id int default null;
    declare latest_sql_state int default 0;
    declare continue handler for sqlexception set latest_sql_state = 1;
  
    start transaction;

    select id into var_user_id from sys_users where user_num = par_user_num limit 1;
    -- 用户不存在则创建
    if (var_user_id is null) then
        insert into sys_users(user_name, user_num, user_pass) values(par_user_num, par_user_num, md5(123456));

    if latest_sql_state = 1 then
      rollback;
      select '创建用户失败' as info;
      leave label_pro;
    else
      commit;
    end if;

    select id into var_user_id from sys_users where user_num = par_user_num limit 1;
    end if;

    -- 角色不存在则跳出
    select id into var_role_id from sys_roles where role_name = par_role_name limit 1;
    if (var_role_id is null) then
        select concat(v_role_name, ' 不存在，请创建') as role_name;
        leave label_pro;
    end if;

    -- 群组不存在则跳出
    select id into var_group_id from sys_groups where group_name = par_group_name limit 1;
    if (var_group_id is null) then
        select concat(par_group_name, ' 不存在，请创建') as group_name;
        leave label_pro;
    end if;

    -- 用户-角色关联不存在则创建
    select id into var_user_role_id from sys_user_roles where user_id = var_user_id and role_id = var_role_id;
    if (var_user_role_id is null) then
        insert into sys_user_roles(user_id, role_id) values(var_user_id, var_role_id);
    end if;

    -- 用户-群组关联不存在则创建
    select id into var_user_group_id from sys_user_groups where user_id = var_user_id and group_id = var_group_id;
    if (var_user_group_id is null) then
        insert into sys_user_groups(user_id, group_id) values(var_user_id, var_group_id);
    end if;

    select
        su.id as user_id,
        su.user_num,
        sr.role_name,
        sg.group_name
    from sys_users as su 
    left join sys_user_roles as sur on sur.user_id = su.id
    left join sys_roles as sr on sr.id = sur.role_id
    left join sys_user_groups as sug on sug.user_id = su.id
    left join sys_groups as sg on sg.id = sug.group_id
    where su.user_num = par_user_num;

    commit;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 添加群组
--
-- @par_group_name  群组名称 
-- @par_group_id    群组 ID
-- @par_data_group_id 数据群组 ID
-- -----------------------------------------------------
drop procedure if exists pro_add_group;
delimiter ;;
create procedure pro_add_group(in par_group_name varchar(200), in par_group_id int, in par_data_group_id int)
begin
  declare var_group_id int;

  select group_id into var_group_id from sys_groups where group_id = par_group_id limit 1;

  if (var_group_id is null) then
    insert into sys_groups(group_id, data_group_id, group_name) value(par_group_id, par_data_group_id, par_group_name);
    select * from sys_groups where group_id = par_group_id limit 1;
  else
    select 'already exist'; 
  end if;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 添加角色
--
-- @par_role_name  角色名称
-- @par_role_id    角色 ID
-- -----------------------------------------------------
drop procedure if exists pro_add_role;
delimiter ;;
create procedure pro_add_role(in par_role_name varchar(200), in par_role_id int)
begin
  declare var_role_id int;

  select id into var_role_id from sys_roles where role_name = par_role_name limit 1;

  if (var_role_id is null) then
    insert into sys_roles(role_id, role_name) value(par_role_id, par_role_name);
    select * from sys_roles where role_name = par_role_name limit 1;
  else
    select 'already exist'; 
  end if;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 查询附近门店（根据经纬度）
--
-- @var_latitude  门店纬度
-- @var_longitude 门店经度
-- -----------------------------------------------------
drop procedure if exists pro_query_nereast_stores;
delimiter ;;
create procedure pro_query_nereast_stores(in var_latitude char(30), in var_longitude char(30))
begin
  select store_code, store_name, address,
     (3959 * acos(
      cos(radians(var_latitude))
      * cos(radians(latitude))
      * cos(radians(longitude) - radians(var_longitude))
      + sin(radians(var_latitude))
      * sin(radians(latitude))
      )) as distance
  from sys_store_locations
  order by distance asc;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 查询用户 APP 菜单权限及报表数据量
--
-- @var_user_num 用户编号 
-- -----------------------------------------------------
drop procedure if exists pro_query_app_menus;
delimiter ;;
create procedure pro_query_app_menus(var_user_num varchar(225))
begin
  declare group_id1 varchar(255) default '' ;
  declare report_table_name1 varchar(255) default '' ;  
    
  declare done int default false;
  declare cur cursor for select group_id,report_table_name
                           from tmp_obj
                          where report_table_name is not null;
                          
  declare continue handler for not found set done = true;

  create temporary table if not exists `tmp_obj` (
    `user_id` varchar(50) not null,
    `user_name` varchar(50) not null,
    `user_num` varchar(50) not null,
    `created_at` datetime default null,
    `role_id` varchar(50) default null,
    `role_name` varchar(50) default null,
    `group_id` varchar(50) default null,
    `group_name` varchar(50) default null,
    `obj_type` varchar(50) default null,
    `obj_name` varchar(50) default null,
    `report_id` varchar(50) default null,
    `report_table_name` varchar(50) default null,
    `row_count` varchar(50) default null
  ) engine=innodb default charset=utf8;

  create temporary table if not exists `tmp_obj_01` (
    `report_table_name` varchar(50) default null,
    `row_count` varchar(50) default null
  ) engine=innodb default charset=utf8;

  insert into tmp_obj(`user_id`,`user_name`,`user_num`,`created_at`,`role_id`,`role_name`,`group_id`,`group_name`,`obj_type`,`obj_name`,`report_id`,`report_table_name`)  
  select t1.user_id,t1.user_name,t1.user_num,t1.created_at,t1.role_id,t1.role_name,t1.group_id,t1.group_name,t1.obj_type,t1.obj_name,t1.report_id,t2.report_table_name
    from (
      select 
         a.id as user_id
        ,a.user_name as user_name
        ,a.user_num as user_num
      ,a.created_at as created_at
        ,d.id as role_id
        ,d.role_name as role_name
        ,e.id  as group_id
        ,e.group_name  as group_name
      ,case when f.obj_type = 1 then '生意概况'
            when f.obj_type = 2 then '报表'
          when f.obj_type = 6 then '工具箱' end as obj_type
      ,case when f.obj_type = 1 then g.kpi_name
            when f.obj_type = 2 then h.name
          when f.obj_type = 6 then l.name end as obj_name
      ,case when f.obj_type = 2 then h.report_id
          when f.obj_type = 6 then l.report_id end as report_id  -- '报表id'      
      from sys_users as a
      inner join sys_user_roles as b on a.id = b.user_id
      inner join sys_roles as d on b.role_id = d.id
      inner join sys_user_groups as c on a.id = c.user_id
      inner join sys_groups as e on c.group_id = e.id
      inner join sys_role_resources f on f.role_id = b.role_id
      left join sys_kpi_bases as g on f.obj_id = g.kpi_id and f.obj_type = 1
      left join sys_analysis_bases as h on f.obj_id = h.group_id and f.obj_type = 2
      left join sys_app_bases as l on f.obj_id = l.group_id and f.obj_type = 6
      where 1 = 1 
        and a.user_num = var_user_num
      order by f.obj_type) as t1
  left join (select distinct report_id,report_table_name
               from sys_util_ods_report_relation
              where locate('main',report_table_name) > 0 
            ) as t2 on t1.report_id = t2.report_id;            

   open cur;
   read_loop: loop
   fetch cur into group_id1,report_table_name1;

   if done then
   leave read_loop;
   end if;
   -- select report_table_name1;
   set @sql_text := concat(
      'insert into tmp_obj_01(report_table_name,row_count) '
      ,'select ''',report_table_name1,''',count(*) as row_count from ',report_table_name1,' where group_id = ',group_id1,';');

    prepare stmt from @sql_text;
    execute stmt;
   end loop;
    
   close cur;    
  update tmp_obj as t1,tmp_obj_01 as t2
     set t1.row_count = t2.row_count
  where t1.report_table_name = t2.report_table_name;  
   
  select
    `user_id`           as '用户id',
    `user_name`         as '用户名',
    `user_num`          as '用户编号',
    `created_at`        as '创建时间',
    `role_id`           as '角色id',
    `role_name`         as '角色名',
    `group_id`          as '群组id',
    `group_name`        as '群组名',
    `obj_type`          as '菜单',
    `obj_name`          as '报表名',
    `report_id`         as '报表id',
    `report_table_name` as '数据表',
    `row_count`         as '数据行数'
    from tmp_obj;

  drop temporary table if exists tmp_obj; 
  drop temporary table if exists tmp_obj_01; 
end
;;
delimiter ;

-- -----------------------------------------------------
-- 查询用户生意概况页数据
--
-- @v_user_num 用户编号 
-- -----------------------------------------------------
drop procedure if exists pro_query_app_kpi;
delimiter ;;
create procedure pro_query_app_kpi(in var_user_num varchar(50))
begin
  select
     skb.kpi_group as '分组名称'
    ,skb.kpi_name  as '指标名称'
    ,skd.dim2      as '左 label'
    ,skd.keyword2  as '左值'
    ,skd.dim1      as '右 label'
    ,skd.keyword1  as '右值'
    ,case when skd.stick = 1 then '置顶' else '常规' end as '位置'
    ,case when skd.source_type = 'yh' then '实时接口' else '缓存数据' end as '数据类型'
  from sys_kpi_bases as skb
  left join sys_kpi_datas as skd on skb.kpi_id = skd.kpi_id
  where (skb.kpi_id in (
    select distinct srr.obj_id from sys_role_resources as srr
    where srr.obj_type = 1 and srr.role_id in (
    select distinct sr.id from sys_users as su
    left join sys_user_roles as sur on sur.user_id = su.id
    left join sys_roles as sr on sur.role_id = sr.id
    where su.user_num = var_user_num
    )
  ) or skb.publicly = 1)
  and skd.group_id in (
    select distinct sg.data_group_id from sys_users as su 
    left join sys_user_groups as sug on sug.user_id = su.id
    left join sys_groups as sg on sug.group_id = sg.id
    where su.user_num = var_user_num
  )
  order by skd.stick desc, skb.group_order desc, skb.item_order desc;
end
;;
delimiter ;

-- -----------------------------------------------------
-- @var_report_id 报表 ID
--
-- 结果1: 报表基本信息
-- 结果2: 该报表支持的群组列表（数据权限）
-- 结果3: 该报表的 ETL 刷新记录
-- -----------------------------------------------------
drop procedure if exists pro_query_report;
delimiter ;;
create procedure pro_query_report(in var_report_id integer)
begin
  select 
    report_id as '报表 ID'
    , template_id as '报表模板'
    , title as '报表标题'
    , refresh_type as '刷新类型'
    , created_at as '创建时间'
    , updated_at as '更新时间'
  from sys_template_reports where report_id = var_report_id;

  select
    report_id as '报表 ID'
    , template_id as '报表模板'
    , group_id as '群组 ID'
  from sys_group_reports where report_id = var_report_id;

  call pro_query_report_etl_history(var_report_id, 3);
end
;;
delimiter ;

-- -----------------------------------------------------
-- 查询某支报表 ETL 更新的列表
--
-- @var_report_id 用户编号 
-- @var_day       查询天数 
-- -----------------------------------------------------
drop procedure if exists pro_query_report_etl_history;
delimiter ;;
create procedure pro_query_report_etl_history(in var_report_id varchar(50),in var_day varchar(50))
begin
  select @row_index :=@row_index + 1 as 'row_index', t.*
  from (
    select
          t2.report_name       as 'report_title'
         ,t2.report_id
         ,t2.template_id
         ,date_format(t1.begin_time, '%y-%m-%d %H:%i:%s') as 'etl_begin_time'
         ,date_format(t1.end_time, '%y-%m-%d %H:%i:%s')   as 'etl_end_time'
         ,date_format(min(t2.begin_time), '%y-%m-%d %H:%i:%s') as 'cache_begin_time'
         ,date_format(max(t2.end_time), '%y-%m-%d %H:%i:%s')   as 'cache_end_time'
         ,date_format(sum(t2.duration), '%y-%m-%d %H:%i:%s')   as 'cache_duration'
      from sys_etl_log as t1
    left join sys_report_cache_action_logs as t2 on t1.end_time = t2.`timestamp`
    where locate('timestamp is not freshing',t1.procedure_name) = 0 
      and t1.report_id = var_report_id
      and timestampdiff(day,date(t1.begin_time),date(now())) <= (var_day-1)
      and t2.refresh_type <> 'ignore'
    group by t1.report_id,t1.begin_time
    order by t1.begin_time desc
  ) as t,
  (select @row_index := 0) as t2
  order by 'row_index' asc;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 查询所有报表 ETL 更新的列表
--
-- 维度一：报表数据表
-- 维度二：获取 ETL/缓存的最新刷新时间，与报表 ID 关联
-- 维度三：ETL/缓存表与维度二中的报表 ID 与时间戳关联
--
-- 说明：
-- 获取到的 ETL/缓存最新记录时间是相互独立的，此时才能客观地监控 ETL 与缓存时间流间隔
-- -----------------------------------------------------
drop procedure if exists pro_query_reports_etl_history;
delimiter ;;
create procedure pro_query_reports_etl_history()
begin
  select @row_index :=@row_index + 1 as 'row_index', t.*
  from (
    select
        str.title        as 'report_title'
      , str.report_id
      , str.template_id
      , str.refresh_type
      , date_format(sel.begin_time, '%y-%m-%d %H:%i:%s') as 'etl_begin_time'
      , date_format(sel.end_time, '%y-%m-%d %H:%i:%s') as 'etl_end_time'
      , timestampdiff(second, sel.begin_time, sel.end_time) as 'etl_duration'
      , date_format(srcal.begin_time, '%y-%m-%d %H:%i:%s') as 'cache_begin_time'
      , date_format(srcal.end_time, '%y-%m-%d %H:%i:%s') as 'cache_end_time'
      , timestampdiff(second, srcal.begin_time, srcal.end_time) as 'cache_duration'
      , timestampdiff(second, sel.end_time, srcal.begin_time)   as 'etl_cache_interval'
      , case when timestampdiff(second, sel.end_time, srcal.begin_time) < 90*60 then '正常' else '报警' end as 'state'
    from sys_template_reports as str
    left join (
      select report_id, max(begin_time) as timestamp
      from sys_report_cache_action_logs
      where refresh_type <> 'ignore' and datediff(now(), begin_time) <= 7
      group by report_id
    ) as srcal2 on srcal2.report_id = str.report_id
    left join sys_report_cache_action_logs as srcal on srcal.report_id = srcal2.report_id and srcal.begin_time = srcal2.timestamp
    left join (
      select report_id, max(begin_time) as timestamp
      from sys_etl_log
      where locate('timestamp is not freshing', procedure_name) = 0 and datediff(now(), begin_time) <= 7
      group by report_id
    ) as sel2 on sel2.report_id = str.report_id
    left join sys_etl_log as sel on sel.report_id = sel2.report_id and sel.begin_time = sel2.timestamp
    where srcal.refresh_type <> 'ignore'
    order by sel.begin_time desc, srcal.begin_time desc
  ) as t
  , (select @row_index := 0) as t2
  order by 'row_index' asc;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 清理过期的 ETL/Cache 记录
--
-- ETL/Cache 日志表中，每支报表分别仅保留最近 50 条记录
-- 目的：为上述查询的存储过程提高性能
-- -----------------------------------------------------
drop procedure if exists pro_refresh_by_day_clean_etl_and_cache_log;
delimiter ;;
create procedure pro_refresh_by_day_clean_etl_and_cache_log()
begin
  declare var_report_id integer;

  declare done integer default false;
  declare cur cursor for select distinct report_id from sys_template_reports;
  declare continue handler for sqlstate '02000' set done = true;

  open cur;
  fetch cur into var_report_id;
  repeat 

    -- 每支报表的 ETL 记录仅保留 50 条
    delete from sys_etl_log where id in (
      select t0.id from (
        select id
        from sys_etl_log
        where report_id = var_report_id
        order by id desc
        limit 10000 offset 50
      ) as t0
    );

    -- 每支报表的 Cache 记录仅保留 50 条
    delete from sys_report_cache_action_logs where id in (
      select t1.id from (
        select id
        from sys_report_cache_action_logs
        where report_id = var_report_id
        order by id desc
        limit 10000 offset 50
      ) as t1 
    );

    fetch cur into var_report_id;
  until done end repeat;
  close cur;
  
  select report_id, count(id) as etl_count from sys_etl_log group by report_id order by etl_count desc;
  select report_id, count(id) as cache_count from sys_report_cache_action_logs group by report_id order by cache_count desc;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 校正 KPI/ETL/CACHE 监控数据流
-- -----------------------------------------------------
drop procedure if exists pro_sys_optimized_report_cache_refresh_constraints;
delimiter ;;
create procedure pro_sys_optimized_report_cache_refresh_constraints()
begin
  alter table sys_report_cache_refresh_constraints modify kpi_refresh_limit integer default 0;
  update sys_report_cache_refresh_constraints set kpi_refresh_limit = 0;
  alter table sys_report_cache_refresh_constraints modify kpi_execute_limit integer default 0;
  update sys_report_cache_refresh_constraints set kpi_execute_limit = 0;
  alter table sys_report_cache_refresh_constraints modify etl_refresh_limit integer default 0;
  update sys_report_cache_refresh_constraints set etl_refresh_limit = 0;
  alter table sys_report_cache_refresh_constraints modify etl_execute_limit integer default 0;
  update sys_report_cache_refresh_constraints set etl_execute_limit = 0;
  alter table sys_report_cache_refresh_constraints modify cache_refresh_limit integer default 0;
  update sys_report_cache_refresh_constraints set cache_refresh_limit = 0;
  alter table sys_report_cache_refresh_constraints modify cache_execute_limit integer default 0;
  update sys_report_cache_refresh_constraints set cache_execute_limit = 0;
end
;;
delimiter ;
-- call pro_sys_optimized_report_cache_refresh_constraints();

-- -----------------------------------------------------
-- 查询胜因角色（或不是）的用户列表
--
-- @in_or_not true/false
-- -----------------------------------------------------
drop procedure if exists pro_query_intfocus_users;
delimiter ;;
create procedure pro_query_intfocus_users(in in_or_not tinyint(1))
begin
    declare intfocus_users varchar(1000);
    set intfocus_users = 'admin,18221013858,13564379606,13585697734,18521362963,13073220733,13701858934,17621200351,13913778859,15278618270,18367053600,15205608978,15821040262,18895332232';

    if in_or_not = 1 then
      select id, user_name, user_num from sys_users 
      where id in (
        select user_id from sys_user_roles where role_id = 99
      );
    else
      select id, user_name, user_num from sys_users 
      where id in (
        select user_id from sys_user_roles where role_id = 99
      ) and not find_in_set(user_num,intfocus_users);
    end if;
end
;;
delimiter ;

-- -----------------------------------------------------
-- 清理非胜因角色的用户（离职同事）
-- -----------------------------------------------------
drop procedure if exists pro_clean_intfocus_users;
delimiter ;;
create procedure pro_clean_intfocus_users()
begin
    declare intfocus_users varchar(1000);
    set intfocus_users = 'admin,18221013858,13564379606,13585697734,18521362963,13073220733,13701858934,17621200351,13913778859,15278618270,18367053600,15205608978,15821040262,18895332232';

  delete from sys_users 
  where id in (
    select user_id from sys_user_roles where role_id = 99
  ) and not find_in_set(user_num,intfocus_users);


  call pro_query_intfocus_users(true);
end
;;
delimiter ;


-- -----------------------------------------------------
-- 创建报表数据表
-- -----------------------------------------------------
drop procedure if exists pro_create_report_data_table;
delimiter ;;
create procedure pro_create_report_data_table(in par_report_id integer, in par_type varchar(100))
begin
    declare var_report_id varchar(10);
    declare var_table_name varchar(100);

    set var_report_id = case 
    when length(par_report_id) = 1 then concat('00', par_report_id)
    when length(par_report_id) = 2 then concat('0', par_report_id) 
    else par_report_id
    end;
    set var_table_name = concat('report_data_', var_report_id, '_', par_type);

    select var_table_name as table_name;

    set @sql = concat(
    'CREATE TABLE `', var_table_name, '` (
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
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;'
  );

  prepare stmt from @sql;
  execute stmt;
  deallocate prepare stmt;
end
;;
delimiter ;
```