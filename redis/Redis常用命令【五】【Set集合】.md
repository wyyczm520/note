# Set 集合

## sadd

### 含义

将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略。

假如 key 不存在，则创建一个只包含 member 元素作成员的集合。

当 key 不是集合类型时，返回一个错误。

### 返回值

被添加到集合中的新元素的数量，不包括被忽略的元素。

### 示例

```
 # 添加单个元素
 redis> SADD bbs "discuz.net"
 (integer) 1
 # 添加重复元素
 redis> SADD bbs "discuz.net"
 (integer) 0
 # 添加多个元素
 redis> SADD bbs "tianya.cn" "groups.google.com"
 (integer) 2
 redis> SMEMBERS bbs
 1) "discuz.net"
 2) "groups.google.com"
 3) "tianya.cn"
``` 

## srem

### 含义 

移除集合 key 中的一个或多个 member 元素，不存在的 member 元素会被忽略。
 
当 key 不是集合类型，返回一个错误。
 
### 返回值

被成功移除的元素的数量，不包括被忽略的元素。

### 示例

```
 # 测试数据
 redis> SMEMBERS languages
 1) "c"
 2) "lisp"
 3) "python"
 4) "ruby"
 # 移除单个元素
 redis> SREM languages ruby
 (integer) 1
 # 移除不存在元素
 redis> SREM languages non-exists-language
 (integer) 0
 # 移除多个元素
 redis> SREM languages lisp python c
 (integer) 3
 redis> SMEMBERS languages
 (empty list or set)
```

## smembers

### 含义

返回集合 key 中的所有成员。
不存在的 key 被视为空集合。

### 返回值

集合中的所有成员。

### 示例

```
 # key 不存在或集合为空
 redis> EXISTS not_exists_key
 (integer) 0
 redis> SMEMBERS not_exists_key
 (empty list or set)
 # 非空集合
 redis> SADD language Ruby Python Clojure
 (integer) 3
 redis> SMEMBERS language
 1) "Python"
 2) "Ruby"
 3) "Clojure"
```

## sismember

### 含义

判断 member 元素是否集合 key 的成员。

### 返回值

如果 member 元素是集合的成员，返回 1 。

如果 member 元素不是集合的成员，或 key 不存在，返回 0 。

### 示例
 
```
 redis> SMEMBERS joe's_movies
 1) "hi, lady"
 2) "Fast Five"
 3) "2012"
 redis> SISMEMBER joe's_movies "bet man"
 (integer) 0
 redis> SISMEMBER joe's_movies "Fast Five"
 (integer) 1
```

## scard

### 含义

返回集合 key 的基数(集合中元素的数量)。

### 返回值

集合的基数。

当 key 不存在时，返回 0

### 示例

```
redis> SADD tool pc printer phone
 (integer) 3
 redis> SCARD tool # 非空集合
 (integer) 3
 redis> DEL tool
 (integer) 1
 redis> SCARD tool # 空集合
 (integer) 0
```

## smove

### 示例

将 member 元素从 source 集合移动到 destination 集合。

SMOVE 是原子性操作。

如果 source 集合不存在或不包含指定的 member 元素，则 SMOVE 命令不执行任何操作，仅返回 0 。否则， member 元素从source 集合中被移除，并添加到 destination 集合中去。

当 destination 集合已经包含 member 元素时， SMOVE 命令只是简单地将 source 集合中的 member 元素删除。

当 source 或 destination 不是集合类型时，返回一个错误。

### 返回值

如果 member 元素被成功移除，返回 1 。

如果 member 元素不是 source 集合的成员，并且没有任何操作对 destination 集合执行，那么返回 0 。

### 示例

```
 redis> SMEMBERS songs
 1) "Billie Jean"
 2) "Believe Me"
 redis> SMEMBERS my_songs
 (empty list or set)
 redis> SMOVE songs my_songs "Believe Me"
 (integer) 1
 redis> SMEMBERS songs
 1) "Billie Jean"
 redis> SMEMBERS my_songs
 1) "Believe Me"
```

## spop

### 含义

移除并返回集合中的一个随机元素。
 
如果只想获取一个随机元素，但不想该元素从集合中被移除的话，可以使用 SRANDMEMBER 命令。

### 返回值

被移除的随机元素。

当 key 不存在或 key 是空集时，返回 nil 。

### 示例

```
redis> SMEMBERS db
 1) "MySQL"
 2) "MongoDB"
 3) "Redis"
 redis> SPOP db
 "Redis"
 redis> SMEMBERS db
 1) "MySQL"
 2) "MongoDB"
 redis> SPOP db
 "MySQL"
 redis> SMEMBERS db
 1) "MongoDB"
```

## srandmember

### 含义

如果命令执行时，只提供了 key 参数，那么返回集合中的一个随机元素。

从 Redis 2.6 版本开始， SRANDMEMBER 命令接受可选的 count 参数：

如果 count 为正数，且小于集合基数，那么命令返回一个包含 count 个元素的数组，数组中的元素各不相同。如果 count大于等于集合基数，那么返回整个集合。

如果 count 为负数，那么命令返回一个数组，数组中的元素可能会重复出现多次，而数组的长度为 count 的绝对值。

该操作和 SPOP 相似，但 SPOP 将随机元素从集合中移除并返回，而 SRANDMEMBER 则仅仅返回随机元素，而不对集合进行任何改动。

### 返回值

