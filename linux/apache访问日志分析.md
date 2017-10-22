
##### 当前WEB服务器中联接次数最多的ip地址

```
# netstat -ntu |awk '{print $5}' |sort | uniq -c| sort -nr
```
 
##### 查看日志中访问次数最多的前10个IP

```
# cat access_log  |cut -d ' ' -f 1 |sort |uniq -c | sort -nr | awk '{print $0 }' | head -n 10 |less
```
 
##### 查看日志中出现100次以上的IP

```
# cat access_log  |cut -d ' ' -f 1 |sort |uniq -c | awk '{if ($1 > 100) print $0}'｜sort -nr |less
```

##### 查看最近访问量最高的文件

```
# cat access_log |tail -10000|awk '{print $7}'|sort|uniq -c|sort -nr|less
```

##### 查看日志中访问超过100次的页面

```
# cat access_log | cut -d ' ' -f 7 | sort |uniq -c |  awk '{if ($1 > 100) print $0}' | less
```

##### 统计某url，一天的访问次数
```
# cat access_log|grep '12/Aug/2009'|grep '/images/index/e1.gif'|wc|awk '{print $1}'
```

##### 前五天的访问次数最多的网页

```
# cat access_log|awk '{print $7}'|uniq -c |sort -n -r|head -20
```

##### 从日志里查看该ip在干嘛

```
# cat access_log | grep 218.66.36.119| awk '{print $1"\t"$7}' | sort | uniq -c | sort -nr | less
```

##### 列出传输时间超过 30 秒的文件

```
# cat access_log|awk '($NF > 30){print $7}' |sort -n|uniq -c|sort -nr|head -20
```
 
##### 列出最最耗时的页面(超过60秒的)

```
# cat access_log |awk  '($NF > 60 && $7~/\.php/){print $7}' |sort -n|uniq -c|sort -nr|head -100
```
