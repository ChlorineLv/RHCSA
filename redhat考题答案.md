# RHCSA考试备注

(Redhat Certified System Administrator)

考试机fundation，有两个下挂虚拟机node1，node2。

&#x1F4CC;烤前检查node1和node2是否能打开console

每台机子考题的IP主机名、存储库等都不同，不要登录别人的IP

考场或提供纸笔，node1的root密码。

容器镜像的信息在“注册服务器”信息里，需自行查看和记录。

## &#x2757;&#x2755;&#x2757;考题注意

### &#x1F514;注意区分node1和node2的题目

### &#x26a1; node1 网络配置题

### &#x26a1; node1 注意yum仓库题

### &#x26a1; node1 注意创建用户和组题

### &#x26a1; node2 破解密码

### &#x26a1; node2 分区文件修改

### &#x26a1; node2 VDO挂载的延迟挂载选项

## 重要配置信息

在考试期间，除了您就坐位置的台式机之外，还将使用多个虚拟系统。您不具有台式机系统的根访问权，但具有对虚拟系统的完全根访问权。

### 系统信息

(实际考试未必有，需要自行上node1查看)
系统IP地址
node1.domain250.example.com 172.25.250.100
node2.domain250.example.com 172.25.250.200

您使用的系统属于 DNS 域 domain250.example.com。该域中的所有系统都位于 172.25.250.0/255.255.255.0 子网中，该子网中的所有系统都位于 domain250.example.com 中。

针对这些系统列出的 IP 地址是应该分配给系统的地址。您可能需要为一个或两个系统配置网络，以便能够通过上述地址访问您的地址。

### 帐户信息

node1 的根密码已经设置为 flectrag 。

除非另有指定，否则这将是用于访问其他系统和服务的密码。此外，除非另有指定，否则应将该密码用于您创建的什么问题帐户或者需要设置密码的任意服务。
其他信息

您可以通过 SSH 或控制台访问考试系统（参见下文所述）。请注意，SSH 访问权可能取决于您解答其他考试项目的情况。

如果您需要在系统上安装其他软件，可以使用位于以下地址的存储库：

    http://content/dvd/BaseOS
    http://content/dvd/AppStream

### 注册服务器信息

注册服务器地址 registry.domain250.example.com

使用 admin 作为用户名，使用 redhat321 作为映像注册表的凭据

### 重要评测信息

您的系统会在重新引导后进行评测，因此务必确保您实施的的所有配置和服务在重新引导后仍然保留。服务必须在没有人工干预的情况下启动。

同样，本次考试使用的所有虚拟实例都必须能够重新引导至适当的多用户目标，而无需任何人工辅助。在无法引导或无法进行无人干预引导的系统上完成的所有操作都将为零分。

## 考试要求

### *在您的系统上执行以下所有步骤*

## 1、将 node1 配置为具有以下网络配置

> 主机名：node1.domain250.example.com
>
> IP 地址：172.25.250.100
>
> 子网掩码：255.255.255.0
>
> 网关：172.25.250.254
>
> DNS服务器：172.25.250.254

&#x26a1;改hostname的语法为hostnamectl

```bash

    // 改主机名
    [root@node1 ~]# hostnamectl set-hostname node1.domain250.example.com
    
    IP解法一：
    // 图形化配IP，子网掩码用ip/来写
    [root@node1 ~]# nmtui
    
    IP解法二：
    // 命令行配IP
    [root@node1 ~]# nmcli connection modify "Wired connection 1" ipv4.addresses 172.25.250.100/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.254.250 ipv4.method manual connection.autoconnect yes
    [root@node1 ~]# nmcli connection up "Wired connection 1"


    // 考试无需，仅用于检查
    // 检查 IP
    [root@node1 ~]# ip addr             
    
    // 检查 DNS    
    [root@node1 ~]# cat /etc/resolv.conf
    
    // 检查网关
    [root@node1 ~]# ip route                
    
    // 检查主机名
    [root@node1 ~]# cat /etc/hostname

    // 检查是否ping通
    [root@node1 ~]# ping node1.domain250.example.com
    [root@node1 ~]# ping registry.domain250.example.com

```

## 2、完成配置您的系统以使用默认存储库

