
命令含义示例

## del

### 含义

删除给定的一个或多个 key 。

不存在的 key 会被忽略。

### 返回值：

被删除 key 的数量。

### 示例

```
 # 删除单个 key
 redis> SET name huangz
 OK
 redis> DEL name
 (integer) 1
 # 删除一个不存在的 key
 redis> EXISTS phone
 (integer) 0
 redis> DEL phone # 失败，没有 key 被删除
 (integer) 0
 # 同时删除多个 key
 redis> SET name "redis"
 OK
 redis> SET type "key-value store"
 OK
 redis> SET website "redis.com"
 OK
 redis> DEL name type website
 (integer) 3
```


## keys
 
查找所有符合给定模式 pattern 的 key 。
 
KEYS * 匹配数据库中所有 key 。
 
KEYS h?llo 匹配 hello ， hallo 和 hxllo 等。
 
KEYS h*llo 匹配 hllo 和 heeeeello 等。
 
KEYS h[ae]llo 匹配 hello 和 hallo ，但不匹配 hillo 。
 
### 返回值

符合给定模式的 key 列表。

### 示例
 
```
 redis> MSET one 1 two 2 three 3 four 4 # 一次设置 4 个 key
 OK
 redis> KEYS *o*
 1) "four"
 2) "two"
 3) "one"
 redis> KEYS t??
 1) "two"
 redis> KEYS t[w]*
 1) "two"
 redis> KEYS * # 匹配数据库内所有 key
 1) "four"
 2) "three"
 3) "two"
 4) "one"
```

## randomkey

### 含义

从当前数据库中随机返回(不删除)一个 key 。

### 返回值：

当数据库不为空时，返回一个 key 。

当数据库为空时，返回 nil 。

### 示例

```
 # 数据库不为空
 redis> MSET fruit "apple" drink "beer" food "cookies" # 设置多个 key
 OK
 redis> RANDOMKEY
 "fruit"
 redis> RANDOMKEY
 "food"
 redis> KEYS * # 查看数据库内所有key，证明 RANDOMKEY 并不删除 key
 1) "food"
 2) "drink"
 3) "fruit"
 # 数据库为空
 redis> FLUSHDB # 删除当前数据库所有 key
 OK
 redis> RANDOMKEY
 (nil)
```

## ttl key

### 含义
 
以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
 
### 返回值

当 key 不存在或没有设置生存时间时，返回 -1 。
 
否则，返回 key 的剩余生存时间(以秒为单位)。

### 示例

```
 # 带 TTL 的key
 redis> SET name "redis"
 OK
 redis> EXPIRE name 30
 (integer) 1
 redis> TTL name
 (integer) 27
 redis> TTL name
 (integer) 13
 redis> TTL name # 过期返回 -1
 (integer) -1
 redis> GET name # 并且 key 被删除
 (nil)
 # 不带TTL的key
 redis> SET site wikipedia.org
 OK
 redis> TTL wikipedia.org
 (integer) -1
 # 不存在的key
 redis> EXISTS not_exists_key
 (integer) 0
 redis> TTL not_exists_key
 (integer) -1
```

## pttl key

### 含义

这个命令类似于 TTL 命令，但它以毫秒为单位返回 key 的剩余生存时间，而不是像 TTL 命令那样，以秒为单位。

### 返回值

如果 key 不存在，返回 -1 。

否则，返回以毫秒为单位表示的 key 的剩余生存时间。

### 示例

```
redis> SET mykey "Hello"
 OK
 redis> EXPIRE mykey 1
 (integer) 1
 redis> PTTL mykey
 (integer) 1000
 # 对不存在的 key 返回 -1
 redis> EXISTS some_key
 (integer) 0
 redis> PTTL some_key
 (integer) -1
```


## EXISTS

### 含义

检查给定 key 是否存在。
 
### 返回值
 
若 key 存在，返回 1 ，否则返回 0 。

### 示例

```
 redis> SET db "redis"
 OK
 redis> EXISTS db
 (integer) 1
 redis> DEL db
 (integer) 1
 redis> EXISTS db
 (integer) 0
```

## move

### 含义

将当前数据库的 key 移动到给定的数据库 db 当中。

如果当前数据库(源数据库)和给定数据库(目标数据库)有相同名字的给定 key ，或者 key 不存在于当前数据库，那么 MOVE 没有任何效果。

因此，也可以利用这一特性，将 MOVE 当作锁(locking)原语(primitive)。

### 返回值

移动成功返回 1 ，失败则返回 0 。

### 示例

