---
layout:    post
title:     "2021.02 GrowthLog"
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



## 02.05
#### Memory

##### HugePage
컴퓨팅에서 가상 메모리는 메모리 관리 기술이다. 스토리지 자원을 활용해서 가상의 큰 메모리를 사용하는 것처럼 바꾼다. OS에서는 프로그램에서 사용해야하는 가상 주소(Virtual Address)를 실제 물리 주소(Physical address)와 맵핑하는 작업을 대신 해준다.  

##### Paging
메모리 관리 기술 중에 하나이다.  운영체제는 페이지라는 동일크기의 블록을 보조 저장소에서 데이터를 페이징은 프로그램이 사용 가능한 실제 메모리의 크기를 초과하도록 보조 저장소를 사용한다. 

프로세스는 자신만의 가상 주소 공간을 가지고 있다.
32bit/64bit -> 최대 4GB/16EB의 주소 공간을 가진다.

특정 프로세스 내에서 쓰레드가 수행될 때 해당 쓰레드는 프로세스가 소유하고 있는 메모리에 대해서만 접근 가능
A 프로세세스가 0x12345678 주소에 무언가를 저장하였지만  B 프로세스도 0x12345678에 저장 될 수 있ㅇ음

Logical memory > Physical Memory를 가능하게 하는 것이 Virtual Address, Viru

- Demanding-Paging 기법

디스크 공간을 메모리처럼 활용할 수 있음
디스크 상에 존재하는 이러한 파일을 Paging file (swap file)이라고 하며
모든 프로세세스가 사용할 수 있는 가상 메모리로 사용 된다.

- Hard swap

페이징 파일에서 실제 물리 메모리로 올리고 내리리고 하는 일련의 


- Page
> 가상메모리를 사용하는 최소 크기 단위
> 윈도우 운영체제 4096(4KB)의 페이지 크기를 사용한다.

페이징 파일에서 물리 메모리로 데이터를 로드 할 때, 아무 위치에 각자가 필요한 용량 만큼 올린다면
메모리 공간에 아무도 쓸 수 없는  짜투리 공간이 발생하게 된다. 이를 막기 위해서 Page라는 최소 크기 단위를 만듬

- Demanding Page
실제로 필요한 Page만 물리메모리로 가져오는 방식
필요 Page에 접근하기 위해서 가상 메모리 주소에 대응하는 물리 메모리 주소를 찾아내야함. 