> YUM 存储库已可以从 <http://foundation0.ilt.example.com/dvd/BaseOS> 和 <http://foundation0.ilt.example.com/dvd/AppStream> 使用配置您的系统，以将这些位置用作默认存储库

```bash

    // name可以随便写，gpgcheck必须0，enabled记得带d
    [root@node1 ~]# vim /etc/yum.repos.d/base.repo
    > [base]
    > name=base
    > baseulr='...'
    > enabled=1
    > gpgcheck=0
    > 
    > [app]
    > name=app
    > baseurl='...'
    > enabled=1
    > gpgcheck=0

    // 考试无需，仅用于查看是否成功
    [root@node1 ~]# yum -y install vim
    [root@node1 ~]# rpm -qa | grep xxxx
    [root@node1 ~]# yum provides xxxx
```

## 3、完成调试 SELinux

> 非标准端口 82 上运行的 Web 服务器在提供内容时遇到问题。根据需要调试并解决问题，使其满足以下条件：
>
> 1、系统上的 Web 服务器能够提供 /var/www/html 中所有现有的 HTML 文件（注：不要删除或以其他方式改动现有的文件内容）
>
> 2、Web 服务器在端口 82 上提供此内容
>
> 3、Web 服务器在系统启动时自动启动

&#x26a1;记得systemctl启动有个d（httpd）

```bash

    // 测试启动不了
    [root@node1 ~]# systemctl start httpd

    // 查看log为啥蹦了
    [root@node1 ~]# tail /var/log/messages 

    // 查看已有端口
    [root@node1 ~]# man semanage port | grep \#

    // 给服务开个82的端口
    [root@node1 ~]# semanage port -a -t http_port_t -p tcp 82

    // 开机自启服务
    [root@node1 ~]# systemctl enable --now httpd

    // -Z 查看哪个标签没有
    [root@node1 ~]# ll -Z /var/www/html

    // 改标签以便文件可以使用
    [root@node1 ~]# chcon -t httpd_sys_content_t /var/www/html/file1

    // 永久通过端口
    [root@node1 ~]# firewall-cmd --permanent --add-port=82/tcp 

    // 刷新一下
    [root@node1 ~]# firewall-cmd --reload

```

## 4、完成创建用户帐户

> 创建下列用户、组和组成员资格：
>
> 1、名为 sysmgrs 的组
>
> 2、用户 natasha ，作为次要组从属于 sysmgrs
>
> 3、用户 harry ，作为次要组还从属于 sysmgrs
>
> 4、用户 sarah ，无权访问系统上的交互式 shell 且不是 sysmgrs 的成员
>
> 5、natasha、harry 和 sarah 的密码应当都是 flectrag

```bash

    [root@node1 ~]# groupadd sysmgrs
    
    // -G 表示附属组，-g 表示gid主组
    [root@node1 ~]# useradd -G sysmgrs natasha
    [root@node1 ~]# useradd -G sysmgrs harry

    // -s /bin/false 不允许使用交互式bash
    [root@node1 ~]# useradd sarah -s /bin/false
    [root@node1 ~]# echo flectrag | passwd --stdin natasha
    [root@node1 ~]# echo flectrag | passwd --stdin harry
    [root@node1 ~]# echo flectrag | passwd --stdin sarah

```

## 5、完成配置 cron 作业

> 配置 cron 作业，该作业每隔 2 分钟运行并执行以下命令：
>
> 1、logger "EX200 in progress"，以用户 natasha 身份运行

```bash

    // # crond -e 编辑当前用户 
    // # crond -l 查看当前用户
    // # crond -u 以某用户身份来控制
    // 5个*：分 时 日 月 星期，斜杠表示每x
    [root@node1 ~]# crontab -e -u natash
    > */2 * * * * logger "EX200 in progress"

    // 查看情况
    [root@node1 ~]# systemctl -l -u natasha
    
    // 设置开机自启
    [root@node1 ~]# systemctl enable --now crond

```

## 6、完成创建协作目录

> 创建具有以下特征的协作目录 /home/managers ：
>
> 1、/home/managers 的组用权是 sysmgrs
>
> 2、目录应当可被 sysmgrs 的成员读取、写入和访问，但任何其他用户不具这些权限。（当然，root 用户有权访问系统上的所有文件和目录）
>
> 3、/home/managers 中创建的文件自动将组所有权设置到 sysmgrs 组

