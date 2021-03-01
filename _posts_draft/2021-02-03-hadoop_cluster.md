---
layout:     post
title:      "Hadoop Cluster 구성"
subtitle:   "Hadoop - SPARK - Zeppelin"
date:       2021-02-03 15:30:00
author:     "YooJong"
header-img: "img/in-post/post-202102/post-bg-hadoop.jpg"
catalog: true
tags:
    - Hadoop
    - Spark
    - Zeppelin
    - YARN
    - Installation
    - 구축
---


# 1. Introduction
사내에서 데이터 분석을 하기 위해서 Hadoop 클러스터를 구성하였다. 클라우드 프로바이더에서 Hadoop 서비스를 제공하긴 하지만 아직까지는 그렇게 자유롭게 사용할 수가 없기도 하고 여태까지 하던 짓이 이런 구축이라 빠르게 구축을 해보았다. 

Hadoop은 설치 방법이 3가지가 있다. 
- Standalone
- Pseudo distributed
- Full distributed

이번에 설치할 때에는 오픈스택 가상머신 4대를 활용하여 Full Distributed 방식으로 설치하여 Hadoop 본연의 목적대로 사용해보고자 한다.

# 2. Hadoop Cluster 
## 2.1 서버 정보

- Ubuntu 18.04
- 8 Cores 
- 16GB RAM
- 120GB Disk

## 2.2 클러스터 정보

### 2.2.1 네트워크

<img class="shadow" src="/img/in-post/post-202102/hadoop-cluster-arch.png" width="500">

### 2.2.2 서버구성
- hadoop-cluster-1
    - Namenode, Secondary Namenode, Jobtracker, Browser, Hadoop Utility
- hadoop-cluster-2
    - Datanode, Tasktracker
- hadoop-cluster-3
    - Datanode, Tasktracker
- hadoop-cluster-4
    - Datanode, Tasktracker

### 2.2.3 소프트웨어 정보

|S/W| Verison|
|---|----|
|java| openjdk-1.8.0_275 |
|python| 3.6.9|
|Hadoop|3.2.2|
|Spark|2.4.7|
|Zeppelin|0.9|

## 3. 사전 구성

### 3.1 HDFS용 디스크 추가

데이터 유지를 위해서 오픈스택 인스턴스(root - 20GB)에 100GB 볼륨을 추가하였다. 추가한 볼륨은 파티션을 나누지 않고 `mkfs.ext4`로 포맷을 한 뒤에 `/data` 폴더에 mount 하였다.

### 3.2 hosts 등록

- 모든 Node에 Hosts를 등록한다. 

```shell
# vi /etc/hosts
60.99.77.218 hadoop-cluster-1
60.99.77.35  hadoop-cluster-2
60.99.77.236 hadoop-cluster-3
60.99.77.55  hadoop-cluster-4
```


### 3.2 Java 설치

- apt를 이용하여 설치
```shell
apt update
apt install openjdk-8-jre
apt install openjdk-8-jdk
```

- Java path 및 버전 설정

```shell
update-alternatives --config java

# There are 3 choices for the alternative java (providing /usr/bin/java).
#
#   Selection    Path                                            Priority   Status
# ------------------------------------------------------------
# * 0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode
#   1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode
#   2            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode
# 
# Press <enter> to keep the current choice[*], or type selection number

1  # 우선순위 번호 1 입력 할 것 
```

#### 3.2.2 Java 경로 등록

```shell
which java
readlink -f /usr/bin/javac
vi /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```



## 3.3 User 생성 


Hadoop은 Multi-user를 지원하며 권장한다. 각각의 유저는 자신만의 Hadoop 클러스터를 사용하는 것처럼 할 수 있으며 파일 등이 분리되어 저장되고 권한 역시 각각 다르게 설정이 될 수 있다.

```
adduser hadoop
chown -R hadoop:hadoop /data
vi /etc/sudoer
hadoop  ALL=NOPASSWD: ALL
```


## 3.4 Cluster SSH 키 등록
Master node에서 설정을 한다.

```shell
ssh-keygen 
# Enter로 넘겨서 Password없이 진행한다.
ssh-copy-id hadoop-cluster-2
ssh-copy-id hadoop-cluster-3
ssh-copy-id hadoop-cluster-4
```

- `ssh hadoop-cluster-2`를 했을 때 비밀번호없이 접속이 가능한 지 확인한다.

## 3.4 Hadoop 설치 및 설정
Hadoop은 Master에서 설정을 마친 후에 나머지 Node에 설정파일을 전달하는 방식으로 한다.


### 3.4.1 Hadoop 설치

```shell
cd /data
wget https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz
tar -zxvf hadoop-3.2.2.tar.gz
cd /data/hadoop-3.2.2
```

## 3.4.2 Hadoop 경로 설정

`/etc/profile` 설정하기
```
export  HADOOP_HOME=${PWD}/hadoop-3.2.2
bashrc 설정
Hadoop 설정
```

### 3.4.2 

### 3.4.3 sync용 쉘스크립트
```shell
```




### 3.4.3 Hadoop 클러스터 배포



# 4. Deploy




# 5. 확인





# 6. 추가 고민거리
- DataNode의 증설
- SecondaryNode 분리
- DataNode 내 HDFS용 디스크 용량 증설

