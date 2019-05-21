# 1. Enable user / password login for each of the 5 nodes

## a. Create a password for user “centos”

##### sudo passwd centos
<pre><code>
[centos@ip-172-31-9-8 ~]$ sudo passwd centos
Changing password for user centos.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
</code></pre>

## b. Modify sshd_config to allow password login
##### sudo vi /etc/ssh/sshd_config
##### 패스워드 인증을 허용
<pre><code>
.....
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication <b>yes</b>
....
</code></pre>

## c. Restart the sshd.service
##### sudo systemctl restart sshd.service

---------------------------------------

# 2. Setup /etc/hosts with the following information for each of the 5 hosts
## a. Private_IP FQDN Shortcut

##### sudo vi /etc/hosts
<pre><code>
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.31.9.8      ts1.test.com    ts1
172.31.13.235   ts2.test.com    ts2
172.31.13.60    ts3.test.com    ts3
172.31.15.65    ts4.test.com    ts4
172.31.15.197   ts5.test.com    ts5
</code></pre>

---------------------------------------

# 3. Change the hostname as necessary to the FQDN that you setup above
##### sudo hostnamectl set-hostname ts1.test.com
##### hostname -f
<pre><code>
[centos@ip-172-31-9-8 ~]$ hostname -f
ts1.test.com
[centos@ip-172-31-9-8 ~]$
</code></pre>

## a. Reboot the host
##### sudo shutdown -r now

---------------------------------------

# 4. Install JDK on each of the hosts (you may choose to install on just the host where you will install CM and use the Wizard later to install on the rest

##### yum list java*jdk-devel
<pre><code>
[centos@ts1 ~]$ yum list java*jdk-devel
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: mirror.kakao.com
 * epel: mirror.layeronline.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
Available Packages
java-1.6.0-openjdk-devel.x86_64        1:1.6.0.41-1.13.13.1.el7_3        base
java-1.7.0-openjdk-devel.x86_64        1:1.7.0.221-2.6.18.0.el7_6        updates
java-1.8.0-openjdk-devel.i686          1:1.8.0.212.b04-0.el7_6           updates
java-1.8.0-openjdk-devel.x86_64        1:1.8.0.212.b04-0.el7_6           updates
java-11-openjdk-devel.i686             1:11.0.3.7-0.el7_6                updates
java-11-openjdk-devel.x86_64           1:11.0.3.7-0.el7_6                updates
java-latest-openjdk-devel.x86_64       1:12.0.1.12-1.rolling.el7         epel
</code></pre>

##### sudo yum install java-1.8.0-openjdk-devel.x86_64
<pre><code>
......
libwayland-client.x86_64 0:1.15.0-1.el7
libwayland-server.x86_64 0:1.15.0-1.el7
libxcb.x86_64 0:1.13-1.el7
libxshmfence.x86_64 0:1.2-1.el7
libxslt.x86_64 0:1.1.28-5.el7
lksctp-tools.x86_64 0:1.0.17-2.el7
mesa-libEGL.x86_64 0:18.0.5-4.el7_6
mesa-libGL.x86_64 0:18.0.5-4.el7_6
mesa-libgbm.x86_64 0:18.0.5-4.el7_6
mesa-libglapi.x86_64 0:18.0.5-4.el7_6
pango.x86_64 0:1.42.4-2.el7_6
pcsc-lite-libs.x86_64 0:1.8.8-8.el7
pixman.x86_64 0:0.34.0-1.el7
python-javapackages.noarch 0:3.4.1-11.el7
python-lxml.x86_64 0:3.2.1-4.el7
ttmkfdir.x86_64 0:3.0.9-42.el7
tzdata-java.noarch 0:2019a-1.el7
xorg-x11-font-utils.x86_64 1:7.5-21.el7
xorg-x11-fonts-Type1.noarch 0:7.5-9.el7

Complete!
</code></pre>

##### rpm -qa java*jdk-devel
<pre><code>
[centos@ts1 ~]$ rpm -qa java*jdk-devel
java-1.8.0-openjdk-devel-1.8.0.212.b04-0.el7_6.x86_64
</code></pre>

