
命令含义示例

## set

### 含义

设置字符串

### 示例

```
set foo bar
```

## get

### 含义

获得字符串

### 示例

```
get foo
```

## mget

### 含义
返回所有(一个或多个)给定 key 的值。

如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 。因此，该命令永不失败。

### 返回值：

一个包含所有给定 key 的值的列表。

### 示例

``` 
 redis> SET redis redis.com
 OK
 redis> SET mongodb mongodb.org
 OK
 redis> MGET redis mongodb
 1) "redis.com"
 2) "mongodb.org"
 redis> MGET redis mongodb mysql # 不存在的 mysql 返回 nil
 1) "redis.com"
 2) "mongodb.org"
 3) (nil)
```

## getrange
 
### 含义
 
返回 key 中字符串值的子字符串，字符串的截取范围由 start 和 end 两个偏移量决定(包括 start 和 end 在内)。
 
负数偏移量表示从字符串最后开始计数， -1 表示最后一个字符， -2 表示倒数第二个，以此类推。

### 返回值

截取得出的子字符串。

### 示例 

```
redis> SET greeting "hello, my friend"
 OK
 redis> GETRANGE greeting 0 4 # 返回索引0-4的字符，包括4。
 "hello"
 redis> GETRANGE greeting -1 -5 # 不支持回绕操作
 ""
 redis> GETRANGE greeting -3 -1 # 负数索引
 "end"
 redis> GETRANGE greeting 0 -1 # 从第一个到最后一个
 "hello, my friend"
 redis> GETRANGE greeting 0 1008611 # 值域范围不超过实际字符串，超过部分自动被符略
 "hello, my friend"
```

## getset

### 含义

将给定 key 的值设为 value ，并返回 key 的旧值(old value)。

当 key 存在但不是字符串类型时，返回一个错误。

### 返回值

 返回给定 key 的旧值。
 
 当 key 没有旧值时，也即是， key 不存在时，返回 nilredis> GETSET db mongodb # 没有旧值，返回 nil

###示例

```
 (nil)
 redis> GET db
 "mongodb"
 redis> GETSET db redis # 返回旧值 mongodb
 "mongodb"
 redis> GET db
 "redis"
```
 
## exists

### 含义

检查字符串是否存在【1：存在 0：不存在】

### 示例

exists foo

## append

### 含义
追加字符串

### 示例

append foo 123

## Time series

### 含义

时间序列

## del

### 含义

删除字符串【1：成功 0：失败】

### 示例

del foo

## setnx

### 含义

将key的值设为value，当且仅当key不存在。

若给定的key已经存在，则setnx不做任何动作

### 返回值

设置成功，返回 1 。
 
设置失败，返回 0 。

### 示例

setnx job "programmer"


## setex

### 含义

将值value关联到key，并将key的生存时间设置为seconds(以秒为单位)。如果key已经存在，setex命令将覆写旧值，这个是原子操作
 
### 返回值

设置成功时返回 OK 。

当 seconds 参数不合法时，返回一个错误。# 在 key 不存在时进行 SETEX
 
### 示例

``` 
 redis> SETEX cache_user_id 60 10086
 OK
 redis> GET cache_user_id # 值
 "10086"
 redis> TTL cache_user_id # 剩余生存时间
 (integer) 49
 # key 已经存在时，SETEX 覆盖旧值
 redis> SET cd "timeless"
 OK
 redis> SETEX cd 3000 "goodbye my love"
 OK
 redis> GET cd
 "goodbye my love"
 redis> TTL cd
 (integer) 2997
```

## psetex

### 含义
 
以毫秒为单位设置key生存时间
 
### 返回值
 
设置成功时返回ok

### 示例

``` 
redis> PSETEX mykey 1000 "Hello"
 OK
 redis> PTTL mykey
 (integer) 999
 redis> GET mykey
 "Hello"
```

## setrange

### 含义
 
用 value 参数覆写(overwrite)给定 key 所储存的字符串值，从偏移量 offset 开始。不存在的 key 当作空白字符串处理。

SETRANGE 命令会确保字符串足够长以便将 value 设置在指定的偏移量上，如果给定 key 原来储存的字符串长度比偏移量小(比如字符串只有 5 个字符长，但你设置的 offset 是 10 )，那么原字符和偏移量之间的空白将用零字节(zerobytes, "\x00" )来填充。

