
```
# tar -zxvf rubygems-0.9.0.tgz
# cd rubygems-0.9.0
# ruby setup.rb
```

检查是否安装成功

```
# gem -v
```

安装redis gem

```
1. 下载源文件redis-3.2.1.gem
2. 安装 gem install –l redis-3.2.1.gem
```

如果安装redis-3.2.1.gem出错，请执行如下步骤

安装zlib

````
1. tar -xzvf zlib-1.2.8.tar.gz
2. cd zlib-1.2.8
3. ./configure --prefix=/opt/zlib
4. make
5. make install
```

安装ruby-zlib

```
1. cd ruby-2.1.6/ext/zlib
2. ruby ./extconf.rb --with-zlib-dir=/opt/zlib
3. make
4. make install
```

最后安装redis-3.2.1.gem

```
gem install -l redis-3.2.1.gem
 ```
