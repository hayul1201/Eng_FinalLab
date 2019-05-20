# 1. Check vm.swappiness on all your nodes

##### cat /proc/sys/vm/swappiness
<pre><code>
[centos@ip-172-31-38-77 ~]$ cat /proc/sys/vm/swappiness
30
</code></pre>

##### sudo sysctl -w vm.swappiness=1
##### cat /proc/sys/vm/swappiness
<pre><code>
[centos@ip-172-31-38-77 ~]$ cat /proc/sys/vm/swappiness
1
</code></pre>

---------------------------------------

# 2. Show the mount attributes of your volume(s)

##### df -h
<pre><code>
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      8.0G  896M  7.2G  11% /
devtmpfs        7.8G     0  7.8G   0% /dev
tmpfs           7.8G     0  7.8G   0% /dev/shm
tmpfs           7.8G   17M  7.8G   1% /run
tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
tmpfs           1.6G     0  1.6G   0% /run/user/1000
</code></pre>

---------------------------------------

# 3. If you have ext-based volumes, list the reserve space setting

##### sudo lsblk -f
<pre><code>
NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
xvda
└─xvda1 xfs          f41e390f-835b-4223-a9bb-9b45984ddf8d /
</code></pre>

>XFS volumes do not support reserve space

---------------------------------------

# 4. Disable transparent hugepage support

##### cat /sys/kernel/mm/transparent_hugepage/enabled
<pre><code>
[centos@ip-172-31-38-77 ~]$ cat /sys/kernel/mm/transparent_hugepage/enabled
[always] madvise never
</code></pre>

##### sudo grep AnonHugePages /proc/meminfo
<pre><code>
[centos@ip-172-31-38-77 ~]$ sudo grep AnonHugePages /proc/meminfo
AnonHugePages:      6144 kB
</code></pre>

##### sudo vi /etc/default/grub
<pre><code>
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial --speed=115200"
GRUB_CMDLINE_LINUX="console=tty0 crashkernel=auto console=ttyS0,115200 <b>transparent_hugepage=never</b>"
GRUB_DISABLE_RECOVERY="true"
</code></pre>

> disable at boottime

##### sudo grub2-mkconfig -o /boot/grub2/grub.cfg

##### shutdown -r now
> reboot

##### cat /sys/kernel/mm/transparent_hugepage/enabled
<pre><code>
[centos@ip-172-31-38-77 ~]$ cat /sys/kernel/mm/transparent_hugepage/enabled
always madvise [never]
</code></pre>

> applied after reboot

---------------------------------------

# 5. List your network interface configuration

