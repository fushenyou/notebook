# 安装 nginx naxis

## 解压 nginx-1.10.2.tar.gz

-   命令格式：tar zxvf 压缩文件名.tar.gz
-   tar zxvf nginx-1.10.2.tar.gz ##解压 naxis 0.55.3.tar.gz
-   tar zxvf 0.55.3.tar.gz

## 安装 nginx

```
cd nginx-x.x.xx/
./configure --prefix=/usr/local/nginx-1.10.2  --add-module=../naxsi-x.xx/naxsi_src/ [add/remove your favorite/usual options]
make
make install
```

## 检查 nginx 依赖库

```
ldd $(which /usr/local/nginx-1.10.2/sbin/nginx)
ls /lib64/ | grep pcre
ln -s /lib64/libpcre.so.0.0.1 /lib64/libpcre.so.1
```

## 配置 nginx naxis

```
http {
 include naxsi_core.rules;
 ...
 server {
  listen ...;
  server_name  ...;
  add_header Set-Cookie "HttpOnly=true";
  add_header X-Frame-Options DENY;
  location /RequestDenied {
      return 403;
  }
  location / {
   #Enable naxsi
   SecRulesEnabled;
   #Enable learning mode
   #LearningMode;
   #Define where blocked requests go
   DeniedUrl "/RequestDenied";
   #CheckRules, determining when naxsi needs to take action
   CheckRule "$SQL >= 8" BLOCK;
   CheckRule "$RFI >= 8" BLOCK;
   CheckRule "$TRAVERSAL >= 4" BLOCK;
   CheckRule "$EVADE >= 4" BLOCK;
   #CheckRule "$XSS >= 8" BLOCK;
   #naxsi logs goes there
   error_log /usr/local/nginx/logs/denied.log;
   ...
  }
 }
```
```
#Enable naxsi
SecRulesEnabled;
#Enable learning mode
#LearningMode;
#Define where blocked requests go
DeniedUrl "/RequestDenied";
#CheckRules, determining when naxsi needs to take action
CheckRule "$SQL >= 8" BLOCK;
CheckRule "$RFI >= 8" BLOCK;
CheckRule "$TRAVERSAL >= 4" BLOCK;
CheckRule "$EVADE >= 4" BLOCK;
#CheckRule "$XSS >= 8" BLOCK;
#naxsi logs goes there
error_log /usr/local/nginx-1.10.2/logs/denied.log;
```
# linux 常用命令

###查看系统参数
cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
cat /proc/cpuinfo | grep "processor" | wc -l
cat /proc/cpuinfo | grep "cpu cores" | uniq
cat /proc/cpuinfo | grep -e "cpu cores" -e "siblings" | sort | uniq

grep '[1-9]\{4\}\.[0-9]\{3\}' access.log |awk -F '|' '{print $1,$4,$5,$9,$10}'
grep '[1-9]\{4\}\.[0-9]\{3\}ms'

grep '[1-9]\{4\}\.[0-9]\{3\}' access.log |grep 'wechatLogin' | awk -F '|' '{print $1,$4,$5,$9,$10}'
grep '06/Jul/2018:13:[00-30]' access.log | grep 'wechatLogin' | awk -F '|' '{print $1,$4,$5,$9,$10}'

awk -F '|' '{print $1,$4,$5,$9,$10}' access.log | grep '[1-9]\{1\}\.[1-9]\{3\}' | grep '/common/wapApi'

# netstat 命令详解

