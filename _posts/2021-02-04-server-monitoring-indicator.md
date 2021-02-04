---
layout:     post
title:      "서버 모니터링 중요 지표"
subtitle:   "for performance"
date:       2021-02-04 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Monitoring 
    - Performance
    - Server
---

# 중요 Indicator


Metric Beats를 통해서




Metric Beats System Module 중 관련 Section 정리

1. 예측
    - 무엇을 예측할 것인가?
    - 무의미한 그래프를 예측 데이터를 보여주는 것이 아님
    - 종합적인 점수에 대한 예측이 될 수도 있음
    - 
    - CPU 사용량, Memory 사용량, Disk 사용량, 네트워크 사용량, Process


2. 최적 배치
    - Host
        - CPU
            - CPU Utilization
            -
        - Memory 
            - Memeory 
    - Virtual Machine
        - CPU
        - Memory 


3. 최적 리소스 추천
    - 
    - CPU
        - Load
    - Memory
    - Disk


4. 이상진단
    - CPU
    - Memory
    - Process
    - Disk
    - Network




1. Uptime
2. Disk
    - Disk busy time
    - IOPs
    - Disk read/write time
    - Disk queue length
    - Split IOPs

    참조
    - [Disk I/O 모니터링을 위한 iostat 명령어 활용법](https://m.blog.naver.com/bumsukoh/221022044759)
    - [리눅스 I/O 트러블 슈팅(I/O Trouble Shooting)](https://m.blog.naver.com/skddms/221606572303)

3. Page File Usage
    메모리와 연관되어 있음 

4. Context Switch
    - CPU

5. Process Activity
    - Process 수를 카운트 해야한다.
    - 계속해서 늘어나면 문제가 있는 것이다.

6. Network Traffic
    - 


7. TCP Activity


- 예측
    1. CPU 사용량 예측
    2. Memory 사용량 예측
    3. Network 사용량 예측
    4. Process 갯수 예측
    5. Disk IO 예측

- 최적 리소스 기반
    1. CPU 




# 참조
