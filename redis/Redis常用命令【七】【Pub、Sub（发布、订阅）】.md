# [Pub、Sub发布，订阅]

## publish

### 含义

将信息 message 发送到指定的频道 channel 。

### 返回值

接收到信息 message 的订阅者数量。

### 示例

```
 # 对没有订阅者的频道发送信息
 redis> publish bad_channel "can any body hear me?"
 (integer) 0
 # 向有一个订阅者的频道发送信息
 redis> publish msg "good morning"
 (integer) 1
 # 向有多个订阅者的频道发送信息
 redis> publish chat_room "hello~ everyone"
 (integer) 3
```

## subscribe

### 含义 

订阅给定的一个或多个频道的信息。
 
### 返回值
 
接收到的信息(请参见下面的代码说明)。

### 示例

```
 # 订阅 msg 和 chat_room 两个频道
 # 1 - 6 行是执行 subscribe 之后的反馈信息
 # 第 7 - 9 行才是接收到的第一条信息
 # 第 10 - 12 行是第二条
 redis> subscribe msg chat_room
 Reading messages... (press Ctrl-C to quit)
 1) "subscribe" # 返回值的类型：显示订阅成功
 2) "msg" # 订阅的频道名字
 3) (integer) 1 # 目前已订阅的频道数量
 1) "subscribe"
 2) "chat_room"
 3) (integer) 2
 1) "message" # 返回值的类型：信息
 2) "msg" # 来源(从那个频道发送过来)
 3) "hello moto" # 信息内容
 1) "message"
 2) "chat_room"
 3) "testing...haha"
```
 
## psubscribe

### 含义

订阅一个或多个符合给定模式的频道。

每个模式以 * 作为匹配符，比如 it* 匹配所有以 it 开头的频道( it.news 、 it.blog 、 it.tweets 等等)， news.* 匹配所有以news. 开头的频道( news.it 、 news.global.today 等等)，诸如此类。

### 返回值

接收到的信息(请参见下面的代码说明)。

### 示例

```
 # 订阅 news.* 和 tweet.* 两个模式
 # 第 1 - 6 行是执行 psubscribe 之后的反馈信息
 # 第 7 - 10 才是接收到的第一条信息
 # 第 11 - 14 是第二条
 # 以此类推。。。
 redis> psubscribe news.* tweet.*
 Reading messages... (press Ctrl-C to quit)
 1) "psubscribe" # 返回值的类型：显示订阅成功
 2) "news.*" # 订阅的模式
 3) (integer) 1 # 目前已订阅的模式的数量
 1) "psubscribe"
 2) "tweet.*"
 3) (integer) 2
 1) "pmessage" # 返回值的类型：信息
 2) "news.*" # 信息匹配的模式
 3) "news.it" # 信息本身的目标频道
 4) "Google buy Motorola" # 信息的内容
 1) "pmessage"
 2) "tweet.*"
 3) "tweet.huangz"
 4) "hello"
 1) "pmessage"
 2) "tweet.*"
 3) "tweet.joe"
 4) "@huangz morning"
 1) "pmessage"
 2) "news.*"
 3) "news.life"
 4) "An apple a day, keep doctors away"
```
 
## unsubscribe

### 含义
 
指示客户端退订给定的频道。
 
如果没有频道被指定，也即是，一个无参数的 UNSUBSCRIBE 调用被执行，那么客户端使用 SUBSCRIBE 命令订阅的所有频道都会被退订。在这种情况下，命令会返回一个信息，告知客户端所有被退订的频道。
 
### 返回值

这个命令在不同的客户端中有不同的表现。
 
## punsubscribe

### 含义

指示客户端退订所有给定模式。
 
如果没有模式被指定，也即是，一个无参数的 PUNSUBSCRIBE 调用被执行，那么客户端使用 PSUBSCRIBE 命令订阅的所有模式都会被退订。在这种情况下，命令会返回一个信息，告知客户端所有被退订的模式。
 
### 返回值

这个命令在不同的客户端中有不同的表现。