```
功能说明：显示网络状态。
语　　法：netstat [-acCeFghilMnNoprstuvVwx] [-A<网络类型>][--ip]
补充说明：利用netstat指令可让你得知整个Linux系统的网络情况。
参　　数：
-a或–all 显示所有连线中的Socket。
-A<网络类型>或–<网络类型> 列出该网络类型连线中的相关地址。
-c或–continuous 持续列出网络状态。
-C 或–cache 显示路由器配置的快取信息。
-e或–extend 显示网络其他相关信息。
-F或 –fib 显示FIB。
-g或–groups 显示多重广播功能群组组员名单。
-h或–help 在线帮助。
-i 或–interfaces 显示网络界面信息表单。
-l或–listening 显示监控中的服务器的Socket。
-M 或–masquerade 显示伪装的网络连线。
-n或–numeric 直接使用IP地址，而不通过域名服务器。
-N 或–netlink或–symbolic 显示网络硬件外围设备的符号连接名称。
-o或–timers 显示计时器。
-p 或–programs 显示正在使用Socket的程序识别码和程序名称。
-r或–route 显示 Routing Table。
-s或–statistice 显示网络工作信息统计表。
-t或–tcp 显示TCP 传输协议的连线状况。
-u或–udp 显示UDP传输协议的连线状况。
-v或–verbose 显示指令执行过程。
-V 或–version 显示版本信息。
-w或–raw 显示RAW传输协议的连线状况。
-x或–unix 此参数的效果和指定”-A unix”参数相同。
–ip或–inet 此参数的效果和指定”-A inet”参数相同。

网络连接状态详解
共有12中可能的状态，前面11种是按照TCP连接建立的三次握手和TCP连接断开的四次挥手过程来描述的。
1)、LISTEN:首先服务端需要打开一个socket进行监听，状态为LISTEN./* The socket is listening for incoming connections. 侦听来自远方TCP端口的连接请求 */

2)、SYN_SENT:客户端通过应用程序调用connect进行active open.于是客户端tcp发送一个SYN以请求建立一个连接.之后状态置为SYN_SENT./*The socket is actively attempting to establish a connection. 在发送连接请求后等待匹配的连接请求 */

3)、SYN_RECV:服务端应发出ACK确认客户端的 SYN,同时自己向客户端发送一个SYN. 之后状态置为SYN_RECV/* A connection request has been received from the network. 在收到和发送一个连接请求后等待对连接请求的确认 */

4)、ESTABLISHED: 代表一个打开的连接，双方可以进行或已经在数据交互了。/* The socket has an established connection. 代表一个打开的连接，数据可以传送给用户 */

5)、FIN_WAIT1:主动关闭(active close)端应用程序调用close，于是其TCP发出FIN请求主动关闭连接，之后进入FIN_WAIT1状态./* The socket is closed, and the connection is shutting down. 等待远程TCP的连接中断请求，或先前的连接中断请求的确认 */

6)、CLOSE_WAIT:被动关闭(passive close)端TCP接到FIN后，就发出ACK以回应FIN请求(它的接收也作为文件结束符传递给上层应用程序),并进入CLOSE_WAIT./* The remote end has shut down, waiting for the socket to close. 等待从本地用户发来的连接中断请求 */

7)、FIN_WAIT2:主动关闭端接到ACK后，就进入了 FIN-WAIT-2 ./* Connection is closed, and the socket is waiting for a shutdown from the remote end. 从远程TCP等待连接中断请求 */

8)、LAST_ACK:被动关闭端一段时间后，接收到文件结束符的应用程序将调用CLOSE关闭连接。这导致它的TCP也发送一个 FIN,等待对方的ACK.就进入了LAST-ACK ./* The remote end has shut down, and the socket is closed. Waiting for acknowledgement. 等待原来发向远程TCP的连接中断请求的确认 */

9)、TIME_WAIT:在主动关闭端接收到FIN后，TCP 就发送ACK包，并进入TIME-WAIT状态。/* The socket is waiting after close to handle packets still in the network.等待足够的时间以确保远程TCP接收到连接中断请求的确认 */

10)、CLOSING: 比较少见./* Both sockets are shut down but we still don’t have all our data sent. 等待远程TCP对连接中断的确认 */

11)、CLOSED: 被动关闭端在接受到ACK包后，就进入了closed的状态。连接结束./* The socket is not being used. 没有任何连接状态 */

12)、UNKNOWN: 未知的Socket状态。/* The state of the socket is unknown. */

SYN: (同步序列编号,Synchronize Sequence Numbers)该标志仅在三次握手建立TCP连接时有效。表示一个新的TCP连接请求。
ACK: (确认编号,Acknowledgement Number)是对TCP请求的确认标志,同时提示对端系统已经成功接收所有数据。
FIN: (结束标志,FINish)用来结束一个TCP回话.但对应端口仍处于开放状态,准备接收后续数据。
```

# sort

```
功能说明：将文件进行排序，并将排序结果标准输出。
语　　法：sort [-bcdfimMnort+] [文件名]
参　　数：
-b：忽略每行前面开始出的空格字符；
-c：检查文件是否已经按照顺序排序；
-d：排序时，处理英文字母、数字及空格字符外，忽略其他的字符；
-f：排序时，将小写字母视为大写字母；
-i：排序时，除了040至176之间的ASCII字符外，忽略其他的字符；
-m：将几个排序号的文件进行合并；
-M：将前面3个字母依照月份的缩写进行排序；
-n：依照数值的大小排序；
-o<输出文件>：将排序后的结果存入制定的文件；
-r：以相反的顺序来排序；
-t<分隔字符>：指定排序时所用的栏位分隔字符；
+<起始栏位>-<结束栏位>：以指定的栏位来排序，范围由起始栏位到结束栏位的前一栏位。
```

# uniq