[Linux Kernel 5 - Virtual Memory & Paging](https://pr0gr4m.tistory.com/entry/Linux-Kernel-5-Virtual-Memory-Paging)


---

## 02.08

#### CPU Metric 항목 분석
**항목**
- cores
- idle
- iowait
- irq
- nice
- softirq
- steal
- system
- total
- user

**cores**
-  `system.cpu.cores`  
- 해당 서버의 Core 갯수

**idle**

- `system.cpu.idle.norm.pct`  
- `system.cpu.idle.pct`  
- `system.cpu.idle.ticks`  

CPU가 모든 일을 끝내고 쉬고 있는 시간을 의미한다. 
I/O wait 또는 Steal 등의 값으로 인해서 USage와의 관계가 일치하지는 않다.

**iowait**

- `system.cpu.iowait.norm.pct`
- `system.cpu.iowait.pct`
- `system.cpu.iowait.ticks`

CPU가 입출력을 대기하는데 사용하는 시간의 비율, 프로세스에 바로 접근 할 수 없는 상황의 경우 


**irq**

- `system.cpu.irq.norm.pct`
- `system.cpu.irq.pct`
- `system.cpu.irq.ticks`

Kernel 레벨에서 interrupt request가 얼마나 있는 지 .
컴퓨터의 주변기기와 데이터와 신호를 주고 받으며 보드에서 일어나는 모든 일을 관장한다. 
주변기기들이 어떠한 일을 하게 되면 주변 기기는 CPU에게 이러한 사실을 알려주어야 한다. 
주변기기가 CPU에게 어떤 사실을 알리면 CPU는 하던 일을 멈추고 

irq 값이 크다면 네트워크나 드라이브 같은 하드웨어
인터럽트는 이벤트가 
인터럽트는 이벤트가 발생했음을 커널(운영체제)에 알리는 신호로 CPU에서 실행하는 명령 순서가 변경된다.
하드웨어 인터럽트 - 키보드 키 누르기, 마우스의 움직임, 시스템 진행과 같은 하드웨어에서 발생하는 이벤트
소프트웨어 인터럽트 - 운영체제의 특정 시스템에 대한 응용 프로글맹으 ㅣ오청 또는 해당 프로글팸의 종료 -> softirq

와탭 사례 : CPU가 인터럽트 요청을 처리한 시간의 비율 
IRQ 값이 크다면 네트워크나 드라이브 같은 하드웨어를 처리하는데 들어가는 시간이 크다. 
CPU에 영향을 주는 하드웨어를 수정하거나 시스템을 업그레이드 해야함


**softirq**

- `system.cpu.softirq.norm.pct`
- `system.cpu.softirq.pct`
- `system.cpu.softirq.ticks`


**Interrupt**
프로그램 실행하는 도중에 예기치 않은 상황이 발생한 경우 현재 실행중인 작업을 즉시 중단하고 발생된 상황을 우선 처리한 후 
실행중이던 작업으로 복귀하여 계속 처리하는 것을 말한다. 외부, 내부, 소프트웨어 인터럽트로 구분한다.

외부 인터럽트  
- 전원 이상 인터럽트 : 정전 또는 전원 이상 시 발생
- 기계 착오 인터럽트 : CPU의 기능적인 오류 동작으로 발생
- 외부 신호 인터럽트 : 타이머에 의해 규정된 시간을 알리는 경우, 키보드로 키를 누른 경우, 외부 장치로 부터 인터럽트 요청이 있는 경우
- 입출력 인터럽트 : 데이터의 오류나 이상 현상이 발생한 경우, 입출력 장치가 데이터의 전송을 요구하거나 전송이 끝났음을 알릴 경우

내부 인터럽트
- 잘못된 명령이나 데이터를 사용할 때 Trap 이라고
- 0으루 나누기, overflow ,underflow 등, 명령어 잘못사용한 경우, 부당한 기억장소의 참조와 같은 프로그램 상으 ㅣ오류

소프트웨어 인터럽트`
- 프로그램 처리중에 명령으 ㅣ요청에 의해 발생한 것으로 가장 대표적인 것 SVC 인터럽트
- SVC 인터럽트 : 사용자가 SVC 명령을 써서 의도적으로 호출한 경우, 복잡한 입출력 처리를 해야하는 경우, 기억장치 할당 및 오퍼레이터오 ㅏ데이화를 하는 경우 발생


IRQ(Interrupt ReQuest)
IRQ가 높다는 것은 인터럽트 발생 건수가 많다는 것?

**nice**

- `system.cpu.nice.norm.pct`
- `system.cpu.nice.pct`
- `system.cpu.nice.ticks`

TOP에서 보이는 nice의 의미
CPU 스케쥴링 우선순위 
-20 ~ + 19 까지 있으며 크기가 클수록 우선순위가 낮다.
root 유저는 renice를 통해서 프로세스의 우선순위를 변경할 수 있다.
일반 유저는 우선순위를 낮추는 것만 가능하다.

CPU utilization graph 상의 nice %의 의미
양의 값의 nice(우선순위)를 가진 유저 레벨의 프로세스가 점유하고 있는 CPU 시간의 백분율 
기본적으로 현재 "사용중"인 CPU time을 말한다. 

idle time은 높으나 nice time 이 높다면 background process가 있는 것이다.

> 여기에서 time이란 시계의 시,분,초를 나타내는 것이 아니라 CPU 사이클 내에서 차지하는 시간을 나타낸다.

[What does 'nice' mean on CPU utilization graphs?](https://serverfault.com/questions/116950/what-does-nice-mean-on-cpu-utilization-graphs/527945)



**steal**

- `system.cpu.steal.norm.pct`
- `system.cpu.steal.pct`
- `system.cpu.steal.ticks`

클라우드 서비스와 물리 서버 환경의 차이에서 발생하는 대표적인 지표. CPU steal time은 가상화된 자원을 분배하는 과정에서 cpu의 자원을 얼마나 빼앗기고 있는 지 알려주는 지표. 하이퍼바이저가 다른 가상 프로세서를 서비스하는 동안 가상 CPU가 실제 CPU를 기다리는 시간을 백분율로 표시한 값. 가상 환경에서 동작하는 VM은 단일 호스트에 있는 다른 인스턴스와 리소스를 공유함. 

> 가상화 환경이 아닌 경우에는 CPU Steal Time은 아무런 의미를 갖지 않는다.

발생원인

- VM이 올라가 있는 물리장비의 자원이 처음부터 부족하거나 물리 장비의 자원은 충분하나 VM이 할당받은 CPU자원이 부족한 경우. 

장애현상

- 배치와 같은 백그라운드 작업은 오래 걸릴 뿐
- 웹 어플리케이션과 같은 실시간 처리는 성능이 감소 -> 실시간 요청 처리 실패 -> 서비스 장

해결방법

벤더사   
- 리소스의 제한 설정을 조절. 특정 서버에서 실행되는 VM 자원 사용률에 대한 설정 조절  
- 하이퍼바이저 업그레이드
- 물리 서버으 업그레이드 
- VM을 다른 물리서버로 이전  -> VM 최적 배치와 연관

고객사
- 더 좋은 인스턴스를 구매하여 사용
- 다른 VM으로 재배포 

<br/>

참조
- [CPU Steal Time의 원인과 대책](https://www.whatap.io/ko/blog/25/)



**system**

- `system.cpu.system.norm.pct`
- `system.cpu.system.pct`
- `system.cpu.system.ticks`


**total**

- `system.cpu.total.norm.pct`
- `system.cpu.total.pct`

```go
# line 112
calculateTotalPct := func() float64 {
		// IOWait time is excluded from the total as per #7627.
		idle := calculatePct(s0.Idle, s1.Idle) + calculatePct(s0.Wait, s1.Wait)
		return common.Round(float64(numCPU)-idle, common.DefaultDecimalPlacesCount)
	}
```
[Metric beats CPU source code](https://github.com/elastic/beats/blob/c0bfea48abb66eccc671a1802cd330cbbfe8fa56/libbeat/metric/system/cpu/cpu.go)


**user**


- `system.cpu.user.norm.pct`
- `system.cpu.user.pct`
- `system.cpu.user.ticks`


**참조**
[https://brunch.co.kr/@leedongins/75](https://brunch.co.kr/@leedongins/75)   
[Metric beats CPU source code](https://github.com/elastic/beats/blob/c0bfea48abb66eccc671a1802cd330cbbfe8fa56/libbeat/metric/system/cpu/cpu.go)


**Time Calculation**

[Accurate calculation of CPU usage given in percentage in Linux?](https://stackoverflow.com/questions/23367857/accurate-calculation-of-cpu-usage-given-in-percentage-in-linux)



**Metric Beats Source Code 분석**
```
// The CPU percentages are divided by given numCPU value and rounded
// using Round.
```

**normalized**
```go
func (m *Metrics) NormalizedPercentages() Percentages {
	return cpuPercentages(m.previousSample, m.currentSample, 1)
}
```

**percentage**
```go
func cpuPercentages(s0, s1 *sigar.Cpu, numCPU int) Percentages {
	if s0 == nil || s1 == nil {
		return Percentages{}
	}

	// timeDelta is the total amount of CPU time available across all CPU cores.
	timeDelta := s1.Total() - s0.Total()
	if timeDelta <= 0 {
		return Percentages{}
	}

	calculatePct := func(v0, v1 uint64) float64 {
		cpuDelta := int64(v1 - v0)
		pct := float64(cpuDelta) / float64(timeDelta)
		return common.Round(pct*float64(numCPU), common.DefaultDecimalPlacesCount)
	}

	calculateTotalPct := func() float64 {
		// IOWait time is excluded from the total as per #7627.
		idle := calculatePct(s0.Idle, s1.Idle) + calculatePct(s0.Wait, s1.Wait)
		return common.Round(float64(numCPU)-idle, common.DefaultDecimalPlacesCount)
	}

	return Percentages{
		User:    calculatePct(s0.User, s1.User),
		System:  calculatePct(s0.Sys, s1.Sys),
		Idle:    calculatePct(s0.Idle, s1.Idle),
		IOWait:  calculatePct(s0.Wait, s1.Wait),
		IRQ:     calculatePct(s0.Irq, s1.Irq),
		Nice:    calculatePct(s0.Nice, s1.Nice),
		SoftIRQ: calculatePct(s0.SoftIrq, s1.SoftIrq),
		Steal:   calculatePct(s0.Stolen, s1.Stolen),
		Total:   calculateTotalPct(),
	}
}
```


```go
// Percentages returns CPU percentage usage information for the core. The values
// range from [0, 100%].
func (m *CoreMetrics) Percentages() Percentages { return (*Metrics)(m).NormalizedPercentages() }
```