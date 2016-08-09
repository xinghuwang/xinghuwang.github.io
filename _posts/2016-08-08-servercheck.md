### 应该弄清的问题

+ 事故的发生时间,持续时间
+ 事故的表现形式，报错，响应慢，是否有概率出现, 是所有服务全挂了还是仅部分服务有问题
+ 事故的影响范围是所有用户还是部分用户
+ 事故是否可稳定重现，是否有规律
+ 最后几次上线内容
+ 是否有监控平台可以查看
+ 是否有日志可以查看

### 查看cpu内存负载

+ free -m

>       free1 + buffers1 + cached1 = free2
>       used1 = buffers1 + cached1 + used2
>       实际看第二行的free
>       share 共享内存

+ uptime

>       负载1min, 5min, 15min
>       系统运行了多长时间

+ top

>       F:选择排序的列
>       F:选择排序的列
>       R:倒排
>       <>:翻页
>       f:选择显示的列
>       o:改变显示的顺序

### 查看系统报错信息
+ dmesg
    - 查看系统日志，老版本不显示时间？
+ less /var/log/messages
+ less /var/log/secure
+ less /var/log/auth
+ sar
    - sar -n TCP,ETCP 1
        - 查看tcp连接状态
        - active/s:每秒本地发起的tcp连接数,即通过connect调用创建的tcp连接
        - passive/s:每秒远程发起的tcp连接数,即通过accept调用创建的tcp连接
        - retrans/s:每秒tcp重传数量
    - sar -n DEV 1
        - 查看网络设备的吞吐率
    - sar -d 10 3: 10s一次，采样3次，统计设备使用信息

>        -b：报告I/O使用情况以及传输速率。（只适用于2.5及之前的内核，所以新内核有可能不支持这个选项）
>        -B：报告“页”使用情况
>        -c：报告进程创建情况
>        -d：报告每一个块设备的使用情况
>        （当你使用时，你会发现在DEV列有类似dev1-7格式的字符串，
>        其中1代表设备的主序号，n代表设备的从序号，
>        而且rd_sec/s列和wr_sec/s列的单位都是512bytes，也就是512B，也就是0.5KB）
>        -I：汇报中断情况
>        -n：汇报网络情况TCP ETCP DEV EDEV SOCK FULL
>        -P：设定CPU
>        -q：汇报队列长度和负载信息
>        -r：汇报内存和交换区使用情况
>        -R：汇报内存情况
>        -u：汇报CPU使用情况
>        -v：汇报i节点、文件和其他内核表信息
>        -w：汇报系统上下文切换情况
>        -x：可以针对某个特定PID给出统计信息，
>        可以直接指定进程ID号；
>        也可以指定为SELF，这样就是检测sar进程本身；
>        如果设定为ALL，则表示汇报所有系统进程信息。
>        -X：汇报特定PID的子进程的信息
>        -y：设定TTY设备的信息。
>
>       怀疑CPU存在瓶颈，可用 sar -u 和 sar -q 等来查看
>       怀疑内存存在瓶颈，可用 sar -B、sar -r 和 sar -W 等来查看
>       怀疑I/O存在瓶颈，可用 sar -b、sar -u 和 sar -d 等来查看

### 查看运行的进程

+ pstree -a
+ ps aux
+ lsof

>       lsof  filename 显示打开指定文件的所有进程
>       lsof -a 表示两个参数都必须满足时才显示结果
>       lsof -c string   显示COMMAND列中包含指定字符的进程所有打开的文件
>       lsof -u username  显示所属user进程打开的文件
>       lsof -g gid 显示归属gid的进程情况
>       lsof +d /DIR/ 显示目录下被进程打开的文件
>       lsof +D /DIR/ 同上，但是会搜索目录下的所有目录，时间相对较长
>       lsof -d FD 显示指定文件描述符的进程
>       lsof -n 不将IP转换为hostname，缺省是不加上-n参数
>       lsof -i 用以显示符合条件的进程情况
>       lsof -i[46] [protocol][@hostname|hostaddr][:service|port]
>                46 --> IPv4 or IPv6
>                protocol --> TCP or UDP
>                hostname --> Internet host name
>                hostaddr --> IPv4地址
>                service --> /etc/service中的 service name (可以不只一个)
>                port --> 端口号 (可以不只一个)

### 监听的网络服务

+ netstat -nltp
+ netstat -nutp
+ netstat -nxtp

>       -a (all)显示所有选项，默认不显示LISTEN相关
>       -t (tcp)仅显示tcp相关选项
>       -u (udp)仅显示udp相关选项
>       -n 拒绝显示别名，能显示数字的全部转化成数字。
>       -l 仅列出有在 Listen (监听) 的服務状态
>       -i 列出网络接口列表
>       -p 显示建立相关链接的程序名
>       -r 显示路由信息，路由表
>       -e 显示扩展信息，例如uid等
>       -s 按各个协议进行统计
>       -c 每隔一个固定时间，执行该netstat命令。
>
>       提示：LISTEN和LISTENING的状态只有用-a或者-l才能看到

