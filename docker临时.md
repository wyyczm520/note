docker 信息

```
[root@server01 mapper]# docker info
Containers: 11
 Running: 10
 Paused: 0
 Stopped: 1
Images: 4
Server Version: 1.10.3
Storage Driver: devicemapper
 Pool Name: docker-253:0-1706593-pool
 Pool Blocksize: 65.54 kB
 Base Device Size: 10.74 GB
 Backing Filesystem: xfs
 Data file: /dev/loop0
 Metadata file: /dev/loop1
 Data Space Used: 911.1 MB
 Data Space Total: 107.4 GB
 Data Space Available: 43.79 GB
 Metadata Space Used: 2.097 MB
 Metadata Space Total: 2.147 GB
 Metadata Space Available: 2.145 GB
 Udev Sync Supported: true
 Deferred Removal Enabled: false
 Deferred Deletion Enabled: false
 Deferred Deleted Device Count: 0
 Data loop file: /var/lib/docker/devicemapper/devicemapper/data
 WARNING: Usage of loopback devices is strongly discouraged for production use. Either use `--storage-opt dm.thinpooldev` or use `--storage-opt dm.no_warn_on_loop_devices=true` to suppress this warning.
 Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
 Library Version: 1.02.107-RHEL7 (2016-06-09)
Execution Driver: native-0.2
Logging Driver: journald
Plugins:
 Volume: local
 Network: null host bridge
Kernel Version: 3.10.0-327.36.3.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
Number of Docker Hooks: 2
CPUs: 2
Total Memory: 1.793 GiB
Name: server01
ID: WB2Q:UMBJ:ANGE:BSP2:YKX2:62GC:KF73:CD5N:NQBT:V3YE:6UQC:3Z5N
WARNING: IPv4 forwarding is disabled
WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
Registries: docker.io (secure)
```

##### systemctl status docker

```
[root@server01 mapper]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
  Drop-In: /usr/lib/systemd/system/docker.service.d
           └─flannel.conf
   Active: active (running) since Sun 2017-01-15 13:41:14 HKT; 6 months 20 days ago
     Docs: http://docs.docker.com
 Main PID: 2569 (docker-current)
   Memory: 75.3M
   CGroup: /system.slice/docker.service
           └─2569 /usr/bin/docker-current daemon --exec-opt native.cgroupdriver=systemd --selinux-enabled --log-driver=journald --bip=172.17.38.1/2...

Mar 26 18:47:27 server01 docker-current[2569]: time="2017-03-26T18:47:27.734639130+08:00" level=info msg="{Action=start, Username=root, Log...=12883}"
Mar 26 18:47:27 server01 docker-current[2569]: time="2017-03-26T18:47:27.738483970+08:00" level=info msg="{Action=resize, Username=root, Lo...=12883}"
Mar 26 18:53:10 server01 docker-current[2569]: time="2017-03-26T18:53:10.346791313+08:00" level=info msg="{Action=stop, ID=e4a9632530e1773a...D=3157}"
Mar 26 18:53:10 server01 docker-current[2569]: time="2017-03-26T18:53:10.530819882+08:00" level=info msg="{Action=stop, ID=71469e7b7c5b490a...D=3157}"
Mar 26 18:53:28 server01 docker-current[2569]: time="2017-03-26T18:53:28.449983803+08:00" level=info msg="{Action=remove, ID=e4a9632530e177...D=3157}"
Mar 26 18:53:28 server01 docker-current[2569]: time="2017-03-26T18:53:28.809929518+08:00" level=info msg="{Action=remove, ID=71469e7b7c5b49...D=3157}"
Mar 26 18:53:29 server01 docker-current[2569]: time="2017-03-26T18:53:29.176760934+08:00" level=error msg="Handler for GET /containers/7146...37825dc"
Mar 26 18:53:52 server01 docker-current[2569]: time="2017-03-26T18:53:52.824564225+08:00" level=info msg="{Action=exec, ID=460f423f58f0120d...=14699}"
Mar 26 18:53:52 server01 docker-current[2569]: time="2017-03-26T18:53:52.827445582+08:00" level=info msg="{Action=start, Username=root, Log...=14699}"
Mar 26 18:53:52 server01 docker-current[2569]: time="2017-03-26T18:53:52.831954436+08:00" level=info msg="{Action=resize, Username=root, Lo...=14699}"
Hint: Some lines were ellipsized, use -l to show in full.
```

#####

```
[root@server01 mapper]# pwd
/dev/mapper
[root@server01 mapper]# ls
control
docker-253:0-1706593-08955a14a0a6af2caa598f31c23f8208ae719321c9426ca35027b511b2b8eed8
docker-253:0-1706593-1b795fe89f46d2c0c354b3171eec66438cebceaf76c56de2c4ed7e81a6231d84
docker-253:0-1706593-230b9902f47923847c4651d2d4deb6f9f57bad1d7be38c3d544baaa81f4b4f6e
docker-253:0-1706593-2431847a38fc8da246434eef76730b8c7ee22c115c9ad88293902b1f443a6a0b
docker-253:0-1706593-4b6238230e76a20cee9bdcd5ec2dec4c423c22eb71a51463eec08251186761a0
docker-253:0-1706593-55177b13dcb3e7a38781ac0fb41c6b1d4013ab01a2a4103d0a87cbf4ca5280fc
docker-253:0-1706593-8119dc39d939bf5c61e02cd2c485b0cc8b39ec8d7eb5b76e8a4e23896fc0e668
docker-253:0-1706593-d03a0c3106a2da0b876ddbfdbb5c97a306c970bb2e7892e12af61b87a195eff1
docker-253:0-1706593-d6f09f3b154b95250bd12b1e1b5c77e7ace765febbc5ce6a510058f82aa321e4
docker-253:0-1706593-f8cba53af1e3399c5252c1c8c35f8bd34ec61d80549a198ff8e5d370f179658f
docker-253:0-1706593-pool
VolGroup-lv_home
VolGroup-lv_root
VolGroup-lv_swap
```
