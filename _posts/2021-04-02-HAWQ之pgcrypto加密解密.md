---
layout: post
title: HAWQ之pgcrypto加密解密
categories: HAWQ
description: HAWQ之pgcrypto加密解密
keywords: HAWQ
---

[toc]

# 参考

[PGCRYPTO](https://www.postgresql.org/docs/8.3/pgcrypto.html)

[固若金汤 - PostgreSQL pgcrypto加密插件](https://developer.aliyun.com/article/58377)

[Postgresql数据加密函数介绍](https://blog.csdn.net/dazuiba008/article/details/79641321)



# pgcrypto安装

```shell
## 安装
psql -d <DBNAME> -c 'create schema crypto'

sed 's/SET search_path = public/SET search_path = crypto/g' $GPHOME/share/postgresql/contrib/pgcrypto.sql  | psql  <DBNAME>

## 简单验证
### 加密
select crypto.pgp_sym_encrypt('This is HAWQ', 'password');

### 解密
select crypto.pgp_sym_decrypt(
      crypto.pgp_sym_encrypt('This is HAWQ', 'password'),
      'password'
);
```



# 功能介绍

## digest()

根据给定的算法获取给定数据的hash值。<br /> 标准算法支持有md5、sha1、sha224、sha256、sha384和sha512<br />e.g.

```SQL
select crypto.digest('This is HAWQ', 'md5');
```



## hmac()

用key计算hash值，type和digest一样，hmac和digest类似，但是只有知道key的情况下才能计算出哈希值，
这样可以预防更改数据以及更改哈希匹配的情况，如果key大于hash block size，那么先计算哈希值，哈希值作为key使用<br />e.g.

```sql
select crypto.hmac('This is HAWQ', 'This is key','md5');
```



## 密码哈希函数

**crypt()用来计算hash值.<br />gen_salt()随机产生一个值作为crypt()的算法参数.**<br />gen_salt()的type参数为des, xdes, md5, bf.   <br />gen_salt()的iter_count指迭代次数, 数字越大加密时间越长, 被破解需要的时间也越长.<br />crypt()和gen_salt()的组合主要是提高了逆向破解的难度, 增强了数据的安全性

### crypt()支持的算法

| 算法 | 最大密码长度 | 适应? | Salt bits | 描述                       |
| ---- | ------------ | ----- | --------- | -------------------------- |
| bf   | 72           | Yes   | 128       | Blowfish-based, variant 2a |
| md5  | unlimited    | No    | 48        | MD5-based crypt            |
| xdes | 8            | Yes   | 24        | Extended DES               |
| des  | 8            | No    | 12        | Original UNIX crypt        |

### crypt()迭代次数

| 算法 | 默认 | 最小值 | 最大值   |
| ---- | ---- | ------ | -------- |
| xdes | 725  | 1      | 16777215 |
| bf   | 8    | 4      | 31       |

- xdes额外限制，只能是奇数

### hash算法速度

| 算法         | Hashes/sec | For `[a-z]` | For `[A-Za-z0-9]` |
| ------------ | ---------- | ----------- | ----------------- |
| `crypt-bf/8` | 28         | 246 years   | 251322 years      |
| `crypt-bf/7` | 57         | 121 years   | 123457 years      |
| `crypt-bf/6` | 112        | 62 years    | 62831 years       |
| `crypt-bf/5` | 211        | 33 years    | 33351 years       |
| `crypt-md5`  | 2681       | 2.6 years   | 2625 years        |
| `crypt-des`  | 362837     | 7 days      | 19 years          |
| `sha1`       | 590223     | 4 days      | 12 years          |
| `md5`        | 2345086    | 1 day       | 3 years           |

**crypt和gen_salt是以牺牲hash速度为代价来换取安全性的**

e.g.

```shell
## 根据salt获取password对应的hash值
dw=# select crypto.crypt('password', 'salt');
     crypt
---------------
 sa3tHJ3/KuYvI
(1 row)

## password + hash获取对应的hash值
dw=# select crypto.crypt('password', 'sa3tHJ3/KuYvI');
     crypt
---------------
 sa3tHJ3/KuYvI
(1 row)

--------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------

# 例子
## 原始数据
-- 原始表
dw=# create table s_test(id int,name text);
dw=# insert into s_test values(1,'This is HAWQ');
dw=# select * from s_test;
 id |     name
----+--------------
  1 | This is HAWQ
(1 row)

insert into s_test (id,name) values (2, crypto.crypt('password', crypto.gen_salt('bf',10)));
INSERT 0 1

## 加密后的数据
dw=# select * from s_test;
 id |                             name
----+--------------------------------------------------------------
  1 | This is HAWQ
  2 | $2a$10$qVsnbCuy2z102e9vKa/bfugyDmEUzt5AUzoRNVApQf31iOySx7mgu
(2 rows)

## error password <> password 返回false
dw=# select crypto.crypt('error password', name)=name from s_test where id = 2;
 ?column?
----------
 f
(1 row)

## password == password 返回true
dw=# select crypto.crypt('password', name)=name from s_test where id = 2;
 ?column?
----------
 t
(1 row)
dw=#
```



## PGP 加密函数

该功能实现了部分OpenPGP (RFC 4880)标准的加密。支持**对称秘钥**和**公共秘钥**的加密。

一条加密的PGP消息包含2个部分，或数据包：

- 数据包包含一个会话秘钥—加密了的对称秘钥或者是公共秘钥。
- 数据包包含带有会话秘钥的加密数据。

### 公共秘钥

#### pgp_key_id()

`pgp_key_id`抽取一个 PGP 公钥或私钥的密钥 ID。或者如果给定了一个加密过的消息，它给出一个用来加密数据的密钥 ID。

它能够返回 2 个特殊密钥 ID：

- `SYMKEY`

  该消息是用一个对称密钥加密的。

- `ANYKEY`

  该消息是用公钥加密的，但是密钥 ID 已经被移除。这意味着你将需要尝试你所有的密钥来看看哪个能解密该消息。`pgcrypto`本身不产生这样的消息。

注意不同的密钥可能具有相同的 ID。这很少见但是是一种正常事件。客户端应用则应该尝试用每一个去解密，看看哪个合适 — 像处理`ANYKEY`一样

#### armor(), dearmor()

这些函数把二进制数据包装/解包成 PGP ASCII-armored 格式，其基本上是带有 CRC 和额外格式化的 Base64。

#### pgp_pub_encrypt()

用一个公共 PGP 密钥 `key`加密`data`。给这个函数一个私钥会产生一个错误。<br />`options`参数可以包含下文所述的选项设置

#### pgp_pub_decrypt()

解密一个公共密钥加密的消息。`key`必须是对应于用来加密的公钥的私钥。如果私钥是用口令保护的，你必须在`psw`中给出该口令。如果没有口令，但你想要指定选项，你需要给出一个空口令。

不允许使用`pgp_pub_decrypt`解密`bytea`数据。这是为了避免输出非法的字符数据。使用`pgp_pub_decrypt_bytea`解密原始文本数据是好的。

`options`参数可以包含下文所述的选项设置。

#### 使用举例

```shell
## 生成公钥和密钥
[xx]# gpg --gen-key
gpg (GnuPG) 2.0.22; Copyright (C) 2013 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

请选择您要使用的密钥种类：
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (仅用于签名)
   (4) RSA (仅用于签名)
您的选择？ 1
RSA 密钥长度应在 1024 位与 4096 位之间。
您想要用多大的密钥尺寸？(2048)
您所要求的密钥尺寸是 2048 位
请设定这把密钥的有效期限。
         0 = 密钥永不过期
      <n>  = 密钥在 n 天后过期
      <n>w = 密钥在 n 周后过期
      <n>m = 密钥在 n 月后过期
      <n>y = 密钥在 n 年后过期
密钥的有效期限是？(0)
密钥永远不会过期
以上正确吗？(y/n)y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

真实姓名：feifeifei
电子邮件地址：
注释：feifeifei
您选定了这个用户标识：
    “feifeifei (feifeifei)”

更改姓名(N)、注释(C)、电子邮件地址(E)或确定(O)/退出(Q)？O
您需要一个密码来保护您的私钥。

我们需要生成大量的随机字节。这个时候您可以多做些琐事(像是敲打键盘、移动
鼠标、读写硬盘之类的)，这会让随机数字发生器有更好的机会获得足够的熵数。
我们需要生成大量的随机字节。这个时候您可以多做些琐事(像是敲打键盘、移动
鼠标、读写硬盘之类的)，这会让随机数字发生器有更好的机会获得足够的熵数。
gpg: 密钥 512675A3 被标记为绝对信任
公钥和私钥已经生成并经签名。

gpg: 正在检查信任度数据库
gpg: 需要 3 份勉强信任和 1 份完全信任，PGP 信任模型
gpg: 深度：0 有效性：  2 已签名：  0 信任度：0-，0q，0n，0m，0f，2u
pub   2048R/512675A3 2021-03-29
密钥指纹 = 34AE 3E3D C0FE 99CA EA3D  4448 F5DD 1206 5126 75A3
uid                  feifeifei (feifeifei)
sub   2048R/1A6C562B 2021-03-29

## 剔除密码
[xx]# gpg  --passwd feifeifei
gpg (GnuPG) 2.0.22; Copyright (C) 2013 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

密钥受保护。

您需要输入密码，才能解开这个用户的私钥：“feifeifei (feifeifei)”
2048 位的 RSA 密钥，钥匙号 512675A3，建立于 2021-03-29

输入要给这把私钥用的新密码。

您不想要用密码――这大概是个坏主意！

您真的想要这么做吗？(y/N)y

## 查看钥匙串
[xx]# gpg --list-secret-keys
/root/.gnupg/secring.gpg
------------------------
sec   2048R/512675A3 2021-03-29
uid                  feifeifei (feifeifei)
ssb   2048R/1A6C562B 2021-03-29


## 导出公钥
[xx]# gpg -a --export feifeifei > public.key

## 导出私钥
[xx]# gpg -a --export-secret-keys feifeifei > secret.key
[xx]#


## 测试
### ID=1存放公钥；ID=2存放私钥
create table keys(id int,name text,pkey bytea);

### 将钥匙串导入TABLE( keys )
insert into keys
select 1,'公钥',crypto.dearmor('-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v2.0.22 (GNU/Linux)

mQENBGBiAL8BCAC9GRWDzTyIxEgEgxY0UtgiuxwA4w3dlV5O/iGZ71zzAkpAs7fY
ZfTattp/5KwoAkhR3WRzZS89gQhDkR67Orqw67t45giH65M4f294urOTguyonXtc
yFTigsHQ5aV5vTOuP/1EarAxb+LV7GUGN4BDDS7nWmpqiPwQ/y8CmgocEf8a6ilc
+6/tedUWX0RGylkMhMGmjXsEG171KYsMYDHZEJYVEAF+3HsarYO/05BcBOaW4pbg
MOiJredSrR9FIKpf4xAlyWITn1tP9m8qQd7JmHMXUUPB4Jq6tK6gEqZX8LZLl4np
SAe0MFSwZFPnND5rOghcReJQwqgMflhQ6KCvABEBAAG0FWZlaWZlaWZlaSAoZmVp
ZmVpZmVpKYkBOQQTAQIAIwUCYGIAvwIbAwcLCQgHAwIBBhUIAgkKCwQWAgMBAh4B
AheAAAoJEPXdEgZRJnWjvyYH/ik4oUs/Mc0M188ELxvbsQkhFUN0NiZHWtNrGnKR
NjsP/vnZERYV7ZncdMXjTBzEdPv6hrB+nl/v3C3wQm9kLi3PoI2gpE6Xr9F2/KTJ
qyXPuKUKso3CyHYRt26dFziUSK3qzeLjHtVGmavavhhuuV1bMUhbvyUxa9UbhyzC
oGxoMKViPhV91zp4bUJSvA8+arjNJuSMAKcqN8o15kQS5HTACunpAFRlhJHqRM0s
wE3AXiov2ffx7oGklD/P12K0kbd2RbBa2YjYkV3E7+IB1O1+JxaTNxjNv1dQRh8G
pnYB9GYUpqpKFoHiEefQ0npmXjfWrOFZuMAxCi1cbzgurB+5AQ0EYGIAvwEIALaY
y+Bk+lfB5+UFnWDGJPY9SUP+3WS5l21Gsyvd2QLF9AxpTQKkCxMHB3CnzBGQLX73
d+ycemVx5tm1CoY1NfxeOwBi8cs1QPJD2IyvdpcZtdDXMZ4qxXKm5IpnDWsoJdjg
9XLt9tu06NR3DyinpV0PxKSw3w/rFGj/96vJ57VWsb11yub4sGAy7QAyIpCTsbIg
muhcaejTecUTDFOV7YlA2DhGhqoYQUtk2Jyi68qSWIW+NVCg0jLuEc6t9NYmlbnY
m3XLHK+/TaFOxoSYssk9gXVWOMdhKEifRUWFwL20rDpYchFNLdr1xP311BIYZBYV
aqd7SIUCwLubjpNxcokAEQEAAYkBHwQYAQIACQUCYGIAvwIbDAAKCRD13RIGUSZ1
owxRCACqPEa1L9zetLwW/yuDuR/h+76qEmQwHmPOo8xUromtiDcZaOAHYCRkJ5wz
4CnHazYZBrGfcMiuFVwwguvHYi0mb29rh3ZvmF+NGTw6PkNbt9Rh7bjanxLj4Bu0
GeYPMI3I+0SLnTfh47v1HLjZ6ozM4lsPRFN6/zkZetltm5sZTW2pOTX4Mx+Gg328
QIDZd8q7I7DNjGRS3tqnZxa2SS4RHXKtM5iMJQmOej2X9MgFCwnFjiklLcZ7QR6z
xB7ccZwZu8lq5l9G3/A4Sg5jrAgzOyKFq4bM7GZboyYTTHhi6VT1zkfVurBb6HXm
tFX82YVb20ybYYOxDGag6qzE7QJX
=NES5
-----END PGP PUBLIC KEY BLOCK-----');

insert into keys
select 2,'私钥',crypto.dearmor('-----BEGIN PGP PRIVATE KEY BLOCK-----
Version: GnuPG v2.0.22 (GNU/Linux)

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
urBb6HXmtFX82YVb20ybYYOxDGag6qzE7QJX
=395H
-----END PGP PRIVATE KEY BLOCK-----');


### 查看原始的钥匙串的值
select id,name,crypto.armor(pkey) from keys;

### 获取钥匙串的key id
select id,name,crypto.pgp_key_id(pkey) from keys;
 id | name |    pgp_key_id
----+------+------------------
  2 | 私钥 | 086812A41A6C562B
  1 | 公钥 | 086812A41A6C562B
(2 rows)

### 公钥加密
select crypto.pgp_pub_encrypt('This is HAWQ',pkey) from keys where id=1;

### 私钥解密
with t_msg as (
select crypto.pgp_pub_encrypt('This is HAWQ',pkey) as msg from keys where id=1
)
select crypto.pgp_pub_decrypt(msg,pkey) from t_msg join keys on keys.id=2;
```



### 对称秘钥

#### pgp_sym_encrypt()

带有一个对称的PGP秘钥`psw`加密`data`。 `options`参数可以包含选项设置。

e.g.

```sql
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password'        -- 密码
                             );
```

#### pgp_sym_decrypt()

解密一个对称秘钥加密的PGP消息。<br />用`pgp_sym_decrypt`解密`bytea`数据是不允许的。 <br />这是为了避免输出不合法的字符数据。<br />用`pgp_sym_decrypt_bytea` 解密原始的文本数据是可以的。<br />`options`参数可以包含选项设置。

e.g.

```sql
select crypto.pgp_sym_decrypt(
                              crypto.pgp_sym_encrypt('This is HAWQ', 'password'),  -- 加密后的数据
                              'password'                                           -- 密码 
                             ) ;
```

### PGP功能的选项

#### compress-algo

只有PostgreSQL编译的时候带有zlib选项时才可以使用下来该选项的压缩算法

```
Values:
      0 - no compression
      1 - ZIP compression
      2 - ZLIB compression (= ZIP plus meta-data and block CRCs)
    Default: 0
    Applies to: pgp_sym_encrypt, pgp_pub_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'compress-algo=2'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                         crypto.pgp_sym_encrypt('This is HAWQ', 'password','compress-algo=2'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ;                             
```



#### unicode-mode

Whether to convert textual data from database internal encoding to UTF-8 and back. If your database already is UTF-8, no conversion will be done, but the message will be tagged as UTF-8. Without this option it will not be.

```
Values: 0, 1
    Default: 0
    Applies to: pgp_sym_encrypt, pgp_pub_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'unicode-mode=1'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                         crypto.pgp_sym_encrypt('This is HAWQ', 'password','unicode-mode=1'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### compress-level

How much to compress. Higher levels compress smaller but are slower. 0 disables compression.

```
    Values: 0, 1-9
    Default: 6
    Applies to: pgp_sym_encrypt, pgp_pub_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'compress-level=9'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                         crypto.pgp_sym_encrypt('This is HAWQ', 'password','compress-level=9'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### cipher-algo

Which cipher algorithm to use.

```
    Values: bf, aes128, aes192, aes256 (OpenSSL-only: 3des, cast5)
    Default: aes128
    Applies to: pgp_sym_encrypt, pgp_pub_encrypt   
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'cipher-algo=aes256'
                             );
 
-- 解密 
select crypto.pgp_sym_decrypt(
                         crypto.pgp_sym_encrypt('This is HAWQ', 'password','cipher-algo=aes256'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### convert-crlf

Whether to convert \n into \r\n when encrypting and \r\n to \n when decrypting. RFC 4880 specifies that text data should be stored using \r\n line-feeds. Use this to get fully RFC-compliant behavior.

```
    Values: 0, 1
    Default: 0
    Applies to: pgp_sym_encrypt, pgp_pub_encrypt, pgp_sym_decrypt, pgp_pub_decrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'convert-crlf=1'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                         crypto.pgp_sym_encrypt('This is HAWQ', 'password','convert-crlf=1'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### disable-mdc

Do not protect data with SHA-1. The only good reason to use this option is to achieve compatibility with ancient PGP products, predating the addition of SHA-1 protected packets to RFC 4880. Recent gnupg.org and pgp.com software supports it fine.

```
    Values: 0, 1
    Default: 0
    Applies to: pgp_sym_encrypt, pgp_pub_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'disable-mdc=1'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                         crypto.pgp_sym_encrypt('This is HAWQ', 'password','disable-mdc=1'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### s2k-mode

Which S2K algorithm to use.

```
    Values:
      0 - Without salt.  Dangerous!
      1 - With salt but with fixed iteration count.
      3 - Variable iteration count.
    Default: 3
    Applies to: pgp_sym_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              's2k-mode=1'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                      crypto.pgp_sym_encrypt('This is HAWQ', 'password','s2k-mode=1'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### s2k-digest-algo

Which digest algorithm to use in S2K calculation.

```
    Values: md5, sha1
    Default: sha1
    Applies to: pgp_sym_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              's2k-digest-algo=md5'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                      crypto.pgp_sym_encrypt('This is HAWQ', 'password','s2k-digest-algo=md5'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### s2k-cipher-algo

Which cipher to use for encrypting separate session key.

```
    Values: bf, aes, aes128, aes192, aes256
    Default: use cipher-algo
    Applies to: pgp_sym_encrypt
```

e.g.

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              's2k-cipher-algo=aes256'
                             );

-- 解密
select crypto.pgp_sym_decrypt(
                     crypto.pgp_sym_encrypt('This is HAWQ', 'password','s2k-cipher-algo=aes256'),  -- 加密后的数据
                         'password'      -- 密码
                             ) ; 
```



#### enable-session-key

Use separate session key. Public-key encryption always uses a separate session key; this is for symmetric-key encryption, which by default uses the S2K key directly.

```
    Values: 0, 1
    Default: 0
    Applies to: pgp_sym_encrypt
```

e.g.

```sql

```



### PGP功能的选项的复合选项

```sql
-- 加密
select crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'compress-algo=2,unicode-mode=1,compress-level=9,convert-crlf=1,disable-mdc=1,s2k-mode=1,s2k-digest-algo=md5,cipher-algo=bf,s2k-cipher-algo=bf'
                             );
 
 
-- 解密 
select crypto.pgp_sym_decrypt(
                     crypto.pgp_sym_encrypt(
  														'This is HAWQ',   -- 数据
                              'password' ,      -- 密码
                              'compress-algo=2,unicode-mode=1,compress-level=9,convert-crlf=1,disable-mdc=1,s2k-mode=1,s2k-digest-algo=md5,cipher-algo=bf,s2k-cipher-algo=bf'
                             ),  -- 加密后的数据
  
                         'password'      -- 密码
                             ) ; 
                             
-- cipher-algo 与 s2k-cipher-algo 必须需要一致                          
```