```bash

    [root@node1 ~]# mkdir /home/managers
    
    // 亦可以改为 root:sysmgrs 表示 用户：组
    [root@node1 ~]# chown :sysmgrs /home/managers
    
    // 自动继承：2，其他不具备权限最后为0
    [root@node1 ~]# chmod 2770 /home/managers/

```

## 7、完成配置 NTP

> 配置您的系统，使其成为 materials.example.com 的 NTP 客户端。（注：materials.example.com 是 classroom.example.com 的 DNS 别名）

&#x26a1;记得启动服务有个d（chronyd）

```bash

    // 将其他server注释掉
    [root@node1 ~]# yum install chrony
    [root@node1 ~]# vim /etc/chrony.conf
    > # server_gate iburst
    > server materials.example.com iburst

    // 开机自启
    [root@node1 ~]# systemctl enable --now chronyd

    // 测试，显示NTP service:active即可
    [root@node1 ~]# timedatectl

```

## 8、完成配置 autofs

> 配置 autofs ，以按照如下所述自动挂载远程用户的主目录：
>
> 1、materials.example.com ( 172.25.254.254 ) NFS 导出 /rhome 到您的系统。此文件系统包含为用户 remoteuser1 预配置的主目录
>
> 2、remoteuser1 的主目录是 materials.example.com:/rhome/remoteuser1
>
> 3、remoteuser1 的主目录应自动挂载到本地 /rhome 下的 /rhome/remoteuser1
>
> 4、主目录必须可供其用户写入
>
> 5、remoteuser1 的密码是 flectrag

```bash

    // 查看服务器有无共享目录（可做可不做）
    [root@node1 ~]# showmount -e materials.example.com
    
    // 安装autofs
    [root@node1 ~]# yum -y install autofs
    
    // 配置文件里加一行，定义挂载目录/rhome，挂载动作看/etc/auto.misc文件
    [root@node1 ~]# vi /etc/auto.master
    > /rhome /etc/auto.misc
    
    // 将远程目录挂载到本地目录（本地目录，权限，远程目录）
    [root@node1 ~]# vi /etc/auto.misc
    > remoteuser1 -fstype=nfs,rw materials.example.com:/rhome/remoteuser1
    
    // 重启服务，设置开机自启
    [root@node1 ~]# systemctl start autofs
    [root@node1 ~]# systemctl enable autofs

```

## 9、完成配置 /var/tmp/fstab 权限

>将文件 /etc/fstab 复制到 /var/tmp/fstab 。配置 /var/tmp/fstab 的权限以满足如下条件：
>
>1、文件 /var/tmp/fstab 自 root 用户所有
>
>2、文件 /var/tmp/fstab 属于组 root
>
>3、文件 /var/tmp/fstab 应不能被任何人执行
>
>4、用户 natasha 能够读取和写入 /var/tmp/fstab
>
>5、用户 harry 无法写入或读取 /var/tmp/fstab
>
>6、所有其他用户（当前或未来）能够读取 /var/tmp/fstab

```bash

    [root@node1 ~]# cp /etc/fstab /var/tmp/fstab
    
    // 查看复制后属性
    [root@node1 ~]# ll -d /var/tmp/fstab

    // 修改owner和组
    [root@node1 ~]# chown root:root /var/tmp/fstab 
    
    // 修改读写权限
    [root@node1 ~]# chmod a-x /var/tmp/fstab

    // 给natasha读写权限
    [root@node1 ~]# setfacl -m u:natasha:rw /var/tmp/fstab

    // 将harry权限关了
    [root@node1 ~]# setfacl -m u:harry:- /var/tmp/fstab

```

## 10、完成配置用户帐户

>配置用户 manalo ，其用户 ID 为 3533。此用户的密码应当为 flectrag。

```bash

    [root@node1 ~]# useradd -u 3533 manalo
    [root@node1 ~]# echo flectrag | passwd --stdin manalo
```

## 11、完成查找文件

> 查找当 jacques 所有的所有文件并将其副本放入 /root/findfiles 目录

* 利用find  -[选项]  查找条件  目标文件——查
* 利用  会将root结尾的结果覆写进/root/a.txt里（如无新建有则覆写）
* -exec cp -a {} /root/findfiles/ 会将前面的结果传进 { } 里执行接下来的语句
* &#x26a1;记得 cp 到一个目录（带/结尾）
* &#x26a1;记得结尾加 \ ; 号

