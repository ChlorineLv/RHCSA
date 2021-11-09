<!--
 * @Description  : 
 * @Python       : 
 * @Author       : ChlorineLv@outlook.com
 * @Date         : 2021-11-01 14:11:00
 * @LastEditors  : ChlorineLv@outlook.com
 * @LastEditTime : 2021-11-09 14:08:27
-->

# RHCSA考试备注

(Redhat Certified System Administrator)

考试机fundation，有两个下挂虚拟机node1，node2。

考题给node1密码（node2需要自行破解）

# 重要配置信息

在考试期间，除了您就坐位置的台式机之外，还将使用多个虚拟系统。您不具有台式机系统的根访问权，但具有对虚拟系统的完全根访问权。

## 系统信息

系统IP 地址
node1.domain250.example.com 172.25.250.100
node2.domain250.example.com 172.25.250.200

您使用的系统属于 DNS 域 domain250.example.com。该域中的所有系统都位于 172.25.250.0/255.255.255.0 子网中，该子网中的所有系统都位于 domain250.example.com 中。

针对这些系统列出的 IP 地址是应该分配给系统的地址。您可能需要为一个或两个系统配置网络，以便能够通过上述地址访问您的地址。
帐户信息

node1 的根密码已经设置为 flectrag 。

除非另有指定，否则这将是用于访问其他系统和服务的密码。此外，除非另有指定，否则应将该密码用于您创建的什么问题帐户或者需要设置密码的任意服务。
其他信息

您可以通过 SSH 或控制台访问考试系统（参见下文所述）。请注意，SSH 访问权可能取决于您解答其他考试项目的情况。

如果您需要在系统上安装其他软件，可以使用位于以下地址的存储库：

    http://content/dvd/BaseOS
    http://content/dvd/AppStream

注册服务器信息

注册服务器地址 registry.domain250.example.com

使用 admin 作为用户名，使用 redhat321 作为映像注册表的凭据
重要评测信息

您的系统会在重新引导后进行评测，因此务必确保您实施的的所有配置和服务在重新引导后仍然保留。服务必须在没有人工干预的情况下启动。

同样，本次考试使用的所有虚拟实例都必须 能够重新引导至适当的多用户目标，而无需任何人工辅助。在无法引导或无法进行无人干预引导的系统上完成的所有操作都将为零分。

# 考试要求

## 在您的系统上执行以下所有步骤

## 将 node1 配置为具有以下网络配置

>主机名：node1.domain250.example.com
>
>IP 地址：172.25.250.100
>
>子网掩码：255.255.255.0
>
>网关：172.25.250.254
>
>DNS服务器：172.25.250.254

<details>
    <summary>参考答案</summary>

                    // 改主机名
    [root@node1 ~]# hostnamectl set-hostname node1.domain250.example.com
    
    IP解法一：
                    // 图形化配IP，子网掩码用ip/来写
    [root@node1 ~]# nmtui
    
    IP解法二：
                    // 命令行配IP
    [root@node1 ~]# nmcli connection modify "Wired connection 1" ipv4.addresses 172.25.250.100/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.254.250 ipv4.method manual connection.autoconnect yes
    [root@node1 ~]# nmcli connection up "Wired connection 1"

    检查：
    [root@node1 ~]# ip addr                 //检查 IP
    [root@node1 ~]# cat /etc/resolv.conf    //检查 DNS
    [root@node1 ~]# ip route                //检查网关
    [root@node1 ~]# cat /etc/hostname       //检查主机名
    [root@node1 ~]# ping node1.domain250.example.com
    [root@node1 ~]# ping registry.domain250.example.com

</details>

## 完成配置您的系统以使用默认存储库

> YUM 存储库已可以从 http://foundation0.ilt.example.com/dvd/BaseOS 和 http://foundation0.ilt.example.com/dvd/AppStream 使用配置您的系统，以将这些位置用作默认存储库

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# vim /etc/yum.repos.d/dvd.repo
    > [base]
    > name=base
    > baseulr='http://foundation0.ilt.example.com/dvd/BaseOS'
    > enabled=1
    > gpgcheck=0
    > 
    > [app]
    > name=app
    > baseurl='http://foundation0.ilt.example.com/dvd/AppStream'
    > enabled=1
    > gpgcheck=0
                    // 考试无需，仅用于查看是否成功
    [root@node1 ~]# yum -y install vim
    [root@node1 ~]# rpm -qa | grep xxxx
    [root@node1 ~]# yum provides xxxx