只提供 key 参数时，返回一个元素；如果集合为空，返回 nil 。

如果提供了 count 参数，那么返回一个数组；如果集合为空，返回空数组。# 添加元素

### 示例

```
 redis> SADD fruit apple banana cherry
 (integer) 3
 # 只给定 key 参数，返回一个随机元素
 redis> SRANDMEMBER fruit
 "cherry"
 redis> SRANDMEMBER fruit
 "apple"
 # 给定 3 为 count 参数，返回 3 个随机元素
 # 每个随机元素都不相同
 redis> SRANDMEMBER fruit 3
 1) "apple"
 2) "banana"
 3) "cherry"
 # 给定 -3 为 count 参数，返回 3 个随机元素
 # 元素可能会重复出现多次
 redis> SRANDMEMBER fruit -3
 1) "banana"
 2) "cherry"
 3) "apple"
 redis> SRANDMEMBER fruit -3
 1) "apple"
 2) "apple"
 3) "cherry"
 # 如果 count 是整数，且大于等于集合基数，那么返回整个集合
 redis> SRANDMEMBER fruit 10
 1) "apple"
 2) "banana"
 3) "cherry"
 # 如果 count 是负数，且 count 的绝对值大于集合的基数
 # 那么返回的数组的长度为 count 的绝对值
 redis> SRANDMEMBER fruit -10
 1) "banana"
 2) "apple"
 3) "banana"
 4) "cherry"
 5) "apple"
 6) "apple"
 7) "cherry"
 8) "apple"
 9) "apple"
 10) "banana"
 # SRANDMEMBER 并不会修改集合内容
 redis> SMEMBERS fruit
 1) "apple"
 2) "cherry"
 3) "banana"
 # 集合为空时返回 nil 或者空数组
 redis> SRANDMEMBER not-exists
 (nil)
 redis> SRANDMEMBER not-eixsts 10
 (empty list or set)
```

## sinter

### 含义

返回一个集合的全部成员，该集合是所有给定集合的交集。

不存在的 key 被视为空集。

当给定集合当中有一个空集时，结果也为空集(根据集合运算定律)。

### 返回值

交集成员的列表

### 示例 
```
 redis> SMEMBERS group_1
 1) "LI LEI"
 2) "TOM"
 3) "JACK"
 redis> SMEMBERS group_2
 1) "HAN MEIMEI"
 2) "JACK"
 redis> SINTER group_1 group_2
 1) "JACK"
```

## sinterstore

### 含义

这个命令类似于 SINTER 命令，但它将结果保存到 destination 集合，而不是简单地返回结果集。

如果 destination 集合已经存在，则将其覆盖。

destination 可以是 key 本身。

### 返回值

结果集中的成员数量。

### 示例

```
redis> SMEMBERS songs
 1) "good bye joe"
 2) "hello,peter"
 redis> SMEMBERS my_songs
 1) "good bye joe"
 2) "falling"
 redis> SINTERSTORE song_interset songs my_songs
 (integer) 1
 redis> SMEMBERS song_interset
 1) "good bye joe"
```

## sunion

### 含义

返回一个集合的全部成员，该集合是所有给定集合的并集。

不存在的 key 被视为空集。

### 返回值

并集成员的列表。

### 示例

```
redis> SMEMBERS songs
 1) "Billie Jean"
 redis> SMEMBERS my_songs
 1) "Believe Me"
 redis> SUNION songs my_songs
 1) "Billie Jean"
 2) "Believe Me"
```

## sunionstore

### 含义

这个命令类似于 SUNION 命令，但它将结果保存到 destination 集合，而不是简单地返回结果集。

如果 destination 已经存在，则将其覆盖。

destination 可以是 key 本身。

### 返回值

结果集中的元素数量。

### 示例

```
redis> SMEMBERS NoSQL
 1) "MongoDB"
 2) "Redis"
 redis> SMEMBERS SQL
 1) "sqlite"
 2) "MySQL"
 redis> SUNIONSTORE db NoSQL SQL
 (integer) 4
 redis> SMEMBERS db
 1) "MySQL"
 2) "sqlite"
 3) "MongoDB"
 4) "Redis"
```

## sdiff

### 含义

返回一个集合的全部成员，该集合是所有给定集合之间的差集。

不存在的 key 被视为空集。

### 返回值

交集成员的列表。

### 示例

```
redis> SMEMBERS peter's_movies
 1) "bet man"
 2) "start war"
 3) "2012"
 redis> SMEMBERS joe's_movies
 1) "hi, lady"
 2) "Fast Five"
 3) "2012"
 redis> SDIFF peter's_movies joe's_movies
 1) "bet man"
 2) "start war"
```

## sdiffstrore

### 含义

这个命令的作用和 SDIFF 类似，但它将结果保存到 destination 集合，而不是简单地返回结果集。
 
如果 destination 集合已经存在，则将其覆盖。
 
destination 可以是 key 本身。
 
### 返回值

结果集中的元素数量。

### 示例

```
 redis> SMEMBERS joe's_movies
 1) "hi, lady"
 2) "Fast Five"
 3) "2012"
 redis> SMEMBERS peter's_movies
 1) "bet man"
 2) "start war"
 3) "2012"
 redis> SDIFFSTORE joe_diff_peter joe's_movies peter's_movies
 (integer) 2
 redis> SMEMBERS joe_diff_peter
 1) "hi, lady"
 2) "Fast Five"
```