+ ss -s

>       -n, 不解析服务名称
>       -r, 解析主机名
>       -a, 显示所有套接字（sockets）
>       -l, 显示监听状态的套接字（sockets）
>       -o, 显示计时器信息
>       -e, 显示详细的套接字（sockets）信息
>       -m, 显示套接字（socket）的内存使用情况
>       -p, 显示使用套接字（socket）的进程
>       -i, 显示 TCP内部信息
>       -s, 显示套接字（socket）使用概况
>       -4, 仅显示IPv4的套接字（sockets）
>       -6, 仅显示IPv6的套接字（sockets）
>       -0, 显示 PACKET 套接字（socket）
>       -t, 仅显示 TCP套接字（sockets）
>       -u, 仅显示 UCP套接字（sockets）
>       -d, 仅显示 DCCP套接字（sockets）
>       -w, 仅显示 RAW套接字（sockets）
>       -x, 仅显示 Unix套接字（sockets）
>       -f, -f=FAMILY  显示 FAMILY类型的套接字（sockets），FAMILY可选，支持  unix, inet, inet6, link, netlink
>       -A, QUERY := {all|inet|tcp|udp|raw|unix|packet|netlink}[,QUERY]
>       -D, -D=FILE     将原始TCP套接字（sockets）信息转储到文件
>       -F, -F=FILE   从文件中都去过滤器信息
>              FILTER := [ state TCP-STATE ] [ EXPRESSION ]

+ tcpdump

>       tcpdump [ -adeflnNOpqStvx ] [ -c 数量 ] [ -F 文件名 ]
>       [ -i 网络接口 ] [ -r 文件名] [ -s snaplen ]
>       [ -T 类型 ] [ -w 文件名 ] [表达式 ]
>       1. tcpdump的选项介绍
>       -a 　将网络地址和广播地址转变成名字；
>       -d 　将匹配信息包的代码以人们能够理解的汇编格式给出；
>       -dd 　将匹配信息包的代码以c语言程序段的格式给出；
>       -ddd 　将匹配信息包的代码以十进制的形式给出；
>       -e 　在输出行打印出数据链路层的头部信息；
>       -f 　将外部的Internet地址以数字的形式打印出来；
>       -l 　使标准输出变为缓冲行形式；
>       -n 　不把网络地址转换成名字；
>       -t 　在输出的每一行不打印时间戳；
>       -v 　输出一个稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息；
>       -vv 　输出详细的报文信息；
>       -c 　在收到指定的包的数目后，tcpdump就会停止；
>       -F 　从指定的文件中读取表达式,忽略其它的表达式；
>       -i 　指定监听的网络接口；
>       -r 　从指定的文件中读取包(这些包一般通过-w选项产生)；
>       -w 　直接将包写入文件中，并不分析和打印出来；
>       -T 　将监听到的包直接解释为指定的类型的报文，常见的类型有rpc 

+ wireshark
+ watch

### 网络连接
+ traceroute
+ nslookup

### io性能
+ iostat -kx 2

>        -c 仅显示CPU统计信息.与-d选项互斥.
>        -d 仅显示磁盘统计信息.与-c选项互斥.
>        -k 以K为单位显示每秒的磁盘请求数,默认单位块.
>        -p device | ALL
>         与-x选项互斥,用于显示块设备及系统分区的统计信息.也可以在-p后指定一个设备名,如:
>         # iostat -p hda
>         或显示所有设备
>         # iostat -p ALL
>        -t    在输出数据时,打印搜集数据的时间.
>        -V    打印版本号和帮助信息.
>        -x    输出扩展信息

+ vmstat 2 10

>       -a：显示活跃和非活跃内存
>       -f：显示从系统启动至今的fork数量 。
>       -m：显示slabinfo
>       -n：只在开始时显示一次各字段名称。
>       -s：显示内存相关统计信息及多种系统活动数量。
>       delay：刷新时间间隔。如果不指定，只显示一条结果。
>       count：刷新次数。如果不指定刷新次数，但指定了刷新时间间隔，这时刷新次数为无穷。
>       -d：显示磁盘相关统计信息。
>       -p：显示指定磁盘分区统计信息
>       -S：使用指定单位显示。参数有 k 、K 、m 、M ，分别代表1000、1024、1000000、1048576字节（byte）。默认单位为K（1024 bytes）
>       -V：显示vmstat版本信息。

+ mpstat 2 10
    - mpstat -P ALL 5 2  显示所有cpu统计信息
+ pidstat
    - pidstat -r -p 123 1 显示进程占用系统资源的情况