```bash

    [root@node1 ~]# mkdir /root/findfiles
    
    // 通过-exec将文件传到cp命令里的{}
    [root@node1 ~]# find / -user jacques -exec cp -a {} /root/findfiles/ \;

```

## 12、完成查找字符串

> 查找文件 /usr/share/xml/iso-codes/iso_639_3.xml 中包含字符串 ng 的所有行。将所有这些行的副本按原始顺序放在文件 /root/list 中。 /root/list 不得包含空行，且所有行必须是 /usr/share/xml/iso-codes/iso_639_3.xml 中原始行的确切副本。

* 利用grep  [选项]  查找条件  目标文件——查grep root /etc/passwd  查root有关（^root查开头，root$查结尾）
* 利用grep root$ /etc/passwd > /root/a.txt  会将root结尾的结果覆写进/root/a.txt里（如无新建有则覆写）
* &#x26a1;记得加 > 号
* 选中字符，鼠标中键即可粘帖所选字符

```bash

    // 记得加 > 
    [root@node1 ~]# grep ng /usr/share/xml/iso-codes/iso_639_3.xml > /root/list

```

## 13、完成创建存档

> 创建一个名为 /root/backup.tar.gz 的 tar 存档，其应包含 /usr/local 的 tar 存档，其应包含 /usr/local 的内容。该 tar 存档必须使用 gzip 进行压缩。

* c 创建tar格式打包的文件
* x 解开.tar格式的包
* f 使用tar文件而不是设备
* 调用压缩或解压（j 调用.bz2，z 调用.gz，J 调用.xz）
* v 显示详细信息

```bash

    // tar [option] [目标包] [打包目录]
    [root@node1 ~]# tar cfvz /root/backup.tar.gz /usr/local

```

## 14、完成配置容器使其自动启动

> 利用注册服务器上的 rsyslog 镜像，创建一个名为  logserver 的容器
>
> 1、面向 wallah 用户，配置一个 systemd 服务
>
> 2、该服务命名为 container-logserver ，并在系统重启时自动启动，无需干预

## 15、完成为容器配置持久存储

> 通过以下方式扩展上一个任务的服务
>
> 1、配置主机系统的 journald 日志以在系统重启后保留数据，并重新启动日志记录服务
>
> 2、将主机 /var/log/journal目录下任何以 *.journal 的文件复制到 /home/wallah/container_logfile 中
>
> 3、将服务配置为在启动时自动将/home/wallah/container_logfile 挂载到容器中的 /var/log/journal 下

