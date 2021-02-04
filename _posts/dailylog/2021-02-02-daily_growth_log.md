---
layout:     post
title:      "2021.02 GrowthLog"
subtitle:   ""
date:       2021-01-28 15:30:00
author:     "YooJong"
header-img: "img/post-bg-infinity.jpg"
catalog: true
tags:
    - 2021 
    - GrowthLog
---
# 2021.02
## 02.01
- Hadoop 클러스터 구축 
    - 인스턴스 4대 
    - Hadoop 
        - core-site.xml
        - yarn-site.xml
        - mapreduce.xml
        - hdfs-site.xml

    - Spark 설치

    - Spark와 Hadoop을 Yarn으로 엮고 있다.
    - YARN 설정과 Spark 설정을 엮어야 되는데 잘 되지 않는다.

## 02.02
- Hadoop 클러스터 구축
- YARN 설정

- Container 내 JAVA_HOME PATH 설정 오류
- Log 메세지

```
[2021-02-02 02:20:46.014]Container exited with a non-zero exit code 127. Error file: prelaunch.err.
Last 4096 bytes of prelaunch.err :
Last 4096 bytes of stderr :
/bin/bash: /bin/java: No such file or directory
```

- 해결방법
`hadoop-env.sh`파일 수정  

```shell
# JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -> 주석처리

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
```



- Spark Job 테스트  
Hadoop - Spark 연동을 확인하기 위해서 간단한 example spark job을 submit 한다.

``` shell
spark-submit –class org.apache.spark.examples.SparkPi \
–master yarn \
–deploy-mode cluster \
–driver-memory 4g \
–executor-memory 2g \
–executor-cores 1 \
${SPARK_HOME}/examples/jars/spark-examples*.jar

spark-submit --class org.apache.spark.examples.SparkPi \
--master yarn-client \
--num-executors 1 \
--driver-memory 512m \
--executor-memory 512m \
--executor-cores 1 \
${SPARK_HOME}/examples/jars/spark-examples*.jar 10
```

- SPARK java 경로 error
`spark-env.sh` 에서 JAVA_HOME을 절대경로로 변경한다.

```shell
# spark-env.sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

- SPARK Session

```java
org.apache.hadoop.ipc.RemoteException(java.io.IOException): File /user/hadoop/.sparkStaging/application_1612247239787_0001/__spark_libs__675299795277147897.zip could only be written to 0 of the 1 minReplication nodes. There are 3 datanode(s) running and 3 node(s) are excluded in this operation.
```


1. hdfs namenode -format
2. /tmp/hadoop-hadoop/data를 지움

```
sudo rm -R /tmp/*
```

3. hdfs datanode -format
4. 모든 데이터 노드 내 data 폴더 내 파일 제거(최후의 방법)

```
rm -rf /data/hd-cluster/data/*
```

        
- Dynamic Allocation 설정 에러

```java
Exception in thread "main" org.apache.spark.SparkException: Dynamic allocation of executors requires the external shuffle service. You may enable this through spark.shuffle.service.enable
```

`spark-defaults.conf`

```
spark.shuffle.service.enable true
```


- Zeppelin 설치
    - PySpark를 할 때에 Worker에 python 설정이 되어 있어야 한다.
    - python 설정

```shell
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
sudo update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1
```
    
**TODO**
- [ ] Ambari 설치  
- [ ] Hue 설치 
- [ ] [Future] 사내 Nas, Amazon S3 HDFS 연결 



## 02.03

- Elastic Search - Hadoop - Spark 연동 구성 정리
- Hadoop Cluster Monitoring 
    - Ambari 외 다른 대안을 찾아야할 듯

- Server Performance Key indicator
    - Disk
    - Network
    - file 

    - references
    > https://www.eginnovations.com/blog/server-performance-monitoring/
    > https://www.monitis.com/blog/essential-server-performance-metrics-you-should-know-but-were-reluctant-to-ask/
    > https://raygun.com/blog/server-performance-metrics/


## 02.04
- Server Performance Monitoring 지표
- PySpark 연결
- Elastic Search 인덱스 설계 방향
- Metric Beats에서 수집하고 있는 데이터에 대한 분석이 필요함.  

<br/>  

- 참고 자료
[CPU 프로파일링 /proc/stat python 프로그램](https://jeongchul.tistory.com/613)
[Disk I/O 모니터링을 위한 iostat 명령어 활용법](https://m.blog.naver.com/bumsukoh/221022044759)
[리눅스 I/O 트러블 슈팅(I/O Trouble Shooting)](https://m.blog.naver.com/skddms/221606572303)

