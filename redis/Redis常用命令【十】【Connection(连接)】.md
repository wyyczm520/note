## auth

### 含义

通过设置配置文件中 requirepass 项的值(使用命令 CONFIG SET requirepass password )，可以使用密码来保护 Redis 服务器。

如果开启了密码保护的话，在每次连接 Redis 服务器之后，就要使用 AUTH 命令解锁，解锁之后才能使用其他 Redis 命令。

如果 AUTH 命令给定的密码 password 和配置文件中的密码相符的话，服务器会返回 OK 并开始接受命令输入。

另一方面，假如密码不匹配的话，服务器将返回一个错误，并要求客户端需重新输入密码。

### 返回值

密码匹配时返回 OK ，否则返回一个错误。

### 示例

```
 # 设置密码
 redis> CONFIG SET requirepass secret_password # 将密码设置为 secret_password
 OK
 redis> QUIT # 退出再连接，让新密码对客户端生效
 [huangz@mypad]$ redis
 redis> PING # 未验证密码，操作被拒绝
 (error) ERR operation not permitted
 redis> AUTH wrong_password_testing # 尝试输入错误的密码
 (error) ERR invalid password
 redis> AUTH secret_password # 输入正确的密码
 OK
 redis> PING # 密码验证成功，可以正常操作命令了
 PONG
 # 清空密码
 redis> CONFIG SET requirepass "" # 通过将密码设为空字符来清空密码
 OK
 redis> QUIT
 $ redis # 重新进入客户端
 redis> PING # 执行命令不再需要密码，清空密码操作成功
 PONG
```

## ping

### 含义

使用客户端向 Redis 服务器发送一个 PING ，如果服务器运作正常的话，会返回一个 PONG 。

通常用于测试与服务器的连接是否仍然生效，或者用于测量延迟值。

### 返回值

如果连接正常就返回一个 PONG ，否则返回一个连接错误。

### 示例

```
 # 客户端和服务器连接正常
 redis> PING
 PONG
 # 客户端和服务器连接不正常(网络不正常或服务器未能正常运行)
 redis 127.0.0.1:6379> PING
 Could not connect to Redis at 127.0.0.1:6379: Connection refused
```

## select

### 含义

切换到指定的数据库，数据库索引号 index 用数字值指定，以 0 作为起始索引值。

默认使用 0 号数据库。

### 返回值

OK

### 示例

```
 redis> SET db_number 0 # 默认使用 0 号数据库
 OK
 redis> SELECT 1 # 使用 1 号数据库
 OK
 redis[1]> GET db_number # 已经切换到 1 号数据库，注意 Redis 现在的命令提示符多了个 [1]
 (nil)
 redis[1]> SET db_number 1
 OK
 redis[1]> GET db_number
 "1"
 redis[1]> SELECT 3 # 再切换到 3 号数据库
 OK
 redis[3]> # 提示符从 [1] 改变成了 [3]
```

## echo

### 含义

打印一个特定的信息 message ，测试时使用。

### 返回值

message 自身。

### 示例

```
 redis> ECHO "Hello Moto"
 "Hello Moto"
 redis> ECHO "Goodbye Moto"
 "Goodbye Moto"
```

## quit

### 含义

请求服务器关闭与当前客户端的连接。
 
一旦所有等待中的回复(如果有的话)顺利写入到客户端，连接就会被关闭。
 
### 返回值

总是返回 OK (但是不会被打印显示，因为当时 Redis-cli 已经退出)。

### 示例

```
 $ redis
 redis> QUIT
 $
```