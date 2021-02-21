---
layout:     post
title:      "Memory 모니터링 지표"
subtitle:   "for performance"
date:       2021-02-04 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Monitoring 
    - Performance
    - Server
    - Memory
---

# 1. 항목
## 1.1 Metric


```json
{
    "@timestamp": "2017-10-12T08:05:34.853Z",
    "event": {
        "dataset": "system.memory",
        "duration": 115000,
        "module": "system"
    },
    "metricset": {
        "name": "memory",
        "period": 10000
    },
    "service": {
        "type": "system"
    },
    "system": {
        "memory": {
            "actual": {
                "free": 8461623296,
                "used": {
                    "bytes": 7159164928,
                    "pct": 0.4583
                }
            },
            "free": 1299234816,
            "hugepages": {
                "default_size": 2097152,
                "free": 0,
                "reserved": 0,
                "surplus": 0,
                "swap": {
                    "out": {
                        "fallback": 0,
                        "pages": 0
                    }
                },
                "total": 0,
                "used": {
                    "bytes": 0,
                    "pct": 0
                }
            },
            "page_stats": {
                "direct_efficiency": {
                    "pct": 0.9242
                },
                "kswapd_efficiency": {
                    "pct": 0.7518
                },
                "pgfree": {
                    "pages": 15924304810
                },
                "pgscan_direct": {
                    "pages": 1185751
                },
                "pgscan_kswapd": {
                    "pages": 50008148
                },
                "pgsteal_direct": {
                    "pages": 1095884
                },
                "pgsteal_kswapd": {
                    "pages": 37594071
                }
            },
            "swap": {
                "free": 7823421440,
                "in": {
                    "pages": 2702
                },
                "out": {
                    "pages": 23582
                },
                "readahead": {
                    "cached": 554,
                    "pages": 986
                },
                "total": 7897870336,
                "used": {
                    "bytes": 74448896,
                    "pct": 0.0094
                }
            },
            "total": 15620788224,
            "used": {
                "bytes": 14321553408,
                "pct": 0.9168
            }
        }
    }
}
```



| 섹션       | 메트릭                | 실제값 예시 |
| ---------- | --------------------- | ----------- |
| actual     | free                  | 8461623296  |
| actual     | used.bytes            | 7159164928  |
| actual     | used.pct              | 0.4583      |
| free       |                       | 1299234816  |
| hugepages  | default_size          | 2097152     |
| hugepages  | free                  | 0           |
| hugepages  | reserved              | 0           |
| hugepages  | surplus               | 0           |
| hugepages  | swap.out.fallback     | 0           |
| hugepages  | swap.pages            | 0           |
| hugepages  | total                 | 0           |
| hugepages  | used.bytes            | 0           |
| hugepages  | used.pct              | 0           |
| page_stats | direct_efficiency.pct | 0.9242      |
| page_stats | kswapd_efficiency.pct | 0.7518      |
| page_stats | pgfree.pages          | 15924304810 |
| page_stats | pgscan_direct.pages   | 1185751     |
| page_stats | pgscan_kswapd.pages   | 50008148    |
| page_stats | pgsteal_direct.pages  | 1095884     |
| page_stats | pgsteal_kswapd.pages  | 37594071    |
| swap       | free                  | 7823421440  |
| swap       | in.pages              | 2702        |
| swap       | out.pages             | 23582       |
| swap       | readahead.cached      | 554         |
| swap       | readahead.pages       | 986         |
| swap       | used.bytes            | 74448896    |
| swap       | used.pct              | 0.0094      |
| swap       | total                 | 7897870336  |
| used       | bytes                 | 1432155340  |
| used       | pct                   | 0.9168      |
| total      |                       | 15620788224 |


pgfree : number of pages scanned by the kswapd daemon
pgscand
pgfree : 가용 리스트에 추가된 페이지 (횟수/s)
pgsteal
pgscand : 직접 페이지 스캣

kswapd - 백그라운드 페이지 아웃 데몬 
pgscan_kwapd = number of pages scanned by kswapd daemon
pgscan_direct = number of pages scanned directly
pgfree : number of pages freed by the system
pgsteal : number of pages placed on the free list by system



pgsteal : 페이지와 스왑 캐시 재사용



# 값 분석
total = actual.free + acutal.used.bytes
swap.total = swap.free + swap.used.bytes

# pages
외부 단편화
내부 단편화

Paging
프로세스를 작은 크기로 나눠서 외부 단편화를 해결

프로세스를 나눈 조각을 Page
물리 메모리를 나눈 조각을 Frame

프로세스는 Page의 집합이고
메모리는 Frame의 집합이다. 
프로세스를 정상적으로 사용하기 위해서는 MMU의 재배치 레지스터를 
여러개 사용해서 각 페이지의 실제 주소로 변경해준다. 
이러한 여러 개의 재배치 레지스터를 Page Table 이라고 한다.

가상 메모리 주소를 물리 메모리 주소로 변환 (Address Translation)

내부 단편화
-> 프로세스의 크기가 페이지 크기의 배수가 아닐 수 있음.

가상메모리 
- 물리 메모리 크기의 한계를 극복하기 위함
- 실행에 필요한 부분만 메모리에 동적할 당하는 것
- 프로세스의 일부분은 페이지 단위일 수 도 있고, 세그먼트 단위일 수도 있지만 
- 이처럼 요구 되어지는 페이지만 올리는 것이 demanding paging이라고 한다.
- 대부분 요구 페이징을 사용하므로 가상메모리와 요구 페이징을 같은 용어로 사용하는 경우 가많다.

