---
layout: post
title: PostgreSQL之C语言函数
categories: PostgreSQL
description: PostgreSQL之C语言函数 
keywords: PostgreSQL
---
# 参考
[language-c](https://www.postgresql.org/docs/8.3/xfunc-c.html)

# 实例
## C语言实现PG的函数功能
### 1.cd $PG_HOME
### 2.编写c语言函数add_func.c
```c
//运行绝大多数c代码所需要的基础定义和声明
#include "postgres.h"
 
//包含所使用的PG_*宏的定义
#include "fmgr.h"
 
//确保服务器不会加载不同postrges版本所编译的代码
PG_MODULE_MAGIC;
 
//将函数add_db定义成符合version1版本的函数
PG_FUNCTION_INFO_V1(add_ab);
 
Datum /*postgres函数的返回类型*/ add_ab(PG_FUNCTION_ARGS /*参数的任意数值*/)
{
        int32 arg_a=PG_GETARG_INT32(0);
        int32 arg_b=PG_GETARG_INT32(1);
        PG_RETURN_INT32(arg_a + arg_b);
}
```
 
### 3.编写编译文件Makfile
```
MODULES = add_func
PG_CONFIG=pg_config
 
PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)
```
 
### 4.在用户下创建函数
```sql
CREATE OR REPLACE FUNCTION add(integer, integer)
  RETURNS integer AS
'/data/pgsql/lib/add_func', 'add_ab'
  LANGUAGE c VOLATILE STRICT
;
```
 
### 5.测试
```sql
select add(1,2);
```

## C语言实现PG的函数功能2之null值处理
### 1.cd $PG_HOME
### 2.编写c语言函数test.c
```c
//运行绝大多数c代码所需要的基础定义和声明
#include "postgres.h"
 
//包含所使用的PG_*宏的定义
#include "fmgr.h"
 
//确保服务器不会加载不同postrges版本所编译的代码
PG_MODULE_MAGIC;
 
//将函数add_db定义成符合version1版本的函数
PG_FUNCTION_INFO_V1(add_ab_null);
 
Datum /*postgres函数的返回类型*/ add_ab_null(PG_FUNCTION_ARGS /*参数的任意数值*/)
{
        int32 not_null=0;
        int32 sum=0;
        if (!PG_ARGISNULL(0)){
                sum += PG_GETARG_INT32(0);
                not_null=1;
        }
 
        if (!PG_ARGISNULL(1)){
                sum += PG_GETARG_INT32(1);
                not_null=1;
        }
 
        if (not_null){
                PG_RETURN_INT32(sum);
        }
        PG_RETURN_NULL();
}
```
 
### 3.编写编译文件Makfile
```c
MODULES = test
PG_CONFIG=pg_config
 
PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)
```
 
### 4.在用户下创建函数
```sql
CREATE OR REPLACE FUNCTION add_null(integer, integer)
  RETURNS integer AS
'/data/pgsql/lib/test', 'add_ab_null'
  LANGUAGE c ;
```
 
### 5.测试
```sql
select add_null(99999999,8);
```

## C语言实现PG的函数功能3之数组实现
### 1.cd $PG_HOME
### 2.编写c语言函数add_int32_array.c
```c
//运行绝大多数c代码所需要的基础定义和声明
#include "postgres.h"
//包含所使用的PG_*宏的定义
#include "fmgr.h"
//数组需要的库文件
#include "utils/array.h"
#include "catalog/pg_type.h"
//确保服务器不会加载不同postrges版本所编译的代码
PG_MODULE_MAGIC;
//将函数add_db定义成符合version1版本的函数
PG_FUNCTION_INFO_V1(add_int32_array);
Datum add_int32_array(PG_FUNCTION_ARGS)
{
        //定义一个指针指向你的数组
        ArrayType *input_array;
        int32 sum=0;
        bool not_null=false;
        //记录从deconstruct_array函数中返回数组的信息   
        Datum *datums;
        bool *nulls;
        int count;
        int i;
        //获取数组的指针
        input_array=PG_GETARG_ARRAYTYPE_P(0);
        Assert(ARR_ELEMTYPE(input_array) == INT4OID);
        //判断数组是否为一维数组        
        if (ARR_NDIM(input_array)>1)
                //非一维数组报错
                ereport(ERROR,(errcode(ERRCODE_ARRAY_SUBSCRIPT_ERROR),errmsg("1-dimensional array needed")));
 
        deconstruct_array(input_array,INT4OID,4,true,'i',&datums,&nulls,&count);
        //将数组中的所有not null的元素相加
        for(i=0;i<count;i++){
                if(nulls[i])
                        continue;
                sum += DatumGetInt32(datums[i]);
                not_null=true;
        }
        if(not_null)
                PG_RETURN_INT32(sum);
        PG_RETURN_NULL();
}
```
### 3.编写编译文件Makfile
```c
MODULES = add_int32_array
PG_CONFIG=pg_config
 
PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)
```
 
### 4.在用户下创建函数
```sql
CREATE OR REPLACE FUNCTION add_arr(int[])
  RETURNS integer AS
'/data/pgsql/lib/add_int32_array', 'add_int32_array'
  LANGUAGE c strict;
```
 
### 5.测试
```sql
select add_arr('{1,null,3,4,5,6,7,8,9}');
```
 
 
