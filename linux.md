# Linux System Administrator/DevOps Interview Questions

## <a name='toc'> 目录 </a>

  1. [一般问题](#general)
  1. [简单](#simple)
  1. [中等](#medium)
  1. [困难](#hard)
  1. [专家](#expert)
  1. [网络](#network)

####[[⬆]](#toc) <a name='general'>一般问题</a>

* 你昨天/这个星期学了什么？
* 谈谈你的首选开发/管理环境。（操作系统，编辑器，浏览器，工具等）
* 告诉我你完成的最后一个主要的linux项目。
* 告诉我你在最近的一段时间内所犯的最大的错误，以及你今天会如何做不同的。从这次经历中你学到了什么？
* 为什么一定要选择你？
* dns在网络上发挥什么功能？
* 什么是http?
* 什么是HTTP代理和它是如何工作的？
* 简要描述HTTPS是如何工作的。
* 什么是SMTP？ 基本的情况下如何通过SMTP进行邮件传递。
* 什么是RAID？什么是RAID0，RAID1，RAID5，RAID10？
* 什么是0级备份？什么是增量备份？
* 描述Linux系统的通用文件系统的层次结构。


####[[⬆]](#toc) <a name='simple'>简单问题Questions:</a>

* 名称和管理员用户的UID是什么？。
* 如何列出目录中的所有文件，包括隐藏的文件？
* 删除目录及其内容的Unix / Linux的命令是什么？
* 哪一个命令将显示你的空闲/使用内存？在linux系统上是否存在自由内存？
* 在一个目录的文件中如何搜索字符串"my konfi is the best"？
* 如何连接到远程服务器或SSH是什么？
* 如何获得所有的环境变量，你如何使用它们？
* 当我运行ifconfig -a我得到"command not found"。什么可能是错误的？
* 如果我输入TAB-TAB，会发生什么？
* 什么命令将显示在Unix / Linux系统磁盘的可用空间？
* 你知道什么命令可以用来检查DNS记录？
* 什么Unix / Linux命令将更改文件的所有权，文件权限？
* chmod +x FILENAME是做什么的?
* 文件的0750的权限是什么意思？
* 目录上的权限0750意味着什么？
* 如何添加一个新的系统用户无需登录权限？
* 如何从用户添加/删除一个组？
* 别名是什么？
* 您如何设置根/用户的邮件地址？
* CTRL-c做什么的?
* 在/etc/services下是做什么的?
* 在bash如何重定向stdout和stderr? (> /dev/null 2>&1)
* Unix和Linux之间的区别是什么。
* Telnet和SSH区别?
* 解释三个负荷平均值，他们表明什么。
* 你能说出一个小写字母，是不是GNU ```ls```的一个有效的选项？


####[[⬆]](#toc) <a name='medium'>中等问题</a>

* 下面的命令做什么，你会如何使用它们？
 * ```tee```
 * ```awk```
 * ```tr```
 * ```cut```
 * ```tac```
 * ```curl```
 * ```wget```
 * ```watch```
 * ```head```
 * ```tail```
* 在```&``` 命令之后做了什么?
* 在```& disown```命令之后做了什么?
* 什么是一个数据包过滤器，它是如何工作的？
* 什么是虚拟内存？
* 什么是swap，它用来做什么?
* 一个记录是什么，NS记录，PTR记录、CNAME记录、MX记录？
* 还有其他的RRS，它们是用来做什么的？
* 什么是水平分割的DNS？
* 什么是粘滞位？
*在一个文件中不可变位做什么？
* 什么是硬链接和符号链接之间的区别？当您删除源符号链接/固网会发生什么？
* 什么是一个inode和哪些字段是存储在inode？
* 如何强制/触发下次重新启动文件系统检查？
* 什么是SNMP和它是干什么用的？
* 什么是一个运行级别和如何获取当前运行级别？
* 什么是SSH端口转发？
* 本地和远程端口转发之间的区别是什么？
* 不使用useradd/adduser向系统添加一个用户的步骤是什么？
* 什么是主要的和次要的特殊文件？
* 描述mknod命令当你使用它时.
* 描述一个场景，当你得到一个"filesystem is full" 的错误，但'df' 表明有自由空间。
* 描述了一个场景，删除文件时，但“DF”不显示被释放的空间。
* 描述'ps' 如何工作.
* 一个子进程死亡，没有父进程等待它的会发生什么，这有什么不好？
* 简要地解释每一个过程状态。
* 如何知道哪个进程监听一个特定的端口？
* 什么是僵尸进程，是什么原因？
* 你运行一个脚本，你想看到它的输出终端并保存在同一文件。你怎么能这样做呢？
* Explain what echo "1" > /proc/sys/net/ipv4/ip_forward does.
* 简要说明你需要采取以创造并安装网站https://foo.example.com有效证书的步骤。
* 你可以有几个HTTPS虚拟主机共享同一个IP？
* 什么是通配符证书？
* 你知道哪种linux文件类型？
* 一个过程和一个线程之间的区别是什么？父和子进程在一个分叉系统调用后？
* exec和fork区别?
* "nohup"用于干什么?
* 这两个命令之间的区别是什么？
  * ```myvar=hello```
  * ```export myvar=hello```
* 有多少NTP服务在您的本地ntp.conf配置？
* What does the column 'reach' mean in ```ntpq -p``` output?
* 你需要升级在100-1000服务器内核，你会如何做？
* 你怎么能得到主机，通道，ID，SCSI磁盘的LUN？
* 如何限制进程内存的使用？
* 什么是bash快速替代/符号代替（^ X ^ Y）？
* 你知道任何alternative shells？如果是的话，你用过吗？
* 一个tarpipe是什么（或者，你将如何去复制一切，包括硬链接和文件，从一个服务器到另一个）？

####[[⬆]](#toc) <a name='hard'>困难问题 Questions:</a>

* 什么是一个隧道，你可以绕过HTTP代理？
* IDS和IPS的区别是什么？
* 你定期使用哪些快捷键？
* 什么是Linux标准库？
* 什么是一个原子操作？
* 你刚配置的HTTP服务器没有在重新启动后运行，你会做什么？
* 〜/ .ssh/ authorized_keys文件是什么样的密钥，这个文件它是用来做什么的？
* 我已经添加了我的SSH公钥在authorized_keys，但我仍然得到一个密码提示，有什么错？
* 你有没有创建的RPM，DEB的或Solaris PKG？
* ```:(){ :|:& };:``` 在系统里有什么用?
* 你如何在一个脚本捕获一个linux信号？
* Can you catch a SIGKILL?
* 当Linux内核启动OOM杀手发生了什么，它是如何选择先杀死哪些进程？
* 从当系统启动和结束时，当你得到一个提示。尽可能详细描述了Linux启动过程。
* chroot监狱是什么？
* 当试图卸载目录它说它很忙，如何找出PID存放目录？
* 什么是ld_preload，什么时候使用它？
* 你运行一个批处理文件却什么都没有发生。你会如何调试呢？
* 什么是cgroup的？你可以指定一个你可以使用它们的场景？


####[[⬆]](#toc) <a name='expert'>专家问题</a>

* A running process gets ```EAGAIN: Resource temporarily unavailable``` on reading a socket. How can you close this bad socket/file descriptor without killing the process?


####[[⬆]](#toc) <a name='network'>网络问题</a>

* 什么是本地主机，为什么` ` ` ping localhost ` ` `失败？
* "ping" & "traceroute" 相似性? 如何跟踪路由能够找到跳。
* 什么是用于显示计算机上的所有开放端口和/或套接字连接的命令？
* 300.168.0.123 是一个有效的IPv4 地址吗?
* Which IP ranges/subnets are "private" or "non-routable" (RFC 1918)?
* VLAN是什么？
* 什么是ARP他用在什么地方。
* TCP 和 UDP的区别?
* 一个默认网关的目的是什么？
* 用什么命令显示在Linux机器上的路由表？
* 网络上的TCP连接可以通过4事情来唯一定义。什么是那些东西？
* 当运行Web浏览器客户端连接到Web服务器，什么是源端口，什么是连接的目的端口？
* 你如何添加一个IPv6地址的特定接口？
* 你已经添加了一个IPv4和IPv6地址的接口eth0。一个ping到v4地址工作但pingV6地址给你```sendmsg: operation not permitted```。什么可能是错的？
* 什么是SNAT，什么时候用？
* 解释你如何登录到Linux系统，把新传入的数据包使用SSH隧道。
* 你如何阻止DDoS攻击？
* 你怎么能看到一个IP数据包的内容？
* 什么是IPoAC（RFC1149）？
