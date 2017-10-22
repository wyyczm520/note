## timer

### 含义

返回当前服务器时间。

### 返回值

一个包含两个字符串的列表： 第一个字符串是当前时间(以 UNIX 时间戳格式表示)，而第二个字符串是当前这一秒钟已经逝去的微秒数。

### 示例

```
redis> TIME
 1) "1332395997"
 2) "952581"
 redis> TIME
 1) "1332395997"
 2) "953148"
```

## dbsize

### 含义
 
返回当前数据库的 key 的数量。
 
### 返回值
 
当前数据库的 key 的数量。

### 示例

```
 redis> DBSIZE
 (integer) 5
 redis> SET new_key "hello_moto" # 增加一个 key 试试
 OK
 redis> DBSIZE
 (integer) 6
```

## bgrewriteaof

### 含义

执行一个 AOF文件 重写操作。重写会创建一个当前 AOF 文件的体积优化版本。

即使 BGREWRITEAOF 执行失败，也不会有任何数据丢失，因为旧的 AOF 文件在 BGREWRITEAOF 成功之前不会被修改。

重写操作只会在没有其他持久化工作在后台执行时被触发，也就是说：

如果 Redis 的子进程正在执行快照的保存工作，那么 AOF 重写的操作会被预定(scheduled)，等到保存工作完成之后再执行 AOF 重写。在这种情况下， 
BGREWRITEAOF 的返回值仍然是 OK ，但还会加上一条额外的信息，说明 BGREWRITEAOF要等到保存操作完成之后才能执行。在 Redis 2.6 或以上的版本，可以使用 INFO 命令查看 BGREWRITEAOF 是否被预定。

如果已经有别的 AOF 文件重写在执行，那么 BGREWRITEAOF 返回一个错误，并且这个新的 BGREWRITEAOF 请求也不会被预定到下次执行。

从 Redis 2.4 开始， AOF 重写由 Redis 自行触发， BGREWRITEAOF 仅仅用于手动触发重写操作。

请移步 持久化文档(英文) 查看更多相关细节。

### 返回值

反馈信息。

### 示例

```
redis> BGREWRITEAOF
Background append only file rewriting started
```

## bgsave

### 含义

BGSAVE 命令执行之后立即返回 OK ，然后 Redis fork 出一个新子进程，原来的 
Redis 进程(父进程)继续处理客户端请求，而子进程则负责将数据保存到磁盘，然后退出。

客户端可以通过 LASTSAVE 命令查看相关信息，判断 BGSAVE 命令是否执行成功。

请移步 持久化文档 查看更多相关细节。

### 返回值

反馈信息。

### 示例

```
redis> BGSAVE
Background saving started
```

## save

### 含义

SAVE 命令执行一个同步保存操作，将当前 Redis 实例的所有数据快照(snapshot)以 RDB 文件的形式保存到硬盘。

一般来说，在生产环境很少执行 SAVE 操作，因为它会阻塞所有客户端，保存数据库的任务通常由 BGSAVE 命令异步地执行。然而，如果负责保存数据的后台子进程不幸出现问题时， SAVE 可以作为保存数据的最后手段来使用。

请参考文档： Redis 的持久化运作方式(英文) 以获取更多消息。

### 返回值

保存成功时返回 OK 。

### 示例

```
redis> SAVE
 OK
```

## lastsave

### 含义

返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示。

### 返回值

一个 UNIX 时间戳。

### 示例

```
redis> LASTSAVE
 (integer) 1324043588S
```

## SLAVEOF

### 含义

SLAVEOF 命令用于在 Redis 运行时动态地修改复制(replication)功能的行为。

通过执行 SLAVEOF host port 命令，可以将当前服务器转变为指定服务器的从属服务器(slave server)。

如果当前服务器已经是某个主服务器(master server)的从属服务器，那么执行 SLAVEOF host port 将使当前服务器停止对旧主服务器的同步，丢弃旧数据集，转而开始对新主服务器进行同步。

另外，对一个从属服务器执行命令 SLAVEOF NO ONE 将使得这个从属服务器关闭复制功能，并从从属服务器转变回主服务器，原来同步所得的数据集不会被丢弃。

利用『 SLAVEOF NO ONE 不会丢弃同步所得数据集』这个特性，可以在主服务器失败的时候，将从属服务器用作新的主服务器，从而实现无间断运行。

### 返回值

总是返回 OK 。

### 示例

```
 redis> SLAVEOF 127.0.0.1 6379
 OK
 redis> SLAVEOF NO ONE
 OK
```

## flushall

### 含义

清空整个 Redis 服务器的数据(删除所有数据库的所有 key )。

此命令从不失败。

### 返回值

总是返回 OK 。

### 示例

