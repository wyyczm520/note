## linux下EOF写法梳理


在平时的运维工作中，我们经常会碰到这样一个场景：

执行脚本的时候，需要往一个文件里自动输入N行内容。如果是少数的几行内容，还可以用echo追加方式，但如果是很多行，那么单纯用echo追加的方式就显得愚蠢之极了！

这个时候，就可以使用EOF结合cat命令进行行内容的追加了。

下面就对EOF的用法进行梳理：

EOF是END Of File的缩写,表示自定义终止符.既然自定义,那么EOF就不是固定的,可以随意设置别名,在linux按ctrl-d就代表EOF.

EOF一般会配合cat能够多行文本输出.

其用法如下:

```
<<EOF        //开始
....
EOF            //结束
```

还可以自定义，比如自定义：

```
<<BBB        //开始
....
BBB              //结束
```

通过cat配合重定向能够生成文件并追加操作,在它之前先熟悉几个特殊符号:

```
 < :输入重定向
 > :输出重定向
 >> :输出重定向,进行追加,不会覆盖之前内容
 << :标准输入来自命令行的一对分隔号的中间内容.
```

下面通过具体实例来感受下EOF用法的妙处：
1）向文件test.sh里输入内容。

```
[root@slave-server opt]# cat << EOF >test.sh
> 123123123
> 3452354345
> asdfasdfs
 EOF
```

```
[root@slave-server opt]# cat test.sh
> 123123123
> 3452354345
> asdfasdfs
```
追加内容

```
[root@slave-server opt]# cat << EOF >>test.sh
> 7777
> 8888
> EOF
```

```[root@slave-server opt]# cat test.sh
> 123123123
> 3452354345
> asdfasdfs
> 7777
> 8888
```

覆盖

```
[root@slave-server opt]# cat << EOF >test.sh
> 55555
> EOF
[root@slave-server opt]# cat test.sh
55555
```

2）自定义EOF，比如自定义为wang

```
[root@slave-server opt]# cat << wang > haha.txt
> ggggggg
> 4444444
> 6666666
> wang
[root@slave-server opt]# cat haha.txt
ggggggg
4444444
6666666
```

3）可以编写脚本，向一个文件输入多行内容

```
[root@slave-server opt]# touch /usr/local/mysql/my.cnf               //文件不提前创建也行，如果不存在，EOF命令中也会自动创建
[root@slave-server opt]# vim test.sh
#!/bin/bash

cat > /usr/local/mysql/my.cnf << EOF                                      //或者cat << EOF > /usr/local/mysql/my.cnf
[client]
port = 3306
socket = /usr/local/mysql/var/mysql.sock

[mysqld]
port = 3306
socket = /usr/local/mysql/var/mysql.sock

basedir = /usr/local/mysql/
datadir = /data/mysql/data
pid-file = /data/mysql/data/mysql.pid
user = mysql
bind-address = 0.0.0.0
server-id = 1
sync_binlog=1
log_bin = mysql-bin

[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 4M
write_buffer = 4M

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
port = 3306
EOF

[root@slave-server opt]# sh test.sh           //执行上面脚本
[root@slave-server opt]# cat /usr/local/mysql/my.cnf    //检查脚本中的EOF是否写入成功
[client]
port = 3306
socket = /usr/local/mysql/var/mysql.sock

[mysqld]
port = 3306
socket = /usr/local/mysql/var/mysql.sock

basedir = /usr/local/mysql/
datadir = /data/mysql/data
pid-file = /data/mysql/data/mysql.pid
user = mysql
bind-address = 0.0.0.0
server-id = 1
sync_binlog=1
log_bin = mysql-bin

[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 4M
write_buffer = 4M

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
port = 3306
```