```
 # key 存在于当前数据库
 redis> SELECT 0 # redis默认使用数据库 0，为了清晰起见，这里再显式指定一次。
 OK
 redis> SET song "secret base - Zone"
 OK
 redis> MOVE song 1 # 将 song 移动到数据库 1
 (integer) 1
 redis> EXISTS song # song 已经被移走
 (integer) 0
 redis> SELECT 1 # 使用数据库 1
 OK
 redis:1> EXISTS song # 证实 song 被移到了数据库 1 (注意命令提示符变成了"redis:1"，表明正在使用数据库 1)
 (integer) 1
 # 当 key 不存在的时候
 redis:1> EXISTS fake_key
 (integer) 0
 redis:1> MOVE fake_key 0 # 试图从数据库 1 移动一个不存在的 key 到数据库 0，失败
 (integer) 0
 redis:1> select 0 # 使用数据库0
 OK
 redis> EXISTS fake_key # 证实 fake_key 不存在
 (integer) 0
 # 当源数据库和目标数据库有相同的 key 时
 redis> SELECT 0 # 使用数据库0
 OK
 redis> SET favorite_fruit "banana"
 OK
 redis> SELECT 1 # 使用数据库1
 OK
 redis:1> SET favorite_fruit "apple"
 OK
 redis:1> SELECT 0 # 使用数据库0，并试图将 favorite_fruit 移动到数据库 1
 OK
 redis> MOVE favorite_fruit 1 # 因为两个数据库有相同的 key，MOVE 失败
 (integer) 0
 redis> GET favorite_fruit # 数据库 0 的 favorite_fruit 没变
 "banana"
 redis> SELECT 1
 OK
 redis:1> GET favorite_fruit # 数据库 1 的 favorite_fruit 也是
 "apple"
```

## rename

### 含义

将 key 改名为 newkey 。

当 key 和 newkey 相同，或者 key 不存在时，返回一个错误。

当 newkey 已经存在时， RENAME 命令将覆盖旧值。

### 返回值：

改名成功时提示 OK ，失败时候返回一个错误。

### 示例

```
 # key 存在且 newkey 不存在
 redis> SET message "hello world"
 OK
 redis> RENAME message greeting
 OK
 redis> EXISTS message # message 不复存在
 (integer) 0
 redis> EXISTS greeting # greeting 取而代之
 (integer) 1
 # 当 key 不存在时，返回错误
 redis> RENAME fake_key never_exists
 (error) ERR no such key
 # newkey 已存在时， RENAME 会覆盖旧 newkey
 redis> SET pc "lenovo"
 OK
 redis> SET personal_computer "dell"
 OK
 redis> RENAME pc personal_computer
 OK
 redis> GET pc
 (nil)
 redis:1> GET personal_computer # 原来的值 dell 被覆盖了
 "lenovo"
```

## renamex

### 含义

当且仅当 newkey 不存在时，将 key 改名为 newkey 。
 
当 key 不存在时，返回一个错误。
 
### 返回值

修改成功时，返回 1 。
 
如果 newkey 已经存在，返回 0 。

### 示例

```
 # newkey 不存在，改名成功
 redis> SET player "MPlyaer"
 OK
 redis> EXISTS best_player
 (integer) 0
 redis> RENAMENX player best_player
 (integer) 1
 # newkey存在时，失败
 redis> SET animal "bear"
 OK
 redis> SET favorite_animal "butterfly"
 OK
 redis> RENAMENX animal favorite_animal
 (integer) 0
 redis> get animal
 "bear"
 redis> get favorite_animal
 "butterfly"
```

## type

### 含义

返回 key 所储存的值的类型。
 
### 返回值

none (key不存在)
 
string (字符串)
 
list (列表)
 
set (集合)
 
zset (有序集)
 
hash (哈希表)

### 示例

```
 # 字符串
 redis> SET weather "sunny"
 OK
 redis> TYPE weather
 string
 # 列表
 redis> LPUSH book_list "programming in scala"
 (integer) 1
 redis> TYPE book_list
 list
 # 集合
 redis> SADD pat "dog"
 (integer) 1
 redis> TYPE pat
 set
```

## expireat

### 含义

EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置生存时间。

不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。

### 返回值

如果生存时间设置成功，返回 1 。
 
当 key 不存在或没办法设置生存时间，返回 0 。

### 示例

```
 redis> SET cache www.google.com
 OK
 redis> EXPIREAT cache 1355292000 # 这个 key 将在 2012.12.12 过期
 (integer) 1
 redis> TTL cache
 (integer) 45081860
```

## pexpireat

### 含义

这个命令和 EXPIREAT 命令类似，但它以毫秒为单位设置 key 的过期 unix 时间戳，而不是像 EXPIREAT 那样，以秒为单位。

### 返回值

如果生存时间设置成功，返回 1 。

当 key 不存在或没办法设置生存时间时，返回 0 。(查看 EXPIRE 命令获取更多信息)

### 示例

```
 redis> SET mykey "Hello"
 OK
 redis> PEXPIREAT mykey 1555555555005
 (integer) 1
 redis> TTL mykey # TTL 返回秒
 (integer) 223157079
 redis> PTTL mykey # PTTL 返回毫秒
 (integer) 223157079318
```

## persist

### 含义

移除给定 key 的生存时间，将这个 key 从『可挥发』的(带生存时间 key )转换成『持久化』的(一个不带生存时间、永不过期的key )。

### 返回值

当生存时间移除成功时，返回 1 .

如果 key 不存在或 key 没有设置生存时间，返回 0 。

### 示例

```
redis> SET mykey "Hello"
 OK
 redis> EXPIRE mykey 10 # 为 key 设置生存时间
 (integer) 1
 redis> TTL mykey
 (integer) 10
 redis> PERSIST mykey # 移除 key 的生存时间
 (integer) 1
 redis> TTL mykey
 (integer) -1
```