```bash

    [root@node1 ~]# ll -d /run/log/journal/
    [root@node1 ~]# mkdir /var/log/journal

    // 设置权限允许读写
    [root@node1 ~]# chown root:systemd-journal /var/log/journal/
    [root@node1 ~]# chmod 2775 /var/log/journal

    // 设置journal为persistent，以设置永久保存
    [root@node1 ~]# vim /etc/systemd/journald.conf
    > [journal]
    > #Storage=auto
    > Storage=persistent
    
    // 重启服务
    [root@node1 ~]# systemd enable --now systemd-journald
    [root@node1 ~]# ls /var/log/journal/
    [root@node1 ~]# cp /var/log/journal/xxxxxx/system.journal /home/wallah/container_journal

    // -R 将该文件夹以及子文件
    [root@node1 ~]# chown -R wallah:wallah /home/wallah/container_logfile

    // 登录 wallah 建容器，不能用 su
    [root@node1 ~]# ssh wallah@node1 
    
    // 登录到注册服器（镜像仓库）
    [wallah@node1 ~]$ podman login registry.domain250.example.com
    # Username: admin
    # Password: redhat321
    # Login Succeeded!
    
    // 查看仓库里镜像
    [wallah@node1 ~]$ podman search registry.domain250.example.com/
    # INDEX NAME DESCRIPTION    STARS OFFICIAL AUTOMATED
    # example.com registry.domain250.example.com/rhel8/mariadb-103 0
    # example.com registry.domain250.example.com/rhel8/httpd-24 0
    # example.com registry.domain250.example.com/library/nginx 0
    # example.com registry.domain250.example.com/ubi7/ubi 0
    # example.com registry.domain250.example.com/ubi8/ubi 0
    # example.com registry.domain250.example.com/rhel8/rsyslog
    
    // 拉镜像
    [wallah@node1 ~]$ podman pull registry.domain250.example.com/rhel8/rsyslog
    
    // 运行容器，podman run: 启动一个容器 -d 后台运行 --name logserver : 容器的名字 -v /home/wallah/container_logfile:/var/log/journal:Z 目录映射
    [wallah@node1 ~]$ podman run -d --name logserver -v /home/wallah/container_logfile:/var/log/journal:Z registry.domain250.example.com/rhel8/rsyslog
    
    // 查看容器
    [wallah@node1 ~]$ podman ps 
    # CONTAINER ID IMAGE COMMAND    CREATED STATUS PORTS NAMES
    # 0bcd0f40c809 registry.domain250.example.com/rhel8/rsyslog:latest
    # /bin/rsyslog.sh 5 seconds ago Up 4 seconds ago
    
    2) 为容器创建一个用户服务
    // 容器随系统启动
    [wallah@node1 ~]$ loginctl enable-linger

    // 查看（估计可以不写）
    [wallah@node1 ~]$ loginctl show-user wallah 
    
    // 创建用户服务 unit
    [wallah@node1 ~]$ mkdir -p ~/.config/systemd/user/
    [wallah@node1 ~]$ cd .config/systemd/user/
    
    // 生成服务管理文件，container-logserver.service 就是你的服务名。
    [wallah@node1 user]$ podman generate systemd -n logserver -f /home/wallah/.config/systemd/user/container-logserver.service

    // 停止运行容器，测试服务管理容器
    [wallah@node1 user]$ podman stop logserver
    
    // 启动用户服务，并设置开机自启动
    [wallah@node1 user]$ systemctl --user enable --now container-logserver.service

```

---

### *node2任务*

在 node2.domain250.example.com 上执行以下任务

## 1、完成设置 root 密码

> 将 node2 的 root 密码设置为 flectrag 。您需要获得系统访问权限才能进行此操作。

```bash

    1、在console里send key，选ctrl+shift+del

    2、待出现启动项时按e
    
    3、定位到Linux开头那行末（约第三行），输入rd.break console=tty0，并ctrl+x重启
    
    4、出现命令行：
    // 重新读写挂载真实系统
    Swith_root:/# mount -o remount,rw /sysroot
    
    // 切换到真实系统
    Swith_root:/# chroot /sysroot
    
    // 改root密码
    sh-4.4# echo flectrag | passwd --stdin root
    
    // SELinux初始化，重新打标签，让改密码生效（注意label的单词拼写）
    sh-4.4# touch /.autorelabel
    sh-4.4# exit
    Swith_root:/# exit
```

## 2、完成配置您的系统以使用默认存储库

> YUM 存储库已可以从 <http://foundation0.ilt.example.com/dvd/BaseOS> 和 <http://foundation0.ilt.example.com/dvd/AppStream> 使用配置您的系统，以将这些位置用作默认存储库

```bash

    [root@node2 ~]# yum install vim
    [root@node2 ~]# vi /etc/yum.repos.d/base.repo
    > [base]
    > name=base
    > baseurl='.......'
    > gpgcheck=0
    > enabled=1
    > 
    > [app]
    > name=app
    > baseurl='......'
    > gpgcheck=0
    > enable=1
```

## 3、完成调整逻辑卷大小

> 将逻辑卷 vo 及其文件系统的大小调整到 230 MiB。确保文件系统内容保持不变。注：分区大小很少与请求的大小完全相同，因此可以接受范围为 217 MiB 到 243 MiB 的大小。

```bash

    // 查看vo的全名
    [root@node2 ~]# df -hT

    // 查看卷后是否有空余
    [root@node2 ~]# vgs

    // 扩容（L表示扩容至）
    [root@node2 ~]# lvextend -rL 230M /dev/mapper/myvol-vo

    // 复查大小
    [root@node2 ~]# df -ht
```

## 4、完成添加交换分区

> 向您的系统添加一个额外的交换分区 756MiB 。交换分区应在系统启动时自动挂载。不要删除或以任何方式改动系统上的任何现有交换分区。

