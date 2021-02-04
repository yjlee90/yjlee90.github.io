---
layout:     post
title:      "Hadoop Cluster 구성"
subtitle:   "Hadoop - SPARK - Zeppelin"
date:       2021-02-03 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Hadoop
    - Spark
    - Zeppelin
    - YARN
    - Installation
    - 구축
---


Java 8설치

Java_home 설정

which java

readlink -f /usr/bin/javac

vi ~/.basrhc

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

HDFS 용 디스크 추가

/data mount

Hadoop 유저 생성

Hadoop 설치

https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz

Hadoop Home 설정

export  HADOOP_HOME=${PWD}/hadoop-3.2.2

bashrc 설정

Hadoop 설정

# 1. 개요

Hadoop 설치 방법 3가지
- Standalone
- Pseudo distributed
- Full distributed

적용 방법
- 오픈스택 위 가상머신 4대 Full Distributed

# 2. Hadoop Cluster구성
## 2.1 서버 정보

- Ubuntu 18.04
- 8 Cores 
- 16GB RAM
- 120GB Disk

## 2.2 클러스터 정보

### 2.2.1 네트워크

### 2.2.2 서버구성
- hadoop-cluster-1
    - Namenode, Secondary Namenode, Jobtracker, Browser, Hadoop Utility
- hadoop-cluster-2
    - Datanode, Tasktracker
- hadoop-cluster-3
    - Datanode, Tasktracker
- hadoop-cluster-4
    - Datanode, Tasktracker

### 2.2.3 프로그램 정보

- Java 8
- Hadoop
- Spark
- Zeppelin

## 3. 사전 구성

### 3.1 HDFS용 디스크 추가

데이터 유지를 위해서 오픈스택 인스턴스(root - 20GB)에 100GB 볼륨을 추가하였다. 오픈스택 인스턴스에 100GB 볼륨을 추가하였음. 현재에는 LVM 구성을 하지 않았음. 용량이 부족하게 될 경우에는 Datanode를 추가하는 

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

## 3.3 User 생성 


Hadoop은 Multi-user를 지원하며 권장한다. 각각의 유저는 자신만의 Hadoop 클러스터를 사용하는 것처럼 할 수 있으며 파일 등이 분리되어 저장되고 권한 역시 각각 다르게 설정이 될 수 있다.

adduser hadoop

## 3.4 Hadoop 설치



## 3.5 Java, Hadoop 경로 설정



## 3.5 Hadoop 설정 변경



# 4. Deploy

# 5. 확인


# 6. 추가 고민거리
- DataNode의 증설
- SecondaryNode 분리
- DataNode 내 HDFS용 디스크 용량 증설