</details>

## 完成调试 SELinux

>非标准端口 82 上运行的 Web 服务器在提供内容时遇到问题。根据需要调试并解决问题，使其满足以下条件：
>
>1、系统上的 Web 服务器能够提供 /var/www/html 中所有现有的 HTML 文件（注：不要删除或以其他方式改动现有的文件内容）
>
>2、Web 服务器在端口 82 上提供此内容
>
>3、Web 服务器在系统启动时自动启动

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成创建用户帐户

>创建下列用户、组和组成员资格：
>
>1、名为 sysmgrs 的组
>
>2、用户 natasha ，作为次要组从属于 sysmgrs
>
>3、用户 harry ，作为次要组还从属于 sysmgrs
>
>4、用户 sarah ，无权访问系统上的交互式 shell 且不是 sysmgrs 的成员
>
>5、natasha、harry 和 sarah 的密码应当都是 flectrag

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# groupadd sysmgrs
    [root@node1 ~]#
    
</details>

## 完成配置 cron 作业

>配置 cron 作业，该作业每隔 2 分钟运行并执行以下命令：
>
>1、logger "EX200 in progress"，以用户 natasha 身份运行

<details>
    <summary>参考答案</summary>
    
    [root@node1 ~]# crontab -e -u natash
    > */2 * * * * logger "EX200 in progress"
    [root@node1 ~]# systemctl status crond
    [root@node1 ~]# systemctl enable --now crond
</details>

## 完成创建协作目录

>创建具有以下特征的协作目录 /home/managers ：
>
>1、/home/managers 的组用权是 sysmgrs
>
>2、目录应当可被 sysmgrs 的成员读取、写入和访问，但任何其他用户不具这些权限。（当然，root 用户有权访问系统上的所有文件和目录）
>
>3、/home/managers 中创建的文件自动将组所有权设置到 sysmgrs 组

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成配置 NTP

>配置您的系统，使其成为 materials.example.com 的 NTP 客户端。（注：materials.example.com 是 classroom.example.com 的 DNS 别名）

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成配置 autofs

>配置 autofs ，以按照如下所述自动挂载远程用户的主目录：
>
>1、materials.example.com ( 172.25.254.254 ) NFS 导出 /rhome 到您的系统。此文件系统包含为用户 remoteuser1 预配置的主目录
>
>2、remoteuser1 的主目录是 materials.example.com:/rhome/remoteuser1
>
>3、remoteuser1 的主目录应自动挂载到本地 /rhome 下的 /rhome/remoteuser1
>
>4、主目录必须可供其用户写入
>
>5、remoteuser1 的密码是 flectrag

<details>
    <summary>参考答案</summary>

                    // 查看服务器有无共享目录（可做可不做）
    [root@node1 ~]# showmount -e materials.example.com
                    // 安装autofs
    [root@node1 ~]# yum -y install autofs
                    // 配置文件里加一行，用于监视/rhome
    [root@node1 ~]# vi /etc/auto.master
    > /rhome /etc/auto.misc
                    // 监视remoteuser1目录，并挂载到远程（本地目录，权限，远程目录）
    [root@node1 ~]# vi /etc/auto.misc
    > remoteuser1 -fstype=nfs,rw materials.example.com:/rhome/remoteuser1
                    // 重启服务，设置开机自启
    [root@node1 ~]# systemctl start autofs
    [root@node1 ~]# systemctl enable autofs
</details>

## 完成配置 /var/tmp/fstab 权限

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

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成配置用户帐户

>配置用户 manalo ，其用户 ID 为 3533。此用户的密码应当为 flectrag。

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# useradd -u 3533 manalo
    [root@node1 ~]# echo flectrag | passwd --stdin manalo
</details>

## 完成查找文件

>查找当 jacques 所有的所有文件并将其副本放入 /root/findfiles 目录

* 利用find  [选项]  查找条件  目标文件——查
* 利用  会将root结尾的结果覆写进/root/a.txt里（如无新建有则覆写）
* -exec cp -a {} /root/findfiles 会将前面的结果传进 { } 里执行接下来的语句

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成查找字符串

