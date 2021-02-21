---
layout:     post
title:      "filesystem 모니터링 지표"
subtitle:   "for performance"
date:       2021-02-18 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Monitoring 
    - Performance
    - Server
    - Filesystem
    - Metric beats 
---
# Filesystem
## 1. 사전지식
### filesystem

여러 개의 물리적 Disk를 가지며 각 Disk는 하나 이상의 filesystem을 갖는다.
Kernel은 논리적 수준에서는 DIsk가 아니라 Filesystem을 다루며 file system
논리적 Device를 다룬다. 


File - 어떤 정보의 모임, 보통 보조기억장치(디스크)에 저장됨 
Filesystem - File들의 집합이 적절한 형태로 구성되어 있는 것

Filesystem이 나온 이융
저장 장치에 파일을 썼다 지웠다하게 되면 Fragmentaion이 발생하게 된다. 새로운 파일이 생성될 때 디스크의 빈 공간을 찾아서 쓰게 되는데 나중에는 하나의 파일이 연속적인 
디스크 공간에 있지 않게 된다. 그렇게 되면 Disk seek time이 더욱 많이 발생하게 된다. -> 새로운 알고리즘 등장

Unix에서 있어서 File은 모든 바이트들이 논리적으로 한줄로 연결된 것 처럼 인식하게 한다. 

filesystem은 기본적으로 특정 부분은 예약되어있음

### inode


## 데이터 
```json
{
 "system" : {
            "filesystem" : {
              "files" : 52428800,
              "available" : 206983532544,
              "free" : 249949982720,
              "used" : {
                "pct" : 0.7417,
                "bytes" : 594488086528
              },
              "free_files" : 49089257,
              "mount_point" : "/",
              "type" : "ext4",
              "total" : 844438069248,
              "device_name" : "/dev/sda3"
            }
          }
}
```

## 관련 리눅스 명령어
- `df -i`
```shell
$ df -i | grep sda3
Filesystem                            Inodes   IUsed     IFree IUse% Mounted on
/dev/sda3                           52428800 3339569  49089231    7% /  
```

- `df -B1`
  `B1` 1Bytes 크기로 갯수를 세겠다. 
```shell
$ df  -B1 | grep sda3
Filesystem                          1K-blocks      Used  Available Use% Mounted on
/dev/sda3                           844438069248 602468827136  199002791936  76% /
```
## 2. Metric 분석
### 2.1 항목 

| 수집항목                      | 실제데이터      |
| ----------------------------- | --------------- |
| system.filesystem.device_name | /dev/sda3       |
| system.filesystem.mount_point | /               |
| system.filesystem.type        | ext4            |
| system.filesystem.total       | 844,438,069,248 |
| system.filesystem.used.bytes  | 594,488,086,528 |
| system.filesystem.free        | 249,949,982,720 |
| system.filesystem.available   | 206,983,532,544 |
| system.filesystem.used.pct    | 0.7417          |
| system.filesystem.files       | 52,428,800      |
| system.filesystem.free_files  | 49,089,257      |


#### filesystem 기본 정보
system.filesystem.device_name
system.filesystem.mount_point
system.filesystem.type
- 파일시스템 종류
- sysfs, proc, udev, devpts, tmpfs, cgroup, pstore, nsfs, 등등 
- 주 관심은 `/dev/*`

#### filesystem 디스크 공간 정보
system.filesystem.total
system.filesystem.used.bytes
system.filesystem.free
system.filesystem.available
system.filesystem.used.pct

`total = free + used`
`free = available + root reserved(수집 x)`

available과 free의 차이 
- ext 유형의 filesystem은 disk에서 root 유저가 사전에 예약한 공간이 있다. 대게 5% 정도 사용된다.  

#### filesystem inode 정보
system.filesystem.files
system.filesystem.free_files


## 분석 결과
1. type으로 device에 관해서만 필터링을 해야함.
2. pct는 수집하지 않아도 된다.
3. inode가 꽉 차게되는 경우 문제가 발생하므로 필요하다.

## 참조
[free disk vs available disk](https://serverfault.com/questions/228514/free-disk-vs-available-disk)