+ dstat --top-io --top-bio

>       -l ：显示负载统计量
>       -m ：显示内存使用率（包括used，buffer，cache，free值）
>       -r ：显示I/O统计
>       -s ：显示交换分区使用情况
>       -t ：将当前时间显示在第一行
>       –fs ：显示文件系统统计数据（包括文件总数量和inodes值）
>       –nocolor ：不显示颜色（有时候有用）
>       –socket ：显示网络统计数据
>       –tcp ：显示常用的TCP统计
>       –udp ：显示监听的UDP接口及其当前用量的一些动态数据
>       当然不止这些用法，dstat附带了一些插件很大程度地扩展了它的功能。你可以通过查看/usr/share/dstat目录来查看它们的一些使用方法，常用的有这些：
>
>       -–disk-util ：显示某一时间磁盘的忙碌状况
>       -–freespace ：显示当前磁盘空间使用率
>       -–proc-count ：显示正在运行的程序数量
>       -–top-bio ：指出块I/O最大的进程
>       -–top-cpu ：图形化显示CPU占用最大的进程
>       -–top-io ：显示正常I/O最大的进程
>       -–top-mem ：显示占用最多内存的进程

### 硬件
+ lspci
+ dmidecode
+ ethtool
+ df -h

### 定时任务
+ ls /etc/cron* + cat

### 应用系统日志
+ nginx
    - error_log
    - access_log
+ php-fpm
    - error_log
    - slow.log
+ mysql
    - show processlist
    _ mysql.log
+ app.log

### 进程调试
+ gdb
+ cat /proc/
+ strace

>       -c 统计每一系统调用的所执行的时间,次数和出错的次数等.
>       -d 输出strace关于标准错误的调试信息.
>       -f 跟踪由fork调用所产生的子进程.
>       -ff 如果提供-o filename,则所有进程的跟踪结果输出到相应的filename.pid中,pid是各进程的进程号.
>       -F 尝试跟踪vfork调用.在-f时,vfork不被跟踪.
>       -h 输出简要的帮助信息.
>       -i 输出系统调用的入口指针.
>       -q 禁止输出关于脱离的消息.
>       -r 打印出相对时间关于,,每一个系统调用.
>       -t 在输出中的每一行前加上时间信息.
>       -tt 在输出中的每一行前加上时间信息,微秒级.
>       -ttt 微秒级输出,以秒了表示时间.
>       -T 显示每一调用所耗的时间.
>       -v 输出所有的系统调用.一些调用关于环境变量,状态,输入输出等调用由于使用频繁,默认不输出.
>       -V 输出strace的版本信息.
>       -x 以十六进制形式输出非标准字符串
>       -xx 所有字符串以十六进制形式输出.
>       -a column
>       设置返回值的输出位置.默认 为40.
>       -e expr
>       指定一个表达式,用来控制如何跟踪.格式如下:
>       [qualifier=][!]value1[,value2]...
>       qualifier只能是 trace,abbrev,verbose,raw,signal,read,write其中之一.value是用来限定的符号或数字.默认的 qualifier是 trace.感叹号是否定符号.例如:
>       -eopen等价于 -e trace=open,表示只跟踪open调用.而-etrace!=open表示跟踪除了open以外的其他调用.有两个特殊的符号 all 和 none.
>       注意有些shell使用!来执行历史记录里的命令,所以要使用\\.
>       -e trace=set
>       只跟踪指定的系统 调用.例如:-e trace=open,close,rean,write表示只跟踪这四个系统调用.默认的为set=all.
>       -e trace=file
>       只跟踪有关文件操作的系统调用.
>       -e trace=process
>       只跟踪有关进程控制的系统调用.
>       -e trace=network
>       跟踪与网络有关的所有系统调用.
>       -e strace=signal
>       跟踪所有与系统信号有关的 系统调用
>       -e trace=ipc
>       跟踪所有与进程通讯有关的系统调用
>       -e abbrev=set
>       设定 strace输出的系统调用的结果集.-v 等与 abbrev=none.默认为abbrev=all.
>       -e raw=set
>       将指 定的系统调用的参数以十六进制显示.
>       -e signal=set
>       指定跟踪的系统信号.默认为all.如 signal=!SIGIO(或者signal=!io),表示不跟踪SIGIO信号.
>       -e read=set
>       输出从指定文件中读出 的数据.例如:
>       -e read=3,5
>       -e write=set
>       输出写入到指定文件中的数据.
>       -o filename
>       将strace的输出写入文件filename
>       -p pid
>       跟踪指定的进程pid.
>       -s strsize
>       指定输出的字符串的最大长度.默认为32.文件名一直全部输出.
>       -u username
>       以username 的UID和GID执行被跟踪的命令

+ ltrace
+ dtruss