```bash

    // 查看磁盘
    [root@node2 ~]# lsblk
    [root@node2 ~]# fdisk /dev/vdb

    // 由于第一次建，则需要选择为extend分区把剩余空间改为扩展分区（方便后续分区）
    > Command (m for help): n
    > Select (default p): e

    // 重新建逻辑分区则不需要选择类型
    > Command (m for help): n
    > First sector :
    > Last sector, +sectors or +size{K,M,G,T,P} : +756M
    > Command (m for help): p
    > Command (m for help): w
    
    // 可能需要重启，记住实际卷名
    [root@node2 ~]# mkswap /dev/vdb5

    // 开启虚拟分区
    [root@node2 ~]# swapon /dev/vdb5

    // 修改文件，记得看编辑器字体颜色
    [root@node2 ~]# vim /etc/fstab
    > /dev/vdb5 swap swap defaults 0 0

```

## 5、完成创建逻辑卷

> 根据如下要求，创建新的逻辑卷：
>
> 1、逻辑卷取名为 qa ，属于 qagroup 卷组，大小为 60 个扩展块
>
> 2、qagroup 卷组中逻辑卷的扩展块大小应当为 16 MiB
>
> 3、使用 ext3 文件系统格式化新逻辑卷。该逻辑卷应在系统启动时自动挂载到 /mnt/qa 下

* pvcreate = physical volume
* vgcreate = volume group
* lvcreate = logical volume
* 如果顺序错了，赶紧用对应的**remove删除

```bash

    // 管理/dev/vdb
    [root@node2 ~]# fdisk /dev/vdb

    // 新建分区，起始扇区直接回车，终点扇区+1G，p检查，w写入并退出
    > Command (m for help): n
    > First sector :
    > Last sector, +sectors or +size{K,M,G,T,P} : +1G
    > Command (m for help): p
    > Command (m for help): w
    
    // 转换为物理卷
    [root@node2 ~]# pvcreate /dev/vdb6

    // 物理卷设置PE基本单元大小16M，指定卷名，创建卷组
    [root@node2 ~]# vgcreate qagroup /dev/vdb6 -s 16M

    // 将卷组分为60个逻辑卷，并命名（小l表数量，大L表大小）
    [root@node2 ~]# lvcreate -n qa -l 60 qagroup

    // 根据题目，指定格式化类型
    [root@node2 ~]# mkfs.ext3 /dev/qagroup/qa

    // 根据题目创建目录
    [root@node2 ~]# mkdir /mnt/qa

    // 改配置
    [root@node2 ~]# vi /etc/fstab
    > /dev/qagroup/qa /mnt/qa ext3 defaults 0 0

    // 检查是否能挂上（不能还重启的话，就会进入紧急模式，在里面重写配置
    [root@node2 ~]# mount -a

    // 检查
    [root@node2 ~]# df -h
```

## 6、完成创建 VDO 卷

> 根据如下要求，创建新的 VDO 卷：
>
> 1、使用未分区的磁盘
>
> 2、该卷的名称为 vdough
>
> 3、该卷的逻辑大小为 50G
>
> 4、该卷使用 xfs 文件系统格式化
>
> 5、该卷（在系统启动时）挂载到 /vbread 下

```bash

    // 查看vdo的相关包
    [root@node2 ~]# yum search vdo
    vdo.x86_64 : Management tools for Virtual Data Optimizer
    kmod-kvdo.x86_64 : Kernel Modules for Virtual Data Optimizer

    // 安装 kmod-kvdo 包
    [root@node2 ~]# yum -y install vdo kmod-kvdo

    // 查看vdo create的用法
    [root@node2 ~]# man vdo |grep “vdo create”
    # vdo create --name=vdo0 --device=/dev/sdb1 --vdoLogicalSize=10T

    // 改名字和大小
    [root@node2 ~]# vdo create --name=vdough --device=/dev/vdc --vdoLogicalSize=50G

    // 快速格式化，或者可以不-K
    [root@node2 ~]# mkfs.xfs -K /dev/mapper/vdough
    [root@node2 ~]# udevadm settle

    // 按要求建文件夹和改挂载
    [root@node2 ~]# mkdir /vbread
    [root@node2 ~]# vi /etc/fstab
    > /dev/mapper/vdough /vbread xfs defaults,_netdev 1 2

    // 尝试挂载和检查
    [root@node2 ~]# mount -a
    [root@node2 ~]# df -h
```

