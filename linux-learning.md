1.统计一个文件中字符出现的次数
perl -e 'while(<>){$count+= s/[str]//g;} print "$count\n";' [filename]



平台: RHEL5.4

执行rpm -q 等命令就一直hang在那里不动, ps看一下发现n多的rpmq进程挂住了. 

这些进程是每晚crontab执行/etc/cron.daily/rpm后, 更新本机rpm库到 /var/lib/rpm/__db.* 文件里. 可能曾经 /var 目录磁盘满过, 所以才出现这样情况, 现在要做的如下:

# rm -f /var/lib/rpm/__db.*

然后再执行rpm命令就没有问题了. 最好再重建一下rpm库

# rpm -vv --rebuilddb
# /etc/cron.daily/rpm

#### 阻止特定IP访问 
$ iptables -A INPUT -p tcp --dport $port -s $ip/32 -d 0.0.0.0/0 -j REJECT
来自$ip访问本地$port端口的访问被阻止。
2.	统计一个文件($filename)中某个字符出现的次数
$ perl -e 'while(<>){$count+= s/[$string]//g; } print "$count\n";'  $filename
$ grep -c ‘$string’$filename
3.	系统任务
$ crontab [-u user] file
$ crontab [-u user] { -e | -l | -r }
4.	内存统计
$ ps aux|awk \
'BEGIN {CPU=0;MEM=0;MEMN=0} /httpd/ {CPU=CPU+$3;MEM=MEM+$4;MEMN=MEMN+$5} END {print "cpu used " CPU "|mem used " MEM "|mem all size is " MEMN}'

##### 删除大量文件
+ 使用`rsync`

		mkdir /tmp/blankdir
		rsync --delete-before  --progress --stats /tmp/blankdir/ /target/ 
+ 使用`find -exec`

		find . -name *.txt -exec ls -l {} \;

+ 使用`find xargs`

		find . -name *.txt | xargs rm -f


#### Vncserver

+ 安装桌面(建议使用xfce，但可以使用`yum -y groupinstall kde`或者`yum -y groupinstall gnome`)：

		yum -y groupinstall xfce   

+ 安装vncserver：

		yum -y install vnc vnc-server

+ 编辑`/etc/sysconfig/vncservers`

		VNCSERVERS="1:root" 
		VNCSERVERARGS[1]="-geometry 1600x900"

+ 编辑`/root/.vnc/xstartup`

		#!/bin/sh 
		/usr/bin/startxfce4

+ 设定密码：

		vncpasswd

+ 启动：

		service vncserver restart


