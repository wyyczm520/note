# Redis安全设置

redis默认安装完成后，没有登录密码和安全设置，没有密码的系统安全隐患很大，另外如果发生一些误操作，比如FLUSHALL会导致整个库被清空，所以为了安全起见，需要设置reids的安全配置项，具体内容如下：

### 1) 设置密码

找到redis.conf配置文件中的requirepass项，在后面加入redis访问密码，密码一定要有大小写字母、特殊符号、数字和字母组合，例如：QwE!@#7a8B
requirepass yourpassword

### 2) 禁用危险指令

在redis.conf中，通过将指令禁用达到避免人为误操作的可能
rename-command 是表示重命名指令，如果是指令后是空字符串 "" ， 那么就表示禁用，如果填入其他值，就表示改名，另外还有一个更绝的方法，是redis在安装时，修改redis的源码src/redis.c中。
研发环境可以改名，但是生产环境建议禁用。

```
{"flushdb",flushdbCommand,1,"w",0,NULL,0,0,0,0,0},
{"flushall",flushallCommand,1,"w",0,NULL,0,0,0,0,0}
```

的部分，注释掉也可以解决。

主要的危险命令如下：

FLUSHALL 删除所有库中的key

FLUSHDB 删除当前库中所有的key

KEYS 匹配数据库中的key 如果是在生产库使用KEYS * 对性能影响非常大，研发或者测试环境可以保留

以下是具体使用方法：

```
rename-command FLUSHALL ""
rename-command FLUSHDB ""
rename-command KEYS ""
```