##### javac -version
<pre><code>
[centos@ts1 ~]$ javac -version
javac 1.8.0_212
</code></pre>

---------------------------------------

# 5. On the host that you will install CM:
## a. Configure the repository for CM 5.15.2

##### sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
<pre><code>
[centos@ts1 ~]$ sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-clouderac
curl: (22) The requested URL returned error: 404 Not Found
error: https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-clouderac: import read failed(2).
[centos@ts1 ~]$ sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
--2019-05-21 10:09:16--  https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo
Resolving archive.cloudera.com (archive.cloudera.com)... 151.101.108.167
Connecting to archive.cloudera.com (archive.cloudera.com)|151.101.108.167|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 290 [binary/octet-stream]
Saving to: ‘/etc/yum.repos.d/cloudera-manager.repo’

100%[======================================>] 290         --.-K/s   in 0s

2019-05-21 10:09:16 (96.0 MB/s) - ‘/etc/yum.repos.d/cloudera-manager.repo’ saved [290/290]
</code></pre>

##### sudo vi /etc/yum.repos.d/cloudera-manager.repo
<pre><code>
baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.14.4/
</code></pre>

##### sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera

## b. Install CM
##### sudo yum install -y cloudera-manager-daemons cloudera-manager-server
<pre><code>
......
Total download size: 748 M
Installed size: 929 M
Downloading packages:
(1/2): cloudera-manager-server-5.14.4-1.cm5144.p0.3.el7.x8 | 8.5 kB   00:00
(2/2): cloudera-manager-daemons-5.14.4-1.cm5144.p0.3.el7.x | 748 MB   00:17
--------------------------------------------------------------------------------
Total                                               43 MB/s | 748 MB  00:17
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : cloudera-manager-daemons-5.14.4-1.cm5144.p0.3.el7.x86_64     1/2
  Installing : cloudera-manager-server-5.14.4-1.cm5144.p0.3.el7.x86_64      2/2
  Verifying  : cloudera-manager-daemons-5.14.4-1.cm5144.p0.3.el7.x86_64     1/2
  Verifying  : cloudera-manager-server-5.14.4-1.cm5144.p0.3.el7.x86_64      2/2

Installed:
  cloudera-manager-daemons.x86_64 0:5.14.4-1.cm5144.p0.3.el7
  cloudera-manager-server.x86_64 0:5.14.4-1.cm5144.p0.3.el7

Complete!
</code></pre>


## c. Install and enable Maria DB (or a DB of your choice)
##### sudo yum install -y mariadb-server
<pre><code>
......
Verifying  : 1:mariadb-5.5.60-1.el7_5.x86_64                             8/11
Verifying  : perl-DBI-1.627-4.el7.x86_64                                 9/11
Verifying  : libaio-0.3.109-13.el7.x86_64                               10/11
Verifying  : perl-PlRPC-0.2020-14.el7.noarch                            11/11

Installed:
mariadb-server.x86_64 1:5.5.60-1.el7_5

Dependency Installed:
libaio.x86_64 0:0.3.109-13.el7
mariadb.x86_64 1:5.5.60-1.el7_5
perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7
perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7
perl-DBD-MySQL.x86_64 0:4.023-6.el7
perl-DBI.x86_64 0:1.627-4.el7
perl-Data-Dumper.x86_64 0:2.145-3.el7
perl-IO-Compress.noarch 0:2.061-2.el7
perl-Net-Daemon.noarch 0:0.48-5.el7
perl-PlRPC.noarch 0:0.2020-14.el7

Complete!
</code></pre>

##### sudo systemctl stop mariadb

##### sudo vi /etc/my.cnf
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


### i. Don’t forget to secure your DB installation
##### sudo rpm --import https://yum.mariadb.org/RPM-GPG-KEY-MariaDB

## d. Install the mysql connector or mariadb connector

