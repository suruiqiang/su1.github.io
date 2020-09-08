---
layout: post
title: HAWQ之oracle兼容函数orafunc
categories: HAWQ
description: HAWQ之oracle兼容函数orafunc
keywords: HAWQ
---
## 参考
```
https://docs.oracle.com/en/database/oracle/oracle-database/18/sqlrf/ABS.html#GUID-D8D3489A-44EA-4FEC-A6F0-B5E312FFC231
```

## 测试的软件版本
```
PostgreSQL 8.2.15 
(OushuDB 3.4.0.0) 
(Apache HAWQ 2.4.0.0) 
(Greenplum Database 4.2.0 build 1) 
on x86_64-unknown-linux-gnu, 
compiled by GCC clang version 8.0.1 (tags/RELEASE_801/final) 
compiled on Jan 15 2020 05:46:01
```
## 安装方法

```
psql dw -f $GPHOME/share/postgresql/contrib/orafunc.sql
```

## 函数功能解析







|名字|例子|功能|
|:-|-|-|
|nvl|select oracompat.nvl(NULL,'1'::text)|等价PostgreSQL的SELECT coalesce(NULL,'1'::text)|
|add_months|select oracompat.add_months('2020-05-25'::date,1);|增加月份|
|last_day|select oracompat.last_day('2020-05-15'::date);|返回当前日期所在月份的最后一天的当前时间|
|next_day|select oracompat.next_day('2020-05-01'::date,'SUNDAY');<br />select oracompat.next_day('2020-05-01'::date,1);|获得当前日期的下一个星期几的日期:<br /> SUNDAY(1), <br />MONDAY(2), <br />TUESDAY(3), <br />WEDNESDAY(4), <br />THURSDAY(5), <br />FRIDAY(6), <br />SATURDAY(7)|
|months_between|select oracompat.months_between('2020-10-01'::date,'2020-02-01'::date)|MONTHS_BETWEEN函数返回两个日期之间的月份数|
|trunc|--## 返回当前日期(YYYY-MM-DD)<br />select oracompat.trunc(now()::date ); <br /><br />--## 返回当前日期(YYYY-MM-DD-HH-MI)<br />select oracompat.trunc(now(),'MI'  ); <br /><br />--## 返回当前日期(YYYY-MM-DD-HH)<br /> select oracompat.trunc(now(),'HH'  ); <br /><br />--## 返回当前星期的第一天<br /> select oracompat.trunc(now(),'D'   ); <br /><br />--## 返回当前日期(YYYY)<br /> select oracompat.trunc(now(),'YYYY'); <br /><br />--## 返回当前日期(YY)<br /> select oracompat.trunc(now(),'YY'  ); <br /><br />--## 返回当前日期(YYYY-MM)<br /> select oracompat.trunc(now(),'MM'  ); <br /><br />--## 返回当前日期(YYYY-MM-DD)<br /> select oracompat.trunc(now(),'DD'  );|截断日期|
|round|select oracompat.round(now()::date);<br />select oracompat.round(now(),'year');<br />select oracompat.round(now(),'month');<br />select oracompat.round(now(),'day');|日期四舍五入函数|
|instr|--## 返回结果：3    默认第一次出现“l”的位置 <br />select oracompat.instr('helloworld','l') ;<br /><br />--## 返回结果：4    即：在“lo”中，“l”开始出现的位置<br />select oracompat.instr('helloworld','lo') ;<br /><br />--## 返回结果：6    即“w”开始出现的位置<br />select oracompat.instr('helloworld','wo') ; <br /><br />--## 返回结果：4    也就是说：在"helloworld"的第2(e)号位置开始，查找第二次出现的“l”的位置<br />select oracompat.instr('helloworld','l',2,2); <br /><br />--## 返回结果：4    也就是说：在"helloworld"的第3(l)号位置开始，查找第二次出现的“l”的位置<br />select oracompat.instr('helloworld','l',3,2); <br /><br />--## 返回结果：9    也就是说：在"helloworld"的第4(l)号位置开始，查找第二次出现的“l”的位置<br />select oracompat.instr('helloworld','l',4,2);<br /><br />--## 返回结果：9    也就是说：在"helloworld"的倒数第1(d)号位置开始，往回查找第一次出现的“l”的位置<br />select oracompat.instr('helloworld','l',-1,1);<br /><br />--## 返回结果：4    也就是说：在"helloworld"的倒数第1(d)号位置开始，往回查找第二次出现的“l”的位置<br />select oracompat.instr('helloworld','l',-2,2); <br /><br />--## 返回结果：9    也就是说：在"helloworld"的第2(e)号位置开始，查找第三次出现的“l”的位置<br />select oracompat.instr('helloworld','l',2,3) ;<br /><br />--## 返回结果：3    也就是说：在"helloworld"的倒数第2(l)号位置开始，往回查找第三次出现的“l”的位置<br />select oracompat.instr('helloworld','l',-2,3);|返回要截取的字符串在源字符串中的位置<br />格式一：instr( string1, string2 )   <br /> 或  instr(源字符串, 目标字符串)<br />格式二：instr( string1, string2 [, start_position [, nth_appearance ] ] )  <br /> 或  instr(源字符串, 目标字符串, 起始位置, 匹配序号)|
|reverse|select oracompat.reverse(123456) ;<br /><br />select oracompat.reverse('人生几何对酒当歌');<br /><br />select oracompat.reverse('123456',3,5);|将一个对象反向转换;针对数据库内部存储的对象编码进行反转的|
| concat | select oracompat.concat('aa','1.23'::float);<br />select oracompat.concat('aa','bb'); | 连接两个字符串 |
|NANVL | select oracompat.nanvl('1.23',1);<br />select oracompat.nanvl('NaN',1);<br />select oracompat.nanvl('nan',1); |[ **select nanvl(a2,a1)**  ] ;NANVL函数仅对BINARY_FLOAT或BINARY_DOUBLE类型的浮点数有用。如果输入值n2是NaN（不是数字），它指示Oracle数据库返回一个可选值n1。如果n2不是NaN，则Oracle返回n2。|
|BITAND|SELECT oracompat.BITAND(6,3);|两个数值型数值在按位进行AND运算;<br />等价PostgreSQL的select 6 & 3;|
|listagg1_transfn| -|-|
|listagg2_transfn|-|-|
|NVL2|select oracompat.nvl2(NULL,'y'::text,'n'::text);<br />select oracompat.nvl2('','y'::text,'n'::text);<br />select oracompat.nvl2('1','y'::text,'n'::text);|nvl2()(E1, E2, E3)的功能为：如果E1为NULL，则函数返回E3，若E1不为null，则返回E2|
|LNNVL|-|-|
|DUMP|select oracompat.dump('Tech');<br />select oracompat.dump('Tech', 10) ;<br />select oracompat.dump('Tech', 16) ;|返回一个varchar2值，这个值包含了数据类型代码、字节长度和表达式的内部表示形式<br />https://wiki.imooc.com/oracle/dump.html|
|NLSSORT|SELECT * FROM test <br />ORDER BY oracompat.NLSSORT(name,'zh_CN.utf8');<br />SELECT * FROM test <br />ORDER BY oracompat.NLSSORT(name,'en_US.utf8');<br />SELECT * FROM test <br />ORDER BY oracompat.NLSSORT(name,'zh_CN.gb18030');|NLSSORT返回字符值char的排序规则键和显式或隐式指定的排序规则。<br />排序规则键是一个用于根据指定的排序规则对char进行排序的字节字符串。<br />排序规则键的属性是：<br />    按二进制比较由给定的排序规则生成的两个排序键的相互排序和按给定的排序规则比较源字符值的相互排序相同|
|SUBSTR|SELECT oracompat.substr('ABCDEFG',0,3) ;<br />SELECT oracompat.substr('ABCDEFG',1,3) ;<br />SELECT oracompat.substr('ABCDEFG',2,3) ;<br />SELECT oracompat.substr('ABCDEFG',-1,3);|取得字符串中指定起始位置和长度的字符串|



