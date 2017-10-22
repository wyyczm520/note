## hset

### 含义

将哈希表 key 中的域 field 的值设为 value 。

如果 key 不存在，一个新的哈希表被创建并进行 HSET 操作。

如果域 field 已经存在于哈希表中，旧值将被覆盖。

### 返回值

如果 field 是哈希表中的一个新建域，并且值设置成功，返回 1 。

如果哈希表中域 field 已经存在且旧值已被新值覆盖，返回 0 。

### 示例

```
redis> HSET website google "www.g.cn" # 设置一个新域
 (integer) 1

 redis> HSET website google "www.google.com" # 覆盖一个旧域
 (integer) 0
```

## hsetnx

### 含义

将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在。
 
若域 field 已经存在，该操作无效。
 
如果 key 不存在，一个新哈希表被创建并执行 HSETNX 命令。
 
### 返回值
 
 设置成功，返回 1 。
 
 如果给定域已经存在且没有操作被执行，返回 0 。

### 示例

```
 redis> HSETNX nosql key-value-store redis
 (integer) 1
 redis> HSETNX nosql key-value-store redis # 操作无效，域 key-value-store 已存在
 (integer) 0
```

## hmset

### 含义

设置hash对象

### 示例

```
hmset user:1 username yiibai password yiibai points 200
```

## hgetall

### 含义

查询hash对象

### 示例

```
hgetall user:1
```

## hdel

### 含义

删除哈希表 key 中的一个或多个指定域，不存在的域将被忽略。

### 返回值

被成功移除的域的数量，不包括被忽略的域。

### 示例

```
 # 测试数据
 redis> HGETALL abbr
 1) "a"
 2) "apple"
 3) "b"
 4) "banana"
 5) "c"
 6) "cat"
 7) "d"
 8) "dog"
 # 删除单个域
 redis> HDEL abbr a
 (integer) 1
 # 删除不存在的域
 redis> HDEL abbr not-exists-field
 (integer) 0
 # 删除多个域
 redis> HDEL abbr b c
 (integer) 2
 redis> HGETALL abbr
 1) "d"
 2) "dog"
```
 
## hlen

### 含义

返回哈希表 key 中域的数量。

### 返回值

哈希表中域的数量。

当 key 不存在时，返回 0 。

### 示例

```
redis> HSET db redis redis.com
 (integer) 1
 redis> HSET db mysql mysql.com
 (integer) 1
 redis> HLEN db
 (integer) 2
 redis> HSET db mongodb mongodb.org
 (integer) 1
 redis> HLEN db
 (integer) 3
```

## hexists

### 含义

查看哈希表 key 中，给定域 field 是否存在。

### 返回值

如果哈希表含有给定域，返回 1 。
如果哈希表不含有给定域，或 key 不存在，返回 0 。

### 示例

```
redis> HEXISTS phone myphone
 (integer) 0
 redis> HSET phone myphone nokia-1110
 (integer) 1
 redis> HEXISTS phone myphone
 (integer) 1
```

## hincrby

### 含义

为哈希表 key 中的域 field 的值加上增量 increment 。

增量也可以为负数，相当于对给定域进行减法操作。

如果 key 不存在，一个新的哈希表被创建并执行 HINCRBY 命令。

如果域 field 不存在，那么在执行命令前，域的值被初始化为 0 。

对一个储存字符串值的域 field 执行 HINCRBY 命令将造成一个错误。

本操作的值被限制在 64 位(bit)有符号数字表示之内。

### 返回值

执行 HINCRBY 命令之后，哈希表 key 中域 field 的值。

### 示例

```
 # increment 为正数
 redis> HEXISTS counter page_view # 对空域进行设置
 (integer) 0
 redis> HINCRBY counter page_view 200
 (integer) 200
 redis> HGET counter page_view
 "200"
 # increment 为负数
 redis> HGET counter page_view
 "200"
 redis> HINCRBY counter page_view -50
 (integer) 150
 redis> HGET counter page_view
 "150"
 # 尝试对字符串值的域执行HINCRBY命令
 redis> HSET myhash string hello,world # 设定一个字符串值
 (integer) 1
 redis> HGET myhash string
 "hello,world"
 redis> HINCRBY myhash string 1 # 命令执行失败，错误。
 (error) ERR hash value is not an integer
 redis> HGET myhash string # 原值不变
 "hello,world"
```

## hincrbyfloat

### 含义

为哈希表 key 中的域 field 加上浮点数增量 increment 。
 
如果哈希表中没有域 field ，那么 HINCRBYFLOAT 会先将域 field 的值设为 0 ，然后再执行加法操作。
 
如果键 key 不存在，那么 HINCRBYFLOAT 会先创建一个哈希表，再创建域 field ，最后再执行加法操作。
 
当以下任意一个条件发生时，返回一个错误：
 
域 field 的值不是字符串类型(因为 redis 中的数字和浮点数都以字符串的形式保存，所以它们都属于字符串类型）
 
域 field 当前的值或给定的增量 increment 不能解释(parse)为双精度浮点数(double precision floating point number)
 
HINCRBYFLOAT 命令的详细功能和 INCRBYFLOAT 命令类似，请查看 INCRBYFLOAT 命令获取更多相关信息。
 
### 返回值

执行加法操作之后 field 域的值。

### 示例

```
 # 值和增量都是普通小数
 redis> HSET mykey field 10.50
 (integer) 1
 redis> HINCRBYFLOAT mykey field 0.1
 "10.6"
 # 值和增量都是指数符号
 redis> HSET mykey field 5.0e3
 (integer) 0
 redis> HINCRBYFLOAT mykey field 2.0e2
 "5200"
 # 对不存在的键执行 HINCRBYFLOAT
 redis> EXISTS price
 (integer) 0
 redis> HINCRBYFLOAT price milk 3.5
 "3.5"
 redis> HGETALL price
 1) "milk"
 2) "3.5"
 # 对不存在的域进行 HINCRBYFLOAT
 redis> HGETALL price
 1) "milk"
 2) "3.5"
 redis> HINCRBYFLOAT price coffee 4.5 # 新增 coffee 域
 "4.5"
 redis> HGETALL price
 1) "milk"
 2) "3.5"
 3) "coffee"
 4) "4.5"
```

## hkeys

### 含义

返回哈希表 key 中的所有域。

### 返回值

一个包含哈希表中所有域的表。

当 key 不存在时，返回一个空表。

### 示例
```
 # 哈希表非空
 redis> HMSET website google www.google.com yahoo www.yahoo.com
 OK
 redis> HKEYS website
 1) "google"
 2) "yahoo"
 # 空哈希表/key不存在
 redis> EXISTS fake_key
 (integer) 0
 redis> HKEYS fake_key
 (empty list or set)
```

## hvals

### 含义

返回哈希表 key 中所有域的值。

### 返回值

一个包含哈希表中所有值的表。

当 key 不存在时，返回一个空表。

### 示例

```
 # 非空哈希表
 redis> HMSET website google www.google.com yahoo www.yahoo.com
 OK
 redis> HVALS website
 1) "www.google.com"
 2) "www.yahoo.com"
 # 空哈希表/不存在的key
 redis> EXISTS not_exists
 (integer) 0
 redis> HVALS not_exists
 (empty list or set)
```