##### sudo ifconfig
<pre><code>
[centos@ip-172-31-38-77 ~]$ sudo ifconfig
ens3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.38.77  netmask 255.255.240.0  broadcast 172.31.47.255
        inet6 fe80::896:2dff:fed6:4258  prefixlen 64  scopeid 0x20<link>
        ether 0a:96:2d:d6:42:58  txqueuelen 1000  (Ethernet)
        RX packets 305  bytes 45097 (44.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 439  bytes 45503 (44.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 6  bytes 416 (416.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6  bytes 416 (416.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

</code></pre>

---------------------------------------

# 6. Show that forward and reverse host lookups are correctly resolved

##### sudo yum install bind-utils net-tools -y
> install for nslookup

##### nslookup 172.31.38.77
<pre><code>
[centos@ip-172-31-38-77 ~]$ nslookup 172.31.38.77
Server:         172.31.0.2
Address:        172.31.0.2#53

Non-authoritative answer:
77.38.31.172.in-addr.arpa       name = ip-172-31-38-77.ap-northeast-2.compute.internal.

Authoritative answers can be found from:
</code></pre>

#####  nslookup ip-172-31-38-77.ap-northeast-2.compute.internal
<pre><code>
[centos@ip-172-31-38-77 ~]$ nslookup ip-172-31-38-77.ap-northeast-2.compute.internal
Server:         172.31.0.2
Address:        172.31.0.2#53

Non-authoritative answer:
Name:   ip-172-31-38-77.ap-northeast-2.compute.internal
Address: 172.31.38.77
</code></pre>

##### sudo vi /etc/hosts
<pre><code>
172.31.40.146 foo-1.example.com foo-1
172.31.34.248 foo-2.example.com foo-2
172.31.39.214 foo-3.example.com foo-3
172.31.38.77 foo-4.example.com foo-4
172.31.44.2 foo-5.example.com foo-5
</code></pre>

---------------------------------------

# 7. Show the nscd service is running
##### sudo yum install nscd
> install nscd

##### sudo service nscd start

##### sudo service nscd status
<pre><code>
[centos@ip-172-31-38-77 ~]$ sudo service nscd status
Redirecting to /bin/systemctl status nscd.service
● nscd.service - Name Service Cache Daemon
   Loaded: loaded (/usr/lib/systemd/system/nscd.service; disabled; vendor preset                                                                    : disabled)
   Active: active (running) since Mon 2019-05-20 06:14:52 UTC; 5s ago
  Process: 4955 ExecStart=/usr/sbin/nscd $NSCD_OPTIONS (code=exited, status=0/SU                                                                    CCESS)
 Main PID: 4956 (nscd)
   CGroup: /system.slice/nscd.service
           └─4956 /usr/sbin/nscd

May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal nscd[4956]: 4...
May 20 06:14:52 ip-172-31-38-77.ap-northeast-2.compute.internal systemd[1]: S...
Hint: Some lines were ellipsized, use -l to show in full.
</code></pre>

---------------------------------------

# 8. Show the ntpd service is running
##### sudo yum install ntp
> install ntpd

##### sudo service ntpd start

##### sudo service ntpd status
<pre><code>
[centos@ip-172-31-38-77 ~]$ sudo service ntpd status
Redirecting to /bin/systemctl status ntpd.service
● ntpd.service - Network Time Service
   Loaded: loaded (/usr/lib/systemd/system/ntpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2019-05-20 06:16:57 UTC; 4s ago
  Process: 5054 ExecStart=/usr/sbin/ntpd -u ntp:ntp $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 5055 (ntpd)
   CGroup: /system.slice/ntpd.service
           └─5055 /usr/sbin/ntpd -u ntp:ntp -g

May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listen and drop on 0 v4wildcard 0.0.0.0 UDP 123
May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listen and drop on 1 v6wildcard :: UDP 123
May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listen normally on 2 lo 127.0.0.1 UDP 123
May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listen normally on 3 ens3 172.31.38.77 UDP 123
May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listen normally on 4 lo ::1 UDP 123
May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listen normally on 5 ens3 fe80::896:2dff:fed6:4258 UDP 123
May 20 06:16:57 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: Listening on routing socket on fd #22 for interface updates
May 20 06:16:58 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: 0.0.0.0 c016 06 restart
May 20 06:16:58 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: 0.0.0.0 c012 02 freq_set kernel 0.000 PPM
May 20 06:16:58 ip-172-31-38-77.ap-northeast-2.compute.internal ntpd[5055]: 0.0.0.0 c011 01 freq_not_set
</code></pre>

---------------------------------------


# install jdk

##### yum list java*jdk-devel
<pre><code>
[centos@ip-172-31-38-77 ~]$ yum list java*jdk-devel
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
Available Packages
java-1.6.0-openjdk-devel.x86_64                                          1:1.6.0.41-1.13.13.1.el7_3                                          base
java-1.7.0-openjdk-devel.x86_64                                          1:1.7.0.221-2.6.18.0.el7_6                                          updates
java-1.8.0-openjdk-devel.i686                                            1:1.8.0.212.b04-0.el7_6                                             updates
java-1.8.0-openjdk-devel.x86_64                                          1:1.8.0.212.b04-0.el7_6                                             updates
java-11-openjdk-devel.i686                                               1:11.0.3.7-0.el7_6                                                  updates
java-11-openjdk-devel.x86_64                                             1:11.0.3.7-0.el7_6                                                  updates
[centos@ip-172-31-38-77 ~]$
</code></pre>

##### sudo yum install java-1.8.0-openjdk-devel.x86_64

##### rpm -qa java*jdk-devel
<pre><code>
[centos@ip-172-31-38-77 ~]$ rpm -qa java*jdk-devel
java-1.8.0-openjdk-devel-1.8.0.212.b04-0.el7_6.x86_64
</code></pre>

##### javac -version
<pre><code>
[centos@ip-172-31-38-77 ~]$ javac -version
javac 1.8.0_212
</code></pre>

>설치확인

---------------------------------------

# install mariadb

##### sudo yum install mariadb-server

##### sudo systemctl stop mariadb

##### sudo vi /etc/my.cnf


##### sudo yum install wget

##### sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/5.16.1/ -P /etc/yum.repos.d/

##### sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-clouderac



sudo systemctl stop mariadb

sudo vi /etc/my.cnf

<pre><code>
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
transaction-isolation = READ-COMMITTED
# Disabling symbolic-links is recommended to prevent assorted security risks;
# to do so, uncomment this line:
symbolic-links = 0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

key_buffer = 16M
key_buffer_size = 32M
max_allowed_packet = 32M
thread_stack = 256K
thread_cache_size = 64
query_cache_limit = 8M
query_cache_size = 64M
query_cache_type = 1

max_connections = 550
#expire_logs_days = 10
#max_binlog_size = 100M

#log_bin should be on a disk with enough free space.
#Replace '/var/lib/mysql/mysql_binary_log' with an appropriate path for your
#system and chown the specified folder to the mysql user.
log_bin=/var/lib/mysql/mysql_binary_log

#In later versions of MariaDB, if you enable the binary log and do not set
#a server_id, MariaDB will not start. The server_id must be unique within
#the replicating group.
server_id=1

binlog_format = mixed

read_buffer_size = 2M
read_rnd_buffer_size = 16M
sort_buffer_size = 8M
join_buffer_size = 8M

# InnoDB settings
innodb_file_per_table = 1
innodb_flush_log_at_trx_commit  = 2
innodb_log_buffer_size = 64M
innodb_buffer_pool_size = 4G
innodb_thread_concurrency = 8
innodb_flush_method = O_DIRECT
innodb_log_file_size = 512M

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
</code></pre>

> 위 내용으로 /etc/my.cnf 내용 수정

##### sudo systemctl enable mariadb

##### sudo systemctl start mariadb

##### sudo /usr/bin/mysql_secure_installation
<pre><code>
[...]
Enter current password for root (enter for none):
OK, successfully used password, moving on...
[...]
Set root password? [Y/n] Y
New password:
Re-enter new password:
[...]
Remove anonymous users? [Y/n] Y
[...]
Disallow root login remotely? [Y/n] N
[...]
Remove test database and access to it [Y/n] Y
[...]
Reload privilege tables now? [Y/n] Y
[...]
All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
</code></pre>

---------------------------------------

# install jdbc

##### sudo yum install wget

##### sudo wget "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz"

##### tar zxvf mysql-connector-java-5.1.46.tar.gz

##### sudo mkdir -p /usr/share/java/

##### cd mysql-connector-java-5.1.46

##### sudo cp mysql-connector-java-5.1.46-bin.jar /usr/share/java/mysql-connector-java.jar

##### mysql -u root -p

<pre><code>
drop database if exists amon;
create database amon character set utf8;
grant all privileges on amon.* to amon@'%' identified by 'amon_password';

drop database if exists rman;
create database rman character set utf8;
grant all privileges on rman.* to rman@'%' identified by 'rman_password';

drop database if exists metastore;
create database metastore character set utf8;
grant all privileges on metastore.* to hive@'%' identified by 'hive_password';

drop database if exists sentry;
create database sentry character set utf8;
grant all privileges on sentry.* to sentry@'%' identified by 'sentry_password';

drop database if exists nav;
create database nav character set utf8;
grant all privileges on nav.* to nav@'%' identified by 'nav_password';

drop database if exists navms;
create database navms character set utf8;
grant all privileges on navms.* to navms@'%' identified by 'navms_password';

drop database if exists cdh;
create database cdh character set utf8;
grant all privileges on cdh.* to cdh@'%' identified by 'cdh';

drop database if exists hue;
create database hue character set utf8;
grant all privileges on hue.* to hue@'%' identified by 'hue';

drop database if exists oozie;
create database oozie character set utf8;
grant all privileges on oozie.* to oozie@'%' identified by 'oozie';

drop database if exists hive;
create database hive character set utf8;
grant all privileges on hive.* to hive@'%' identified by 'hive';

drop database if exists scm;
create database scm character set utf8;
grant all privileges on scm.* to scm@'%' identified by 'scm';
</code></pre>

##### sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

##### sudo yum install cloudera-manager-daemons cloudera-manager-server
> master node 에만 설치

##### sudo yum install cloudera-manager-daemons cloudera-manager-agent

##### sudo systemctl start cloudera-scm-agent