Page fault
- CPU가 접근하려는 페이지가 메모리에 없는 경우
- 즉, 페이지 테이블의 valid bit가 0인 경우
- valid bit 확인 -> 0인 경우 인터럽트 신호를 보내 운영체제 내부 ISR로 점 -> ISR에서 디스크를 탐색하여 해당 프로세스의 페이지를 찾음 -> 해당 페이지를 비어있는 프레임에 할당 -> 페이지 테이블 갱신 (valid bit 1로 변)경 -> 다시 명령어로 돌아가서 실행

swap과 같은 점은 디스크를 경유한다는 점. 
swap과 다른 점은 swapping은 프로세스 단위, Demanding Paging은 page 단위로 이동하는 차이

Page replacement
메모리가 꽉찬 상황에서 새로운 페이지를 적재해야하는 경우
기존의 페이지와 변경해야함. 이미 메모리에 있는 페이지 중 하나를 backing store에 보내고(page-out) 새로운 페이지를 메모리에 올린다.(page-in) 
victimg page 라고 한다.

victim page 선택 방법은 - CPU에 의해 수정되지 않는 페이지를 고른다. 
수정되지 않은 page는 backing store에 쓰기 연산을 할 필요가 없기 때문이다. 
modified bit를 추가하여 검사한다. 
그 중에서도 FIFO로 선택한다. 





페이지 크기에 따라 성능 영향
- 내부 단편화 
- Page-in, page-out 시간 
  - 하드 디스크 기준으로 헤더가 움직이는 시간(seek time)
  - 페이지가 크면 클수록 한 번의 seek time마다 큰 페이지를 읽을 수 잇으므로 페이지 부재 빈도가 줄어든다. 
  - 페이지 테이블 크기
    - 페이지 크기가 클수록 페이지 갯수가 줄어 테이블 크기도 줄어든다.
  - Memory resolution 
    - 해당 메모리에 필요한 데이터가 있을 확률
    - 페이지 크기가 작을수록 resolution을 높일 수 있음.
    - 페이즈 크기가 크면 다른 필요 없는 부분이 있을 확률이 크기 때문
  - Page fault 발생 확률
    - 페이즈 크기가 클 수록 page fault 발생 확률이 작다.
    - locality 성질과도 크다. 


프레임 할당
2.1 Thrashing
- 메모리에 
- 프로세스가 증가할수록 메인 메모리에 비어 있는 프레임 개수는 줄어들게 되고 모든 프레임이 가득 차게 된다. 그 후에도 계속 프로세스가 증가한다면 메모리와 Backing store 사이에 page in/out 작업이 발생하는데 프로세스가 많아질 수록 해당 작업이 증가하게된다. page in/out은 CPU가 작업하지 않는 작업이다. 그러므로 디스크 IO작업 많아질수록 CPU는 아무것도 하지 않는다. 

I/O 작업이 증가하여 CPU 이용률이 떨어지는 현상을 Thrashing이라고 한다. 
Thrashing을 해결 하는 방법
- 프로세스 당 충분한/적절한 수의 프레임을 할당한다. 
- GLobal replacement보다 Local replacement를 사용한다.

정적할당 / 동적할당
정적할당
- 동일 할당 : 모든 프로세스에게 똑같은 수의 프레임을 할당한다.
- 비례 할당 : 프로세스의 크기에 따라 프레임을 할당한다. 프로세스가 크더라도 모든 기능을 사용하지 않기 때문에 비효율적임


동적할당
프로세스가 실행 중일 때 어느 페이지를 사용하는 지에 실험하는 결과 Locality 성질이 성립한다는 것을 발견한다.
특정 시간에 특정 범위으 ㅣ페이지를 주로 참조한다. 이러한 성질은 캐시에서도 볼 수 있다. 특정 시간에 따라 사용하는 페이지으 ㅣ개수마큰 프레임을 할당해줄 수 있다. 

프로세스를 미리 수행해봐야 알 수 있다는 것이다. -> Working set
미래가 아닌 과거를 보는 것
Working set은 현재 시간에서 일정시간 이전동안 사용되었던 페이지으 ㅣ집합 이다. 


# hugepages

# swap



# pgsteal_kwapd



# 참조
[Linux에서 메모리를 다 써버렸을 때 일어나는 일](https://medium.com/@EJSohn/%EB%B2%88%EC%97%AD-linux%EC%97%90%EC%84%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC%EB%A5%BC-%EB%8B%A4-%EC%8D%A8%EB%B2%84%EB%A0%B8%EC%9D%84-%EB%95%8C-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EC%9D%BC-9dadba29c89c)  
- CPU Load와 IOwait, Buffer/Cache 간의 관계를 확인할 수 있음.

[[운영체제(OS)] 13. 페이징](https://velog.io/@codemcd/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9COS-13.-%ED%8E%98%EC%9D%B4%EC%A7%95)


[서비스 성능 향상을 위한 서버 부하 및 리소스 모니터링 방법](https://velog.io/@jahoy/%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%84%B1%EB%8A%A5-%ED%96%A5%EC%83%81%EC%9D%84-%EC%9C%84%ED%95%9C-%EC%84%9C%EB%B2%84-%EB%B6%80%ED%95%98-%EB%B0%8F-%EB%A6%AC%EC%86%8C%EC%8A%A4-%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81-%EB%B0%A9%EB%B2%95)