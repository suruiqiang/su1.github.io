---
layout: post
title: Oracle之JOB 
categories: Oracle
description: Oracle之JOB
keywords: Oracle
---

# JOB参数说明
```
dbms_scheduler
							==>>create_job
							==>>drop_job

FREQ:用来指定间隔的时间周期
参数:
		YEARLY    年
		MONTHLY   月
		WEEKLY    周
		DAILY     日
		HOURLY    时
		MINUTELY  分
		SECONDLY  秒

指定频率
INTERVAL,指定间隔的频繁
 
BySecond  : 指定一天中的秒速 0-59
BYHOUR    : 指定一天中的小时 1-24
BYDAY     ：指定每周那天运行 (MON,TUE,WED,THU,FRI,SAT,SUN)
BYMONTHDAY：指定每个月中的哪一天。-1表示每个月的最后一天
BYMONTH   ：指定每年的月份
BYDATE    ：指定日期。 0310表示3月10日
```

# JOB的执行各种执行汇总
## 每小时每隔5分钟执行一次
```
begin
  dbms_scheduler.create_job
  (
    job_name        => 'JOB_TEST_TEST',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'begin proc_test_test; end;',
    repeat_interval => 'FREQ=HOURLY; BYMINUTE=05,10,15,10,15,20,25',
    enabled         => true
  );
end;
```
 
## 每天的23点5分59秒执行一次
```
begin
  dbms_scheduler.create_job
  (
    job_name        => 'JOB_TEST_TEST',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'begin proc_test_test; end;',
    repeat_interval => 'FREQ=DAILY;BYHOUR=23;BYMINUTE=05;BySecond=59',
    enabled         => true
  );
end;
```

## 每周一的23点5分59秒执行一次
```
begin
  dbms_scheduler.create_job
  (
    job_name        => 'JOB_TEST_TEST',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'begin proc_test_test; end;',
    repeat_interval => 'FREQ=WEEKLY;BYDAY=MON;BYHOUR=23;BYMINUTE=05;BySecond=59',
    enabled         => true
  );
end;
```
 
## 每月的第六天的23点5分59秒执行一次
```
begin
  dbms_scheduler.create_job
  (
    job_name        => 'JOB_TEST_TEST',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'begin proc_test_test; end;',
    repeat_interval => 'FREQ=MONTHLY;BYMONTHDAY=6;BYHOUR=23;BYMINUTE=05;BySecond=59',
    enabled         => true
  );
end;
```
 
## 每年1月份的第六天的23点5分59秒执行一次
```
begin
  dbms_scheduler.create_job
  (
    job_name        => 'JOB_TEST_TEST',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'begin proc_test_test; end;',
    repeat_interval => 'FREQ=YEARLY;BYMONTH=1;BYMONTHDAY=6;BYHOUR=23;BYMINUTE=05;BySecond=59',
    enabled         => true
  );
end;
```
 
## 每隔5分钟执行一次
```
begin
  dbms_scheduler.create_job
  (
    job_name        => 'JOB_TEST_TEST',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'begin proc_test_test; end;',
    repeat_interval => 'FREQ=MINUTELY; interval=5',
    enabled         => true
  );
end;
 
/*
注意:
FREQ=HOURLY ; interval=5   --表示每隔5小时执行一次
FREQ=DAILY  ; interval=5   --表示每隔5天执行一次
FREQ=WEEKLY ; interval=5   --表示每隔5周执行一次
FREQ=MONTHLY; interval=5   --表示每隔5月执行一次
FREQ=YEARLY ; interval=5   --表示每隔5年执行一次
*/
```
 
 
# 运行job
```
begin
  dbms_scheduler.run_job(job_name => 'JOB_TEST_TEST'); 
end;
```

# 停止正在运行的job 
```
begin
  dbms_scheduler.stop_job(job_name => 'JOB_TEST_TEST'); 
end;
```
  
# 删除job
```
begin
  dbms_scheduler.drop_job(job_name => 'JOB_TEST_TEST'); 
end;
```

# 查询job的运行情况
```
select * from all_scheduler_job_run_details;
select * from all_scheduler_job_log;
```
 

