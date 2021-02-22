---
layout:     post
title:      "CPU 모니터링 Metric 분석"
subtitle:   ""
date:       2021-02-04 15:30:00
author:     "YooJong"
header-img: "img/post-bg-cpu.jpg"
catalog: true
tags:
    - Monitoring 
    - Performance
    - Server
    - CPU
---

서버 모니터링에서 가장 중요한 CPU Metric에 대해서 조사하고 분석한다. CPU의 동작방식과 운영체제 속에서 CPU가 어떻게 상호작용하는 지에 대해 Metric을 이용해서 CPU의 동작 방식을 이해 한다. Metric 수집 방식은 Elastic사에서 사용하는 Metric beats를 이용하여 Elastic Search 로 수집한다. 

# 사전지식

## norm, pct, ticks
**ticks**  
- 실제 계산 시에 사용되는 CPU 시간의 값

Time(s) = Ticks / CLOCK_PER_SEC = Ticks / CPU_FREQUENCIES

| CPU frequency(hz) | 1 Clock 시간(s) | CPU Ticks | CPU 시간 |
| ----------------- | --------------- | --------- | -------- |
| 100               | 0.01            | 30        | 0.3      |

**pct**
- 모든 core에 대하여 해당 메트릭의 비율(%)

**norm**
- pct 값을 core 수로 나누어서 normalized 된 값을 구함


# 분석

## 수집

### 항목

- `system.cpu.cores`
- `system.cpu.idle.norm.pct`
- `system.cpu.idle.pct`
- `system.cpu.idle.ticks`
- `system.cpu.iowait.norm.pct`
- `system.cpu.iowait.pct`
- `system.cpu.iowait.ticks`
- `system.cpu.irq.norm.pct`
- `system.cpu.irq.pct`
- `system.cpu.irq.ticks`
- `system.cpu.softirq.norm.pct`
- `system.cpu.softirq.pct`
- `system.cpu.softirq.ticks`
- `system.cpu.nice.norm.pct`
- `system.cpu.nice.pct`
- `system.cpu.nice.ticks`
- `system.cpu.steal.norm.pct`
- `system.cpu.steal.pct`
- `system.cpu.steal.ticks`
- `system.cpu.system.norm.pct`
- `system.cpu.system.pct`
- `system.cpu.system.ticks`
- `system.cpu.user.norm.pct`
- `system.cpu.user.pct`
- `system.cpu.user.ticks`
- `system.cpu.total.norm.pct`
- `system.cpu.total.pct`

### 실 데이터

```json
{
    "@timestamp": "2017-10-12T08:05:34.853Z",
    "event": {
        "dataset": "system.cpu",
        "duration": 115000,
        "module": "system"
    },
    "host": {
        "cpu": {
            "pct": 0.1629
        }
    },
    "metricset": {
        "name": "cpu",
        "period": 10000
    },
    "service": {
        "type": "system"
    },
    "system": {
        "cpu": {
            "cores": 4,
            "idle": {
                "norm": {
                    "pct": 0.8371
                },
                "pct": 3.3484,
                "ticks": 806327077
            },
            "iowait": {
                "norm": {
                    "pct": 0
                },
                "pct": 0,
                "ticks": 312229
            },
            "irq": {
                "norm": {
                    "pct": 0.0125
                },
                "pct": 0.0501,
                "ticks": 6548016
            },
            "nice": {
                "norm": {
                    "pct": 0
                },
                "pct": 0,
                "ticks": 517231
            },
            "softirq": {
                "norm": {
                    "pct": 0.0025
                },
                "pct": 0.01,
                "ticks": 1561223
            },
            "steal": {
                "norm": {
                    "pct": 0
                },
                "pct": 0,
                "ticks": 156646
            },
            "system": {
                "norm": {
                    "pct": 0.0551
                },
                "pct": 0.2206,
                "ticks": 31037256
            },
            "total": {
                "norm": {
                    "pct": 0.1629
                },
                "pct": 0.6516
            },
            "user": {
                "norm": {
                    "pct": 0.0927
                },
                "pct": 0.3709,
                "ticks": 139619320
            }
       }
    }
}
```
<br>
<br>

## 항목별 상세

### cores

해당 서버의 Core 갯수

- `system.cpu.cores`

### idle

CPU가 모든 일을 끝내고 쉬고 있는 시간을 의미한다.
I/O wait 또는 Steal 등의 값으로 인해서 Usage와의 관계가 일치하지는 않다.