### 返回值

被 SETRANGE 修改之后，字符串的长度。# 对非空字符串进行 SETRANGE

### 示例

```
redis> SET greeting "hello world"
 OK
 redis> SETRANGE greeting 6 "Redis"
 (integer) 11
 redis> GET greeting
 "hello Redis"
 # 对空字符串/不存在的 key 进行 SETRANGE
 redis> EXISTS empty_string
 (integer) 0
 redis> SETRANGE empty_string 5 "Redis!" # 对不存在的 key 使用 SETRANGE
 (integer) 11
 redis> GET empty_string # 空白处被"\x00"填充
 "\x00\x00\x00\x00\x00Redis!"
```

## mset

### 含义

同时设置一个或多个 key-value 对。

### 返回值

总是返回 OK (因为 MSET 不可能失败)

### 示例

```
redis> MSET date "2012.3.30" time "11:00 a.m." weather "sunny"
 OK
 redis> MGET date time weather
 1) "2012.3.30"
 2) "11:00 a.m."
 3) "sunny"
 # MSET 覆盖旧值例子
 redis> SET google "google.hk"
 OK
 redis> MSET google "google.com"
 OK
 redis> GET google
 "google.com"
```

## metnx


### 含义

同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。

即使只有一个给定 key 已存在， MSETNX 也会拒绝执行所有给定 key 的设置操作。
MSETNX 是原子性的，因此它可以用作设置多个不同 key 表示不同字段(field)的唯一性逻辑对象(unique logic object)，所有字段要么全被设置，要么全不被设置。

### 返回值

当所有 key 都成功设置，返回 1 。

如果所有给定 key 都设置失败(至少有一个 key 已经存在)，那么返回 0 。

### 示例

```
 # 对不存在的 key 进行 MSETNX
 redis> MSETNX rmdbs "MySQL" nosql "MongoDB" key-value-store "redis"
 (integer) 1
 redis> MGET rmdbs nosql key-value-store
 1) "MySQL"
 2) "MongoDB"
 3) "redis"
 # MSET 的给定 key 当中有已存在的 key
 redis> MSETNX rmdbs "Sqlite" language "python" # rmdbs 键已经存在，操作失败
 (integer) 0
 redis> EXISTS language # 因为 MSET 是原子性操作，language 没有被设置
 (integer) 0
 redis> GET rmdbs # rmdbs 也没有被修改
 "MySQL"
```

## strlen
 
### 含义

返回 key 所储存的字符串值的长度。
 
当 key 储存的不是字符串值时，返回一个错误。
 
### 返回值
 
字符串值的长度。
 
当 key 不存在时，返回 0

### 示例

```
 # 获取字符串的长度
 redis> SET mykey "Hello world"
 OK
 redis> STRLEN mykey
 (integer) 11
 # 不存在的 key 长度为 0
 redis> STRLEN nonexisting
 (integer) 0
```

## decr

### 含义 

将 key 中储存的数字值减一。
 
如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 DECR 操作。
 
如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。
 
本操作的值限制在 64 位(bit)有符号数字表示之内。
 
关于递增(increment) / 递减(decrement)操作的更多信息，请参见 INCR 命令。
 
### 返回值

执行 DECR 命令之后 key 的值。

### 示例

```
 # 对存在的数字值 key 进行 DECR
 redis> SET failure_times 10
 OK
 redis> DECR failure_times
 (integer) 9
 # 对不存在的 key 值进行 DECR
 redis> EXISTS count
 (integer) 0
 redis> DECR count
 (integer) -1
 # 对存在但不是数值的 key 进行 DECR
 redis> SET company YOUR_CODE_SUCKS.LLC
 OK
 redis> DECR company
 (error) ERR value is not an integer or out of range
```

## decrby

### 含义

 将 key 所储存的值减去减量 decrement 。
 
 如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 DECRBY 操作。
 
 如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。
 
 本操作的值限制在 64 位(bit)有符号数字表示之内。
 
 关于更多递增(increment) / 递减(decrement)操作的更多信息，请参见 INCR 命令。
 
