---
layout: post
title: PostgrSQL之秒与日期的互转
categories: PostgrSQL
description: PostgrSQL之秒与日期的互转
keywords: PostgrSQL
---

# 秒 ==> 日期('yyyymmdd')
```sql
select to_char((timestamp with time zone 'epoch'+绝对秒数 * interval '1 second'),'yyyymmdd');    
或
select round(extract(epoch from current_timestamp::timestamp with time zone));
```

# 日期 ==> 秒
```sql
select extract(epoch from current_date);
```