- `system.cpu.idle.norm.pct`
- `system.cpu.idle.pct`
- `system.cpu.idle.ticks`

### iowait

CPU가 입출력을 대기하는데 사용하는 시간의 비율, 프로세스에 바로 접근 할 수 없는 상황의 경우

- `system.cpu.iowait.norm.pct`  
- `system.cpu.iowait.pct`
- `system.cpu.iowait.ticks`

### irq

CPU가 Interrupt Request를 처리하는 시간의 비율

- `system.cpu.irq.norm.pct`
- `system.cpu.irq.pct` 
- `system.cpu.irq.ticks`

### softirq
CPU가 Soft Interrupt Request를 처리하는 시간의 비율

- `system.cpu.softirq.norm.pct`
- `system.cpu.softirq.pct`
- `system.cpu.softirq.ticks`



#### Interrupt에 대해서

프로그램 실행하는 도중에 예기치 않은 상황이 발생한 경우 현재 실행중인 작업을 즉시 중단하고 발생된 상황을 우선 처리한 후 실행중이던 작업으로 복귀하여 계속 처리하는 것을 말한다. 외부, 내부, 소프트웨어 인터럽트로 구분한다.  

1. 외부 인터럽트  
- 전원 이상 인터럽트 : 정전 또는 전원 이상 시 발생
- 기계 착오 인터럽트 : CPU의 기능적인 오류 동작으로 발생
- 외부 신호 인터럽트 : 타이머에 의해 규정된 시간을 알리는 경우, 키보드로 키를 누른 경우, 외부 장치로 부터 인터럽트 요청이 있는 경우
- 입출력 인터럽트 : 데이터의 오류나 이상 현상이 발생한 경우, 입출력 장치가 데이터의 전송을 요구하거나 전송이 끝났음을 알릴 경우

2. 내부 인터럽트
- 잘못된 명령이나 데이터를 사용할 때 Trap 이라고 함, 0으로 나누기, overflow ,underflow, 명령어 잘못 사용한 경우, 부당한 기억장소의 참조와 같은 프로그램 상의 오류 등

3.  소프트웨어 인터럽트
- 프로그램 처리중에 명령 요청에 의해 발생한 것
- SVC 인터럽트 : 사용자가 SVC 명령을 써서 의도적으로 호출한 경우, 복잡한 입출력 처리를 해야하는 경우, 기억장치 할당 및 오퍼레이터와의 대화를 하는 경우 발생

 
_예시_

CPU는 컴퓨터의 주변기기와 데이터와 신호를 주고 받으며 보드에서 일어나는 모든 일을 관장한다. 주변기기들이 어떠한 일을 하게 되면 주변 기기는 CPU에게 이러한 사실을 알려주어야 한다. 주변기기가 CPU에게 어떤 사실을 알리면 CPU는 하던 일을 멈추고 해당 요청을 처리한다. 따라서IRQ 값이 크다면 네트워크나 드라이브 같은 하드웨어를 처리하는데 들어가는 시간이 크다. CPU에 영향을 주는 하드웨어를 수정하거나 시스템을 업그레이드 해야함을 의미한다.

 
### nice
양의 값의 nice(우선순위)를 가진 유저 레벨의 프로세스가 점유하고 있는 CPU 시간의 백분율을 나타냄. 기본적으로 현재 "사용중"인 CPU time을 말한다.

- `system.cpu.nice.norm.pct`
- `system.cpu.nice.pct`
- `system.cpu.nice.ticks`

 

#### Nice에 대해서

CPU 스케쥴링 우선순위. -20 ~ + 19 까지 있으며 크기가 클수록 우선순위가 낮다. root 유저는 renice를 통해서 프로세스의 우선순위를 변경할 수 있다. 일반 유저는 우선순위를 낮추는 것만 가능하다.

### steal
가상 서버와 물리 서버 환경 차이에서 발생하는 지표. 가상화된 자원을 분배하는 과정에서 CPU의 자원을 얼마나 뺏기고 있는 지 알려주는 지표. 하이퍼바이저가 다른 가상 프로세스(가상머신)를 서비스 하는 동안 가상 CPU가 실제 CPU를 기다리는 시간을 백분율로 표시한 값.

> 가상화 환경이 아닌 경우에는 Steal time은 아무런 의미를 갖지 않는다.

- `system.cpu.steal.norm.pct`
- `system.cpu.steal.pct`
- `system.cpu.steal.ticks`

#### Steal 값이 높아지는 현상