```
功能说明：将文件进行排序，并将排序结果标准输出。
语　　法：sort [-cdfsuw] [文件名]
参　　数：
-c或——count：在每列旁边显示该行重复出现的次数；
-d或--repeated：仅显示重复出现的行列；
-f<栏位>或--skip-fields=<栏位>：忽略比较指定的栏位；
-s<字符位置>或--skip-chars=<字符位置>：忽略比较指定的字符；
-u或——unique：仅显示出一次的行列；
-w<字符位置>或--check-chars=<字符位置>：指定要比较的字符。
```

# grep

```
功能说明：global search regular expression(RE) and print out the line，全面搜索正则表达式并把行打印出来）是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。
语　　法：grep [-abcEFGhHilLnqrsvVwxy][-A<显示列数>][-B<显示列数>][-C<显示列数>][-d<进行动作>][-e<范本样式>][-f<范本文件>][--help][范本样式][文件或目录...]
参　　数：
-a 不要忽略二进制数据。
-A<显示列数> 除了显示符合范本样式的那一行之外，并显示该行之后的内容。
-b 在显示符合范本样式的那一行之外，并显示该行之前的内容。
-c 计算符合范本样式的列数。
-C<显示列数>或-<显示列数>  除了显示符合范本样式的那一列之外，并显示该列之前后的内容。
-d<进行动作> 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep命令将回报信息并停止动作。
-e<范本样式> 指定字符串作为查找文件内容的范本样式。
-E 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-f<范本文件> 指定范本文件，其内容有一个或多个范本样式，让grep查找符合范本条件的文件内容，格式为每一列的范本样式。
-F 将范本样式视为固定字符串的列表。
-G 将范本样式视为普通的表示法来使用。
-h 在显示符合范本样式的那一列之前，不标示该列所属的文件名称。
-H 在显示符合范本样式的那一列之前，标示该列的文件名称。
-i 忽略字符大小写的差别。
-l 列出文件内容符合指定的范本样式的文件名称。
-L 列出文件内容不符合指定的范本样式的文件名称。
-n 在显示符合范本样式的那一列之前，标示出该列的编号。
-q 不显示任何信息。
-R/-r 此参数的效果和指定“-d recurse”参数相同。
-s 不显示错误信息。
-v 反转查找。
-w 只显示全字符合的列。
-x 只显示全列符合的列。
-y 此参数效果跟“-i”相同。
-o 只输出文件中匹配到的部分。
```

## 查看前 20 行

-   head -n 20

## 查看后 20 行

-   tail -n 20

## 查看系统并发数

-   netstat -nat|grep ESTABLISHED|wc -l

## 统计 80 端口链接数

-   netstat -nat | grep -i "80" | wc -l

## 查看当前进程数

-   ps aux | grep httpd | wc -l

## 并发请求数及其 TCP 连接状态

-   netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
    或
-   netstat -nat |awk '{print $6}'|sort|uniq -c|sort -rn

```
TIME_WAIT 8947 等待足够的时间以确保远程TCP接收到连接中断请求的确认
FIN_WAIT1 15 等待远程TCP连接中断请求，或先前的连接中断请求的确认
FIN_WAIT2 1 从远程TCP等待连接中断请求
ESTABLISHED 55 代表一个打开的连接
SYN_RECV 21 再收到和发送一个连接请求后等待对方对连接请求的确认
CLOSING 2 没有任何连接状态
LAST_ACK 4 等待原来的发向远程TCP的连接中断请求的确认
```

## 查找较多 time_wait 连接

-   netstat -n|grep TIME_WAIT|awk '{print $5}'|sort|uniq -c|sort -rn|head -n20

## 查较多的 SYN 连接

-   netstat -an | grep SYN | awk '{print $5}' | awk -F: '{print $1}' | sort | uniq -c | sort -nr | more

## 访问量 ip 前 20 统计

-   netstat -anlp|grep 80|grep tcp|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -nr|head -n20

# nginx 日志分析

## 列出访问前 10 位的 ip 地址

-   cat access.log|awk -F '|' '{print $3}'|sort|uniq -c|sort -nr|head -10

## 列出耗时最长 前一百 的请求

-   cat access.log |awk -F '|' '{print $5 "|" $1 "|" $4 }'|sort -nr|head -100

## 列出耗时超过 10 秒的请求以及对应请求发生的次数

-   cat access.log |awk -F '|' '($5 > 10){print $4}' |sort -n|uniq -c|sort -nr|head -100

## 统计网站流量（G)

-   cat access.log |awk '{sum+=$6} END {print sum/1024/1024/1024}'

## 统计 404 链接

-   awk -F '|' '($7 ~/404/)' access.log | awk -F '|' '{print $4,$7}' | sort | uniq -c

## 查询某个时间段的日志

-   grep -E '12/Jul/2018:15:[00-59]' access.log
