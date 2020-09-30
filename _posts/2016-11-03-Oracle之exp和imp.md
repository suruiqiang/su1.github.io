---
layout: post
title: Oracle之exp和imp 
categories: Oracle
description: Oracle之exp和imp
keywords: Oracle
---
# exp/imp工具
执行环境:sqlplus
## exp导出命令
```
exp 用户名/密码@库名 full=y file=d:/xxx.dmp log=xxx.log;
	其中full=y 表示全库导出，缺省情况下只会将该用户下的对象导出
	 
exp 用户名/密码@库名 file=d:/xxx.dmp owner=xx,yy log=xxx.log;
	其中ower=xx只能备份指定用户的对象
	 
exp 用户名/密码@库名 tables=emp,empppp file=d:/xxx.dmp log=xxx.log;
	其中tables=emp表示备份相关表
	 
exp 用户名/密码@库名 tables=(emp) file=d:/xxx.dmp log=xxx.log;
	query=\"where oper_id like '00%'\"        
	这是将表emp中的字段oper_id以"00"打头的数据导出
```
## imp导入命令
```
将用户名相同的库中的数据导入指定的服务器相同的用户下
imp 用户名/密码@库名 full=y file=d:/xxx.dmp log=xxx.log;
		
将原用户kf touser的数据导入到用户kf_new下
imp kf_new/zx@zxcc_new file=d:\zxcc.dmp fromuser=kf touser=kf_new;
    其中fromuser=kf为.dmp文件里的对象的原先的ower，
	而touser=kf_new为作为导入的对象的新的ower
	 
imp kf_new/zx@zxcc_new file=d:\zxcc.dmp fromuser=kf touser=kf_new ignore=y;
	其中ignore=y忽略错误
```
