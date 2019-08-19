# 1. Create a CDH Cluster on AWS

## a. Linux setup

### Add the following linux accounts to [all nodes]

~~~
# 계정 생성
sudo useradd training
sudo usermod -u 3800 training
sudo passwd training

sudo groupadd skcc
cat /etc/passwd | grep training
sudo usermod -aG skcc training

# 계정 수도권한추가
sudo usermod -aG wheel training
~~~

![](/img/1-1.PNG)


### List the your instances by IP address and DNS name

~~~
sudo vi /etc/hosts

# 아래와 같이 내용 추가 private ip <모든 node>
172.31.33.234 util.com util
172.31.42.17 mn.com mn
172.31.44.103 dn1.com dn1
172.31.42.90 dn2.com dn2
172.31.45.39 dn3.com dn3
~~~
![](/img/1-2.PNG)

### Hostname modification for each node

~~~
# 각각의 node (노드 명은 약어 말고 full name)
sudo hostnamectl set-hostname <노드명>
예) sudo hostnamectl set-hostname util.com

# 변경된 hostname 확인
hostname
~~~
![](/img/1-3.PNG)

~~~
sudo yum install bind-utils net-tools -y

nslookup [도메인명]

~~~
![](/img/1-4.PNG)

~~~

getent hosts
~~~
![](/img/1-5.PNG)


### List the Linux release you are using

~~~
linux version 확인

[centos@ip-172-31-33-234 ~]$ grep . /etc/*release*

~~~
![](/img/1-6.PNG)


### List the file system capacity for the first node (master node)

~~~
[centos@ip-172-31-33-234 ~]$ df -Th
~~~
![](/img/1-7.PNG)

### List the command and output for yum repolist enabled

~~~
yum repolist all
~~~

![](/img/1-8.PNG)


### List the /etc/passwd entries for training and the /etc/group entries for skcc

~~~
cat /etc/passwd | grep training
cat /etc/group | grep skcc

~~~
![](/img/1-9.PNG)


### List output of the flowing commands:
~~~
getent group skcc

getent passwd training
~~~

![](/img/1-10.PNG)



## b. Install a MySQl server

### 진행한 부분
~~~
sshd_config setting for each node [all nodes!]

sudo vi /etc/ssh/sshd_config
# PasswordAuthentication -> yes 로 변경 후 저장

# sshd 재시작 및 상태확인
sudo systemctl restart sshd.service
sudo systemctl status sshd.service


sudo yum update
sudo yum install -y wget

# 전체 y 선택
~~~


### Use MariaDB as the database for all the services. You may choose your own username and passwords but make a record of it so that we may access them.

~~~
sudo yum install -y mariadb-server

sudo systemctl enable mariadb
sudo systemctl start mariadb
# mariadb 상태 확인
sudo systemctl status mariadb
~~~
![](/img/1-11.PNG)

### List the following in your GitHub
~~~
1. A command and output that shows the hostname of your database server
~~~
![](/img/1-add1.PNG)
~~~
2. A command and output that reports the database server version
~~~
![](/img/1-13.PNG)
~~~
3. A command and output that lists all the databases in the server
~~~
![](/img/1-12.PNG)

~~~
mysql -u root -p

# db, user 생성
CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON scm.* TO 'scm-user'@'%' IDENTIFIED BY 'password';

CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON amon.* TO 'amon-user'@'%' IDENTIFIED BY 'password';

CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON rman.* TO 'rman-user'@'%' IDENTIFIED BY 'password';

CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON hue.* TO 'hue-user'@'%' IDENTIFIED BY 'password';

CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON metastore.* TO 'metastore-user'@'%' IDENTIFIED BY 'password';

CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON sentry.* TO 'sentry-user'@'%' IDENTIFIED BY 'password';

CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON oozie.* TO 'oozie-user'@'%' IDENTIFIED BY 'password';

FLUSH PRIVILEGES;
~~~
![](/img/1-14.PNG)

## c. Install Cloudera Manager



~~~
[all nodes]
CDH version 5.15.2

sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

# base url 수정
sudo vi /etc/yum.repos.d/cloudera-manager.repo
baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.15.2/
~~~

![](/img/1-15.PNG)




### 기타 진행


~~~
[only util]
rpc에 key 추가
sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera

cloudera install
sudo yum install cloudera-manager-daemons cloudera-manager-server



[all nodes]
Install a supported Oracle JDK 
# 설치 가능한 jdk list 확인
sudo yum list oracle*
sudo yum install -y oracle-j2sdk1.7

[all nodes]
jdbc connector 설치 
sudo wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz
tar zxvf mysql-connector-java-5.1.47.tar.gz
sudo mkdir -p /usr/share/java/
cd mysql-connector-java-5.1.47
sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar
cd /usr/share/java/
sudo yum install -y mysql-connector-java

~~~


~~~
#  set CM DB
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm-user password
# CM server start
sudo systemctl start cloudera-scm-server
sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log


Install a cluster and deploy CDH
http://52.78.148.227:7180/
admin / admin
~~~

![](/img/1-16.PNG)
![](/img/1-17.PNG)
![](/img/1-18.PNG)
![](/img/1-19.PNG)

HDFS, YARN, ZooKeeper 먼저 설치

![](/img/1-20.PNG)
![](/img/1-21.PNG)
![](/img/1-22.PNG)
![](/img/1-23.PNG)
![](/img/1-24.PNG)