### 返回值
 
 减去 decrement 之后， key 的值。

### 示例

```
 # 对已存在的 key 进行 DECRBY
 redis> SET count 100
 OK
 redis> DECRBY count 20
 (integer) 80
 # 对不存在的 key 进行DECRBY
 redis> EXISTS pages
 (integer) 0
 redis> DECRBY pages 10
 (integer) -10
```
 
## incr
 
### 含义

将 key 中储存的数字值增一。
 
如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCR 操作。
 
如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。
 
本操作的值限制在 64 位(bit)有符号数字表示之内。
 
### 返回值
 
执行 INCR 命令之后 key 的值。
 
### 示例

```
 redis> SET page_view 20
 OK
 redis> INCR page_view
 (integer) 21
 redis> GET page_view # 数字值在 Redis 中以字符串的形式保存
 "21"
```

## incrby
 
### 含义

 将 key 所储存的值加上增量 increment 。
 
 如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCRBY 命令。
 
 如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。
 
 本操作的值限制在 64 位(bit)有符号数字表示之内。
 
 关于递增(increment) / 递减(decrement)操作的更多信息，参见 INCR 命令。
 
### 返回值
 
 加上 increment 之后， key 的值。
 
### 示例

```
 # key 存在且是数字值
 redis> SET rank 50
 OK
 redis> INCRBY rank 20
 (integer) 70
 redis> GET rank
 "70"
 # key 不存在时
 redis> EXISTS counter
 (integer) 0
 redis> INCRBY counter 30
 (integer) 30
 redis> GET counter
 "30"
 # key 不是数字值时
 redis> SET book "long long ago..."
 OK
 redis> INCRBY book 200
 (error) ERR value is not an integer or out of range
```

## incrbyfloat

### 含义

为 key 中所储存的值加上浮点数增量 increment 。
 
如果 key 不存在，那么 INCRBYFLOAT 会先将 key 的值设为 0 ，再执行加法操作。
 
如果命令执行成功，那么 key 的值会被更新为（执行加法之后的）新值，并且新值会以字符串的形式返回给调用者。
 
无论是 key 的值，还是增量 increment ，都可以使用像 2.0e7 、 3e5 、 90e-2 那样的指数符号(exponential notation)来表示，但是，执行 INCRBYFLOAT 命令之后的值总是以同样的形式储存，也即是，它们总是由一个数字，一个（可选的）小数点和一个任意位的小数部分组成（比如 3.14 、 69.768 ，诸如此类)，小数部分尾随的 0 会被移除，如果有需要的话，还会将浮点数改为整数（比如 3.0 会被保存成 3 ）。
 
除此之外，无论加法计算所得的浮点数的实际精度有多长， INCRBYFLOAT 的计算结果也最多只能表示小数点的后十七位。
 
当以下任意一个条件发生时，返回一个错误：
 
key 的值不是字符串类型(因为 Redis 中的数字和浮点数都以字符串的形式保存，所以它们都属于字符串类型）
 
key 当前的值或者给定的增量 increment 不能解释(parse)为双精度浮点数(double precision floating point number）
 
### 返回值
 
 执行命令之后 key 的值。
 
### 示例

```
 # 值和增量都不是指数符号
 redis> SET mykey 10.50
 OK
 redis> INCRBYFLOAT mykey 0.1
 "10.6"
 # 值和增量都是指数符号
 redis> SET mykey 314e-2
 OK
 redis> GET mykey # 用 SET 设置的值可以是指数符号
 "314e-2"
 redis> INCRBYFLOAT mykey 0 # 但执行 INCRBYFLOAT 之后格式会被改成非指数符号
 "3.14"
 # 可以对整数类型执行
 redis> SET mykey 3
 OK
 redis> INCRBYFLOAT mykey 1.1
 "4.1"
 # 后跟的 0 会被移除
 redis> SET mykey 3.0
 OK
 redis> GET mykey # SET 设置的值小数部分可以是 0
 "3.0"
 redis> INCRBYFLOAT mykey 1.000000000000000000000 # 但 INCRBYFLOAT 会将无用的 0 忽略掉，有需要的话，将浮点变为整数
 "4"
 redis> GET mykey
 "4"
```