## 7、完成配置系统调优

> 为您的系统选择建议的 tuned 配置集并将它设为默认设置。

```bash

    [root@node2 ~]# yum -y install tuned
    // 查看推荐
    [root@node2 ~]# tuned-adm recommend
    # virtual-guest
    [root@node2 ~]# tuned-adm active
    # Current active profile: virtual-guest
    [root@node2 ~]# tuned-adm profile virtual-guest
```

---

### *其他考题任务*

在 node1 上执行以下任务

## 1、添加 sudo 免密操作（新增）

> 允许 sysmgrs 组成员 sudo 时不需要密码

```bash

    // visudo区别于直接改配置文件，退出前有语法检查
    [root@node1 ~]# visudo
    > # %wheel ALL=(ALL) NOPASSWD: ALL
    > %sysmgrs ALL=(ALL) NOPASSWD: ALL

    // 测试
    [root@node1 ~]# su - natasha
    [natasha@node1 ~]$ sudo useradd liweijie
    [natasha@node1 ~]$ sudo userdel -r liweijie
```

## 2、配置创建新用户的密码策略（新增）

> 创建新用户时，默认密码策略为 20 天后，密码会过期

```bash

    [root@node1 ~]# vim /etc/login.defs
    > PASS_MAX_DAYS 20
    [root@node1 ~]# useradd dog
    [root@node1 ~]# chage -l dog
    > Last password change : Apr 29, 2021
    > Password expires : May 19, 2021
    > Password inactive : never
    > Account expires : never
    > Minimum number of days between password change : 0
    > Maximum number of days between password change : 20
    > Number of days of warning before password expires : 
```

## 3、创建脚本

> 创建一个名为 newsearch 的脚本
> 1、该脚本放置在 /usr/bin 下
> 2、该脚本用来查找 /usr 下所有大于 30k ，但是小于 50k 且具有 SUID 权限的文件，将这些文件放置于 /root/newfiles 下

&#x1F4CC;UID权限则为-u=s或/u=s，GID权限则为-g=s或/g=s

```bash

    // 创建一个文本文件，判断语法以if开头fi结尾，-d检查是否目录
    [root@node1 ~]# vim /usr/bin/newsearch
    > #!/bin/bash
    > if [ ! -d /root/newfiles ];then
    >   mkdir /root/newfiles
    > fi
    > find /usr -size +30k -size -50k -perm /u=s -exec cp -a {} /root/newfiles \;

    // 或者
    > mkdir /root/newfiles    
    > find /usr -size +30k -size -50k -perm /u=s -exec cp -a {} /root/newfiles \;

    // 所有人都有权限
    [root@node1 ~]# chmod +x /usr/bin/newsearch
    
    // 运行脚本
    [root@node1 ~]# /usr/bin/newsearch
    
    // 检查
    [root@node1 ~]# ll -h /root/newfiles 
    > -rws--x--x. 1 root root 33K Dec 17 2019 chfn
    > -rwsr-x---. 1 root cockpit-wsinstance 46K Mar 12 2020 cockpit-session
    > -rwsr-xr-x. 1 root root 33K Dec 13 2019 passwd
    > -rwsr-xr-x. 1 root root 33K Dec 17 2019 umount
    > -rwsr-xr-x. 1 root root 37K Dec 19 2019 unix_chkpwd
    > -rws--x--x. 1 root root 47K Nov 20 2018
```

## 4、设置默认权限

> 用户 manalo 在 node1 上，所有新创建的文件都应具有 -r--r--r-- 的默认权限，此用户的所有新创建目录应具有 dr-xr-xr-x 的默认权

```bash

    [root@node1 ~]# su - manalo
    [manalo@node1 ~]$ vim ~/.bashrc
    > umask 222
    [manalo@node1 ~]$ souce ~/.bashrc //配置生效

    // 检查
    [manalo@node1 ~]$ touch a.txt
    [manalo@node1 ~]$ mkdir dir1
    [manalo@node1 ~]$ ll
    total 0
    -r--r--r--. 1 a a 0 Oct 26 18:04 a.txt
    dr-xr-xr-x. 2 a a 6 
```

