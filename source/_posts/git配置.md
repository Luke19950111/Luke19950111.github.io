---
title: git配置
date: 2019-06-12 11:30:36
tags: git
categories: git
---
<center>git配置可以用命令行，也可以直接改.gitconfig文件</center>
<!-- more -->

今天从github上down一个仓库，连连报错，原来都是git配置的锅。


报错

```
error: RPC failed; result=56, HTTP code = 2008.82 MiB | 4.72 MiB/s   
fatal: The remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed
```

或

```
RPC failed; curl 18 transfer closed with outstanding read data remaining
```

缓存区溢出，修改git传输字节限制 ，执行以下命令

`git config --global http.postBuffer  524288000`

如果依然clone失败，可能是网络下载速度过慢，执行以下命令

`git config --global http.lowSpeedLimit 0`

`git config --global http.lowSpeedTime 999999`

如果依旧clone失败，则首先浅层clone，然后更新远程库到本地

`git clone --depth=1 http://xxx.git`

`git fetch --unshallow`


报错

```
error: RPC failed; curl 56 OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 10054
```

`git config http.sslVerify "false"`

报错

```
remote: fatal: early EOF
```

.gitconfig里加上(与上文执行命令等同)

```
[core]
    compression = -1
```