>查找文件 /usr/share/xml/iso-codes/iso_639_3.xml 中包含字符串 ng 的所有行。将所有这些行的副本按原始顺序放在文件 /root/list 中。 /root/list 不得包含空行，且所有行必须是 /usr/share/xml/iso-codes/iso_639_3.xml 中原始行的确切副本。
<details>
    <summary>笔记</summary>
    
</details>

* 利用grep  [选项]  查找条件  目标文件——查grep root /etc/passwd  查root有关（^root查开头，root$查结尾）

* 利用grep root$ /etc/passwd > /root/a.txt  会将root结尾的结果覆写进/root/a.txt里（如无新建有则覆写）
* 选中字符，鼠标中键即可粘帖所选字符


<details>
    <summary>参考答案</summary>
    
    [root@node1 ~]# grep ng /usr/share/xml/iso-codes/iso_639_3.xml /root/list
    
</details>

## 完成创建存档

>创建一个名为 /root/backup.tar.gz 的 tar 存档，其应包含 /usr/local 的 tar 存档，其应包含 /usr/local 的内容。该 tar 存档必须使用 gzip 进行压缩。

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成配置容器使其自动启动

>利用注册服务器上的 rsyslog 镜像，创建一个名为  logserver 的容器
>
>1、面向 wallah 用户，配置一个 systemd 服务
>
>2、该服务命名为 container-logserver ，并在系统重启时自动启动，无需干预

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成为容器配置持久存储

>通过以下方式扩展上一个任务的服务
>
>1、配置主机系统的 journald 日志以在系统重启后保留数据，并重新启动日志记录服务
>
>2、将主机 /var/log/journal目录下任何以 *.journal 的文件复制到 /home/wallah/container_logfile 中
>
>3、将服务配置为在启动时自动将/home/wallah/container_logfile 挂载到容器中的 /var/log/journal 下

<details>
    <summary>参考答案</summary>
    
    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

---

### node2任务

在 node2.domain250.example.com 上执行以下任务


## 完成设置 root 密码

>将 node2 的 root 密码设置为 flectrag 。您需要获得系统访问权限才能进行此操作。

<details>
    <summary>参考答案</summary>

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
                // SELinux初始化，重新打标签，让改密码生效
    sh-4.4# touch /.autorelable
    sh-4.4# exit
    Swith_root:/# exit
</details>

## 完成配置您的系统以使用默认存储库

>YUM 存储库已可以从 http://foundation0.ilt.example.com/dvd/BaseOS 和 http://foundation0.ilt.example.com/dvd/AppStream 使用配置您的系统，以将这些位置用作默认存储库

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成调整逻辑卷大小

>将逻辑卷 vo 及其文件系统的大小调整到 230 MiB。确保文件系统内容保持不变。注：分区大小很少与请求的大小完全相同，因此可以接受范围为 217 MiB 到 243 MiB 的大小。

<details>
    <summary>参考答案</summary>
    
    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成添加交换分区

>向您的系统添加一个额外的交换分区 756MiB 。交换分区应在系统启动时自动挂载。不要删除或以任何方式改动系统上的任何现有交换分区。

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成创建逻辑卷

>根据如下要求，创建新的逻辑卷：
>
>1、逻辑卷取名为 qa ，属于 qagroup 卷组，大小为 60 个扩展块
>
>2、qagroup 卷组中逻辑卷的扩展块大小应当为 16 MiB
>
>3、使用 ext3 文件系统格式化新逻辑卷。该逻辑卷应在系统启动时自动挂载到 /mnt/qa 下

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成创建 VDO 卷

>根据如下要求，创建新的 VDO 卷：
>
>1、使用未分区的磁盘
>
>2、该卷的名称为 vdough
>
>3、该卷的逻辑大小为 50G
>
>4、该卷使用 xfs 文件系统格式化
>
>5、该卷（在系统启动时）挂载到 /vbread 下

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>

## 完成配置系统调优

>为您的系统选择建议的 tuned 配置集并将它设为默认设置。

<details>
    <summary>参考答案</summary>

    [root@node1 ~]# mkdir /root/findfiles
    [root@node1 ~]# find / user jacques -exec cp -a {} /root/findfiles/ \;
</details>