##### sudo wget "https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz"
<pre><code>
....
-connector-java-5.1.46.tar.gz"
--2019-05-21 10:25:37--  https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
Resolving dev.mysql.com (dev.mysql.com)... 137.254.60.11
Connecting to dev.mysql.com (dev.mysql.com)|137.254.60.11|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz [following]
--2019-05-21 10:25:38--  https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
Resolving cdn.mysql.com (cdn.mysql.com)... 23.212.13.170
Connecting to cdn.mysql.com (cdn.mysql.com)|23.212.13.170|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4434926 (4.2M) [application/x-tar-gz]
Saving to: ‘mysql-connector-java-5.1.46.tar.gz’

100%[======================================>] 4,434,926   --.-K/s   in 0.04s

2019-05-21 10:25:38 (111 MB/s) - ‘mysql-connector-java-5.1.46.tar.gz’ saved [4434926/4434926]
</code></pre>

##### tar zxvf mysql-connector-java-5.1.46.tar.gz
<pre><code>
....
mysql-connector-java-5.1.46/src/testsuite/simple/jdbc42/ConnectionTest.java
mysql-connector-java-5.1.46/src/testsuite/simple/jdbc42/ResultSetTest.java
mysql-connector-java-5.1.46/src/testsuite/simple/jdbc42/StatementsTest.java
mysql-connector-java-5.1.46/src/testsuite/simple/tb2-data.txt.gz
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/ca-cert.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/ca-key.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/ca-truststore
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/certs_howto.txt
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/client-cert.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/client-key.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/client-keystore
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/mykey.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/mykey.pub
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/server-cert.pem
mysql-connector-java-5.1.46/src/testsuite/ssl-test-certs/server-key.pem
</code></pre>

##### sudo mkdir -p /usr/share/java/

##### cd mysql-connector-java-5.1.46

##### sudo cp mysql-connector-java-5.1.46-bin.jar /usr/share/java/mysql-connector-java.jar

## e. Create the necessary users and dbs in your database
### i. Grant them the necessary rights
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

exit;

</code></pre>

