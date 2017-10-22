# sentinel.conf 解析

**属性:** port 26379

**说明:** Sentinel实例的端口号

---

**属性:** dir /tmp

**说明:** Sentinel 实例的目录

---

**属性:** logfile /var/log/redis/redis-server.log

**说明:** 日志文件

---

**属性:** daemonize yes

**说明:** 后台执行

---

**属性:** protected-mode no

**说明:** 3.2里的参数，是否开启保护模式，默认开启。要是配置里没有指定bind和密码。开启该参数后，redis只会本地进行访问，拒绝外部访问。要是开启了密码 和bind，可以开启。否 则最好关闭，设置为no。

---

**属性:** sentinel monitor mymaster 127.0.0.1 6379 2

**说明:** 格式：sentinel <option_name> <master_name> <option_value>；这一行代表sentinel监控的master的名字叫做mymaster,地址为127.0.0.1:6379，行尾最后的一个2代表什么意思呢？我们知道，网络是不可靠的，有时候一个sentinel会因为网络堵塞而误以为一个master redis已经死掉了，当sentinel集群式，解决这个问题的方法就变得很简单，只需要多个sentinel互相沟通来确认某个master是否真的死了，这个2代表，当集群中有2个sentinel认为master死了时，才能真正认为该master已经不可用了。

---

**属性:** sentinel down-after-milliseconds mymaster 30000

**说明:** sentinel会向master发送心跳PING来确认master是否存活，如果master在“一定时间范围”内不回应PONG 或者是回复了一个错误消息，那么这个sentinel会主观地(单方面地)认为这个master已经不可用了(subjectively down, 也简称为SDOWN)。而这个down-after-milliseconds就是用来指定这个“一定时间范围”的，默认单位是毫秒，默认30秒。

---

**属性:** sentinel parallel-syncs mymaster 1

**说明:** 在发生failover主备切换时，这个选项指定了最多可以有多少个slave同时对新的master进行同步，这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave处于不能处理命令请求的状态。

---

**属性:** sentinel failover-timeout mymaster 180000

**说明:** failover过期时间，当failover开始后，在此时间内仍然没有触发任何failover操作，当前sentinel将会认为此次failoer失败。默认180秒，即3minutes.

---

**属性:** vsentinel auth-pass mymaster xxxxxxx

**说明:** 设置连master和slaves验证密码，在监控redis实例时很有用

---

**属性:** sentinel notification-script <master-name> <script-path>

**说明:** 发生切换之后执行的一个自定义脚本：如发邮件、vip切换等

---

**属性:** sentinel client-reconfig-script T1 /opt/bin/notify.py

**说明:** 发生切换之后执行的一个自定义脚本：如发邮件、vip切换等