1. 원인
- VM이 올라가 있는 물리장비의 자원이 처음부터 부족하거나 물리 장비의 자원은 충분하나 VM이 할당받은 CPU자원이 부족한 경우.

2. 현상

- 배치와 같은 백그라운드 작업은 오래 걸릴 뿐
- 웹 어플리케이션과 같은 실시간 처리는 성능이 감소 -> 실시간 요청 처리 실패 -> 서비스 장애

3. 해결방법

- Cloud Provider
  - 리소스 제한 설정, VM 자원 사용률에 대한 설정
  - 하이퍼바이저 업그레이드
  - 물리서버 사양 업그레이드
  - VM 마이그레이션

- Cloud Client
  - 인스턴스 사양 업그레이드

### system
시스템(커널)에 의해서 사용되는 CPU 시간의 비율

- `system.cpu.system.norm.pct`
- `system.cpu.system.pct`
- `system.cpu.system.ticks`

### user
시스템(커널)에서 사용하는 프로세스를 제외한 사용자 어플리케이션에 의해 사용되는 CPU 비율

- `system.cpu.user.norm.pct`
- `system.cpu.user.pct`
- `system.cpu.user.ticks`

### total
위의 메트릭 항목들을 가지고 계산하여 도출된 CPU 사용량

- `system.cpu.total.norm.pct`
- `system.cpu.total.pct`


### Metric beats 소스코드 

수집을 담당하는 Metric beats에서 CPU에 관한 정보를 normalized, pct와 같이  ㄱㅖ산하여 Elastic search에 넘긴다. 어ㄸㅓㄴ식으로 ㄱㅖ산이 되는 지 확인하기 위해서 소스코드를 분석한다.


#### normailized

```go
func (m *Metrics) NormalizedPercentages() Percentages {
	return cpuPercentages(m.previousSample, m.currentSample, 1)
}
```

normalized는 CpuPercentage값에서 numCPU를 1로 가정하여 ㄱㅖ산하고 이이다.


#### pct

```go
// The CPU percentages are divided by given numCPU value and rounded
// using Round.
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

CPU Percentage 값에 대해서 어ㄸ언 식으로 구하는 지 살펴보자.
Sigar(System Information Gatherer and Reporter) 라이브러리를 이용해서 가져온 객체(s0, s1)을 `calculatePct` 함수에 넣고 이ㅆ다. Sigar 객체에는 `user`, `sys`, `idle` 등 앞서 살펴본 메트릭에 대한 관찰 시점의 값을 가지고 있다. 관찰된 두 개의 값의 변화량을 관찰 시점에 차이인 시간의 변화량으로 계산을 하고 있다. 

즉, CPU Metric(%) = New CpuMetricValue - Old CpuMetricValue  / New timeStamp - Old timeStamp * 100 으로 계산 할 수 있다.

여기서 CPU와 관련된 시간에 대해서 궁금하다. 관찰 시점은 어떻게 정의 되는가? CPU tick 값을 통해서 확인할 수 있다.

CPU는 Clock cycle을 가지면서 진동을 하면서 처리를 하는 것으로 알려져있다. 
간단하게 0과 1을 한번 하는 것을 하나의 tick이라고 했을 때 이 tick을 시간 값으로 변환된다. 
바로 1/f 가 바로 1 tick에  걸리는 시간이다.

예를 들면 100Hz의 CPU는 1 tick에 0.01초가 걸리는 셈이다.

수집된 Metric을 확인해보면 ticks 값도 같이 수집되어 있는 걸 확인할 수 이이다. 이는 timedelta에 대한 값으로 볼 수 있다. 수집 대상 호스트의 CPU frequency 값을 안다면 Percentage를 계산하기 위해서 사용된 시간 값을 알 수 있다.

> Core, Thread 등에 대해서는 전혀 고민하지 않은 개념적인 것만 확인한 것임


# 참조
- [CPU 지표정리](https://serverfault.com/questions/116950/what-does-nice-mean-on-cpu-utilization-graphs/527945)
- [CPU Steal Time의 원인과 대책](https://www.whatap.io/ko/blog/25/)
- [Accurate calculation of CPU usage given in percentage in Linux](https://stackoverflow.com/questions/23367857/accurate-calculation-of-cpu-usage-given-in-percentage-in-linux)
- [What does 'nice' mean on CPU utilization graphs?](https://serverfault.com/questions/116950/what-does-nice-mean-on-cpu-utilization-graphs/527945)
 

 

 

 

 

 
