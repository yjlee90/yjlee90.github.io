---
layout:     post
title:      "Network 모니터링 지표"
subtitle:   "for performance"
date:       2021-02-04 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Monitoring 
    - Performance
    - Server
    - Network
    - Metric beats 
---

# 1. 항목
## 1.1 Metric


```json
{
  "_index": "metricbeat-7.9.3-2021.02.11-000002",
  "_type": "_doc",
  "_id": "YM0dqXcBxU9_l6YhgnaR",
  "_version": 1,
  "_score": null,
  "_source": {
    "@timestamp": "2021-02-16T04:33:38.468Z",
    "cloud": {
      "machine": {
        "type": "t2.large"
      },
      "provider": "openstack",
      "instance": {
        "name": "demoes01-1.novalocal",
        "id": "i-00000057"
      },
      "availability_zone": "nova"
    },
    "event": {
      "dataset": "system.network",
      "module": "system",
      "duration": 283153
    },
    "metricset": {
      "name": "network",
      "period": 10000
    },
    "service": {
      "type": "system"
    },
    "system": {
      "network": {
        "name": "ens3",
        "in": {
          "errors": 0,
          "dropped": 0,
          "bytes": 1009569302583,
          "packets": 571518361
        },
        "out": {
          "bytes": 688670287509,
          "errors": 0,
          "dropped": 0,
          "packets": 173059361
        }
      }
    },
    "ecs": {
      "version": "1.5.0"
    },
    "host": {
      "name": "demoes01",
      "ip": [
        "60.99.77.156",
        "fe80::f816:3eff:fe77:2ab"
      ],
      "mac": [
        "fa:16:3e:77:02:ab"
      ],
      "hostname": "demoes01",
      "architecture": "x86_64",
      "os": {
        "name": "Ubuntu",
        "kernel": "4.15.0-91-generic",
        "codename": "bionic",
        "platform": "ubuntu",
        "version": "18.04.5 LTS (Bionic Beaver)",
        "family": "debian"
      },
      "id": "7073be0b60d5416fac3c0a636b71ec72",
      "containerized": false
    },
    "agent": {
      "version": "7.9.3",
      "hostname": "demoes01",
      "ephemeral_id": "273c71f2-961e-4fe6-b97d-b4fece7b640e",
      "id": "5f25689b-4690-4c07-ae43-c60ce81b0cdb",
      "name": "demoes01",
      "type": "metricbeat"
    }
  },
  "fields": {
    "@timestamp": [
      "2021-02-16T04:33:38.468Z"
    ]
  },
  "sort": [
    1613450018468
  ]
}}
```



bytes와 Packet의 관계

bytes-in : Amount of data received throught the interface
bytes-out : Amount of data sent through the interface

packet : a small segment of a larger message. Data sent over computer networks, is divided into packet. 
this packet recombined by the computer or device that receive them


*-error의 의미
in - 받는 중에 error가 난 횟수
out - 보내는 중에 error가 난 횟수


*-dropped의 의미
in - 들어오는 패킷 중에 드랍된 패킷
out - 바깥으로 나가는 패킷중에 드랍된o 패킷. Darwin,BSD 시스템에서는 무조건 0임 OS에서 확인할 수 없음


드랍이 발생하는 이유
- 


성능 영향

PER을 알아야한다.
Packet Error Rate
- 장애를 판단하는 기준


어떤 인터페이스를 많이 쓴다.
어떤 프로세스가 어떤 인터페이스를 이용하여 네트워크를 많이 이용하고 있다. 

 
