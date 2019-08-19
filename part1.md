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


