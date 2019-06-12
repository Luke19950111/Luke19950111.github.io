---
title: 一个简易server
date: 2018-04-27 15:01:45
tags: [Node.js, server]
categories: Node.js
---
<center>自己写个服务器试试</center>
<!-- more -->

Tim Berners-Lee写出了第一个网页、第一个浏览器和第一个服务器。
网页我们见过，浏览器我们见过，但我们还没见过服务器。
了解服务器的原理是非常重要的。

我们可以自己搞一个服务器，然后提供HTTP服务。
1. 我们已经有一个服务器了，我们所使用的电脑就是服务器。（相对我们的电脑，服务器有更好的CPU，更高的内存，不需要显卡和显示器等）
2. 但是我们还没有提供HTTP服务的程序。

用脚本就可以提供HTTP服务，我们就用Node.js脚本试试吧。

#### 接收请求
用一个文件就可以实现：
1. 新建一个安全目录，进入目录`cd ~/Desktop; mkdir node-demo; cd node-demo`
2. `touch server.js`
3. 编辑它，[内容](https://github.com/Luke19950111/node-demo/blob/master/server-before.js)
4. 运行`node server`或`node server.js`(会看到提示要加端口号)
5. 根据提示调整命令
6. 成功以后，这个server保持运行(ctrl+c可中断)

服务器完成。
这个服务器目前只有一个功能，就是打印出路径和查询字符串。
还缺少一个重要功能，发出HTTP响应。

打开一个新的bash窗口，运行`curl http://localhost:你指定的端口号/xxx`或`crul http://127.0.0.1:你指定的端口号/xxx`（或者直接用浏览器访问）
你会发现server打印出了路径。
```
$ node server-before.js 8888
监听 8888 成功
请用在空中转体720度然后用电饭煲打开 http://localhost:8888
得到 HTTP 路径
/xxx
查询字符串为

不含查询字符串的路径为
/xxx

```
（如果你的手机和你的电脑在同一个内网中，你可以试试用手机来访问这个server，同样会打印出路径）

1. 这说明server收到了我们用curl发出的请求；
2. 但是由于server没有发出响应，curl就一直等在那里，无法退出（ctrl+c中断这个傻curl吧）。

#### 发出响应
接下来我们让server发出响应。
1. 便捷server.js
2. 在中间标注区间添加两行代码
```
  response.write('Hi')
  response.end()
```
3. 中断之前的server，重新运行`node server 8888`
4. 运行`curl http://localhost:8888/xxx`,结果如下：
```
 Hi% //%表示结尾，不想看到它可以把'Hi'换成'Hi\n'
```
5. 好了，响应添加成功
6. 使用`curl -s -v -- "http://localhost:8888/xxx"`可以查看完整的请求和响应

注意：
1. 后缀是没有用的，文件内容是由HTTP头中的Content-Type保证的；
2. HTTP路径不是文件路径，/xxx.html不一定对应xxx.html文件。
这个脚本返回一个页面（ [代码](https://github.com/Luke19950111/node-demo/blob/master/server.js) ）。注意路径中的后缀都是混乱的，返回的内容根据Content-Type而定。