## f. Setup the CM database
##### sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm
<pre><code>
[centos@ts1 ~]$ sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm
Enter SCM password:
JAVA_HOME=/usr/lib/jvm/java-openjdk
Verifying that we can write to /etc/cloudera-scm-server
Creating SCM configuration file in /etc/cloudera-scm-server
Executing:  /usr/lib/jvm/java-openjdk/bin/java -cp /usr/share/java/mysql-connector-java.jar:/usr/share/java/oracle-connector-java.jar:/usr/share/cmf/schema/../lib/* com.cloudera.enterprise.dbutil.DbCommandExecutor /etc/cloudera-scm-server/db.properties com.cloudera.cmf.db.
[                          main] DbCommandExecutor              INFO  Successfully connected to database.
All done, your SCM database is configured correctly!
</code></pre>

## g. Start the CM server and prepare to install the cluster through the CM GUI installation process
##### sudo systemctl start cloudera-scm-server
##### sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log

<pre><code>
.....
2019-05-21 10:39:35,072 INFO SearchRepositoryManager-0:com.cloudera.server.web.cmf.search.components.SearchRepositoryManager: Num entities:209
2019-05-21 10:39:35,072 INFO SearchRepositoryManager-0:com.cloudera.server.web.cmf.search.components.SearchRepositoryManager: Generating documents:2019-05-21T01:39:35.072Z
2019-05-21 10:39:35,094 INFO SearchRepositoryManager-0:com.cloudera.server.web.cmf.search.components.SearchRepositoryManager: Num docs:222
2019-05-21 10:39:35,094 INFO SearchRepositoryManager-0:com.cloudera.server.web.cmf.search.components.SearchRepositoryManager: Constructing repo:2019-05-21T01:39:35.094Z
2019-05-21 10:39:35,197 INFO WebServerImpl:com.cloudera.server.web.cmf.AggregatorController: AggregateSummaryScheduler started.
2019-05-21 10:39:35,687 INFO SearchRepositoryManager-0:com.cloudera.server.web.cmf.search.components.SearchRepositoryManager: Finished constructing repo:2019-05-21T01:39:35.687Z
2019-05-21 10:39:35,688 INFO WebServerImpl:org.mortbay.log: jetty-6.1.26.cloudera.4
2019-05-21 10:39:35,690 INFO WebServerImpl:org.mortbay.log: Started SelectChannelConnector@0.0.0.0:7180
2019-05-21 10:39:35,690 INFO WebServerImpl:com.cloudera.server.cmf.WebServerImpl: Started Jetty server.
2019-05-21 10:39:40,531 INFO ScmActive-0:com.cloudera.server.cmf.components.ScmActive: ScmActive completed successfully.
</code></pre>



## 이후 capture file

![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/1.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/2.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/3.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/4.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/5.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/6.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/7.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/8.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/9.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/10.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/11_1.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/11_2.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/12_1.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/12_2.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/13.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/14.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/15_1.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/15_2.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/15_3.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/16.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/16_2.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/17_1.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/17_2.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/18_1.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/19.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/20.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/21.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/22.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/23.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/24.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/25.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/26.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/27.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/28.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/29.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/30.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/31.PNG?raw=true)

## install kafka

![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/32.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/33.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/34.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/35.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/36.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/37.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/38.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/39.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/40.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/41.PNG?raw=true)
![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/42.PNG?raw=true)

## analysis excercise

### Create user “training” with password “training” and add to group wheel for sudo access.
<pre><code>
[centos@ts1 etc]$ sudo useradd  training -G wheel

[centos@ts1 etc]$ sudo passwd training
Changing password for user training.
New password:
BAD PASSWORD: The password contains the user name in some form
Retype new password:
passwd: all authentication tokens updated successfully.

[centos@ts1 etc]$ grep training /etc/passwd
training:x:1001:1001::/home/training:/bin/bash

[centos@ts1 etc]$ grep training /etc/group
wheel:x:10:centos,training
training:x:1001:
</code></pre>

### Go to training_material/devsh/scripts and review the setup.sh

<pre><code>
[centos@ts1 etc]$ sudo useradd  training -G wheel

[centos@ts1 etc]$ sudo passwd training
Changing password for user training.
New password:
BAD PASSWORD: The password contains the user name in some form
Retype new password:
passwd: all authentication tokens updated successfully.

[centos@ts1 etc]$ grep training /etc/passwd
training:x:1001:1001::/home/training:/bin/bash

[centos@ts1 etc]$ grep training /etc/group
wheel:x:10:centos,training
training:x:1001:
</code></pre>

##### mysql -u root -p 
##### grant all privileges on *.* to training@'%' identified by 'training';
##### exit;
##### ./setup.sh

<pre><code>
[training@ts1 scripts]$ ./setup.sh
[setup.sh] Course setup started at: Tue May 21 14:21:04 KST 2019
[setup.sh] Setting up course environment variables
[setup.sh] Setting up the loudacre database in MySQL
ERROR 1045 (28000): Access denied for user 'training'@'localhost' (using password: YES)
ERROR 1045 (28000): Access denied for user 'training'@'localhost' (using password: YES)
ERROR 1045 (28000): Access denied for user 'training'@'localhost' (using password: YES)
[setup.sh] Setting up iPython notebook/Jupyter
[setup.sh] Setting up Hive/Impala table for demo/exercises
Starting Impala Shell without Kerberos authentication
Error connecting: TTransportException, Could not connect to ts1.test.com:21000
Not connected to Impala, could not execute queries.
[setup.sh] Course setup completed at: Tue May 21 14:21:06 KST 2019
</code></pre>

![Screenshot](https://github.com/recrom/data_ingest_exercise/blob/master/img/43.PNG?raw=true)


## Sqoop
##### sqoop import --connect jdbc:mysql://172.31.9.8:3306/loudacre --username training --password training --table basestations --target-dir /user/hive/warehouse/basestations

##### sqoop import --connect jdbc:mysql://172.31.9.8:3306/loudacre --username training --password training --table device --target-dir /user/training/loudacre/device

##### sqoop import --connect jdbc:mysql://172.31.9.8:3306/loudacre --username training --password training --table customerservicerep --target-dir /user/training/loudacre/customerservicerep_hive  --hive-import

<pre><code>
create external table device (
device_num string,
release_dt string,
device_name string,
device_type string)
row format delimited 
fields terminated by ','
location 'hdfs://ts2.test.com:8020/user/training/loudacre/device'
;
</code></pre>