## 5、配置容器使其自动启动（B 卷）

 > 利用注册服务器上的 rsyslog 镜像，创建一个名为 logger 的容器
 >
 > 1、面向 wallah 用户，配置一个 systemd 服务
 >
 > 2、该服务命名为 container-logger` ，并在系统重启时自动启动，无需干预
 >
 > 3、将服务配置为在启动时自动将 /home/wallah/var_log` 挂载到容器中的 /var/log 下
 >
 > 4、在容器中执行命令 podman exec logger logger -p authpriv.info SUIBIAN

```bash

    [root@node1 ~]# ssh wallah@node1
    [wallah@node1 ~]$ podman login -u admin -p redhat321
    > WARNING! ...
    > login Succeeded!
    [wallah@node1 ~]$ podman search registry.domain250.example.com/
    > INDEX NAME DESCRIPTION STARS OFFICIAL AUTOMATED
    > example.com registry.domain250.example.com/rhel8/rsyslog 0
    [wallah@node1 ~]$ podman run -d --name logger -v /home/wallah/var_log:/var/log:Z registry.domain250.example.com/rhel8/rsyslog
    [wallah@node1 ~]$ podman stop logger
    [wallah@node1 ~]$ loginctl enable-linger
    [wallah@node1 ~]$ loginctl show-user wallah
    [wallah@node1 ~]$ mkdir -p ~/.config/systemd/user/
    [wallah@node1 ~]$ cd ~/.config/systemd/user/
    [wallah@node1 ~]$ podman generate systemd -n logger -f
    [wallah@node1 ~]$ systemctl --user enable --now container-logger
    [wallah@node1 ~]$ systemctl --user status container-logger
    [wallah@node1 ~]$ podman exec logger logger -p authpriv.info SUIBIAN

    [wallah@node1 ~]$ grep SUIBIAN /home/wallah/var_log/secure
    > 2021-08-13T11:45:37.824980+00:00 574761d4236a root: SUIBIAN
    [wallah@node1 ~]$ exit
    [root@node1 ~]# reboot
    [root@node1 ~]# ps -aux | grep podman
```

## 6、容器 nginx

> 利用注册服务器上的 nginx 镜像，创建一个名为 nginx 的容器
> 1、面向 wallah 用户，配置一个 systemd 服务 该服务命名为 container-nginx`，并在系统重启时自动启动，无需干预
> 2、在 /home/wallah/www 下创建文件 index.html ，内容为 hello nginx
> 3、将服务配置为在启动时自动将/home/wallah/www挂载到容器中的/usr/share/nginx/html下将容器主机上的端口 8080 映射到容器上的端口 80

```bash

    [root@node1 ~]# ssh wallah@node1
    [wallah@node1 ~]$
    [wallah@node1 ~]$ podman login registry.domain250.example.com
    > Username: `admin`
    > Password: `redhat321`
    > login Succeeded!
    [wallah@node1 ~]$ podman search registry.domain250.example.com/
    > INDEX NAME DESCRIPTION STARS OFFICIAL AUTOMATED
    > example.com registry.domain250.example.com/library/nginx 0
    [wallah@node1 ~]$ mkdir /home/wallah/www
    [wallah@node1 ~]$ echo hello nginx > /home/wallah/www/index.html
    [wallah@node1 ~]$ podman run -d --name nginx -v /home/wallah/www:/usr/share/nginx.html:Z -p 8080:80 registry.domain250.example.com/library/nginx
    [wallah@node1 ~]$ loginctl enable-linger
    [wallah@node1 ~]$ loginctl show-user wallah
    [wallah@node1 ~]$ mkdir -p ~/.config/systemd/user/
    [wallah@node1 ~]$ cd ~/.config/systemd/user/
    [wallah@node1 ~]$ podman generate systemd -n nginx -f
    [wallah@node1 ~]$ podman stop nginx
    [wallah@node1 ~]$ systemctl --user enable --now container-nginx
    [wallah@node1 ~]$ systemctl --user status container-nginx
    [wallah@node1 ~]$ curl localhost:8080
    > hello nginx
    [wallah@node1 ~]$ exit
    [wallah@node1 ~]# reboot
    [wallah@node1 ~]# curl localhost:8080
    [wallah@node1 ~]# ss -antup | grep 8080
    [wallah@node1 ~]# ps -aux | grep
```