```
 redis> DBSIZE # 0 号数据库的 key 数量
 (integer) 9
 redis> SELECT 1 # 切换到 1 号数据库
 OK
 redis[1]> DBSIZE # 1 号数据库的 key 数量
 (integer) 6
 redis[1]> flushall # 清空所有数据库的所有 key
 OK
 redis[1]> DBSIZE # 不但 1 号数据库被清空了
 (integer) 0
 redis[1]> SELECT 0 # 0 号数据库(以及其他所有数据库)也一样
 OK
 redis> DBSIZE
 (integer) 0
```

## flushdb

### 含义

清空当前数据库中的所有 key。

此命令从不失败。

### 返回值

总是返回 OK 。

### 示例

```
 redis> DBSIZE # 清空前的 key 数量
 (integer) 4
 redis> FLUSHDB
 OK
 redis> DBSIZE # 清空后的 key 数量
 (integer) 0
```

## shutdown

### 含义

SHUTDOWN 命令执行以下操作：

停止所有客户端

如果有至少一个保存点在等待，执行 SAVE 命令

如果 AOF 选项被打开，更新 AOF 文件

关闭 redis 服务器(server)

如果持久化被打开的话， SHUTDOWN 命令会保证服务器正常关闭而不丢失任何数据。

另一方面，假如只是单纯地执行 SAVE 命令，然后再执行 QUIT 命令，则没有这一保证 —— 因为在执行 SAVE 之后、执行 QUIT之前的这段时间中间，其他客户端可能正在和服务器进行通讯，这时如果执行 QUIT 就会造成数据丢失。

SAVE 和 NOSAVE 修饰符

通过使用可选的修饰符，可以修改 SHUTDOWN 命令的表现。比如说：

执行 SHUTDOWN SAVE 会强制让数据库执行保存操作，即使没有设定(configure)保存点

执行 SHUTDOWN NOSAVE 会阻止数据库执行保存操作，即使已经设定有一个或多个保存点(你可以将这一用法看作是强制停止服务器的一个假想的 ABORT 命令)

### 返回值

执行失败时返回错误。

执行成功时不返回任何信息，服务器和客户端的连接断开，客户端自动退出。

### 示例

```
 redis> PING
 PONG
 redis> SHUTDOWN
 $
 $ redis
 Could not connect to Redis at: Connection refused
 not connected>
```

## debug object

### 含义

DEBUG OBJECT 是一个调试命令，它不应被客户端所使用。
 
查看 OBJECT 命令获取更多信息。
 
### 返回值

当 key 存在时，返回有关信息。

当 key 不存在时，返回一个错误。

### 示例

```
 redis> DEBUG OBJECT my_pc
 Value at:0xb6838d20 refcount:1 encoding:raw serializedlength:9 lru:283790 lru_seconds_idle:150
 redis> DEBUG OBJECT your_mac
 (error) ERR no such key
```

## debug segrault

### 含义

执行一个不合法的内存访问从而让 Redis 崩溃，仅在开发时用于 BUG 模拟。

### 返回值

无

### 示例

```
 redis> DEBUG SEGFAULT
 Could not connect to Redis at: Connection refused
 not connected>
```

## monitor

实时打印出 Redis 服务器接收到的命令，调试用。

### 返回值

总是返回 OK 。

### 示例

```
redis> MONITOR
OK
 1324109476.800290 "MONITOR" # 第一个值是 UNIX 时间戳，之后是执行的命令和参数
 1324109479.632445 "PING"
 1324109486.408230 "SET" "greeting" "hello moto"
 1324109490.800364 "KEYS" "*"
 1324109509.023495 "lrange" "my_book_list" "0" "-1"
```

## sync

### 含义

用于复制功能(replication)的内部命令。

### 返回值

不明确

### 示例

```
redis> SYNC
"REDIS0002\xfe\x00\x00\auser_id\xc0\x03\x00\anumbers\xc2\xf3\xe0\x01\x00\x00\tdb_number\xc0\x00\x00\x04name\x06huangz\x00\anew_key\nhello_moto\x00\bgreeting\nhello moto\x00\x05my_pc\bthinkpad\x00\x04lock\xc0\x01\x00\nlock_times\xc0\x04\xfe\x01\t\x04info\x19\x02\x04name\b\x00zhangyue\x03age\x02\x0022\xff\t\aooredis,\x03\x04name\a\x00ooredis\aversion\x03\x001.0\x06author\x06\x00huangz\xff\x00\tdb_number\xc0\x01\x00\x05greet\x0bhello world\x02\nmy_friends\x02\x05marry\x04jack\x00\x04name\x05value\xfe\x02\x0c\x01s\x12\x12\x00\x00\x00\r\x00\x00\x00\x02\x00\x00\x01a\x03\xc0f'\xff\xff"
 (1.90s)
```