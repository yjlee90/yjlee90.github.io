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

# 1. 항목
## 1.1 Metric
- cores
- idle
- iowait
- irq
- nice
- softirq
- steal
- system
- user
- total

## 1.2 norm, pct, ticks
**ticks**  
- 실제 계산 시에 사용되는 CPU 시간의 값
- ticks 관련 아래 따로 내용 정리 

**pct**
- 모든 core에 대하여 해당 메트릭의 비율(%)

**norm**
- pct 값을 core 수로 나누어서 normalized 된 값을 구함

→ 실제로 필요한 데이터는 norm 또는 ticks 까지이다. 전체 pct는 norm 데이터 값에 cores를 곱해서 구할 수 있다.


# 2. 항목별 분석
## 2.1  cores
해당 서버의 Core 갯수
`system.cpu.cores`

## 2.2 idle
CPU가 모든 일을 끝내고 쉬고 있는 시간을 의미한다.
I/O wait 또는 Steal 등의 값으로 인해서 Usage와의 관계가 일치하지는 않다.

- `system.cpu.idle.norm.pct`
- `system.cpu.idle.pct`
- `system.cpu.idle.ticks`

## 2.3 iowait
CPU가 입출력을 대기하는데 사용하는 시간의 비율, 프로세스에 바로 접근 할 수 없는 상황의 경우

- `system.cpu.iowait.norm.pct`  
- `system.cpu.iowait.pct`
- `system.cpu.iowait.ticks`

## 2.4 irq

CPU가 Interrupt Request를 처리하는 시간의 비율

- `system.cpu.irq.norm.pct`
- `system.cpu.irq.pct` 
- `system.cpu.irq.ticks`

## 2.5 softirq
CPU가 Soft Interrupt Request를 처리하는 시간의 비율

- `system.cpu.softirq.norm.pct`
- `system.cpu.softirq.pct`
- `system.cpu.softirq.ticks`

> Interrupt 

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

 

## 2.6 nice
양의 값의 nice(우선순위)를 가진 유저 레벨의 프로세스가 점유하고 있는 CPU 시간의 백분율을 나타냄. 기본적으로 현재 "사용중"인 CPU time을 말한다.

- `system.cpu.nice.norm.pct`
- `system.cpu.nice.pct`
- `system.cpu.nice.ticks`

 

> nice 

CPU 스케쥴링 우선순위. -20 ~ + 19 까지 있으며 크기가 클수록 우선순위가 낮다. root 유저는 renice를 통해서 프로세스의 우선순위를 변경할 수 있다. 일반 유저는 우선순위를 낮추는 것만 가능하다.

## 2.7 steal
가상 서버와 물리 서버 환경 차이에서 발생하는 지표. 가상화된 자원을 분배하는 과정에서 CPU의 자원을 얼마나 뺏기고 있는 지 알려주는 지표. 하이퍼바이저가 다른 가상 프로세스(가상머신)를 서비스 하는 동안 가상 CPU가 실제 CPU를 기다리는 시간을 백분율로 표시한 값.

> 가상화 환경이 아닌 경우에는 Steal time은 아무런 의미를 갖지 않는다.

- `system.cpu.steal.norm.pct`
- `system.cpu.steal.pct`
- `system.cpu.steal.ticks`

_발생원인_

- VM이 올라가 있는 물리장비의 자원이 처음부터 부족하거나 물리 장비의 자원은 충분하나 VM이 할당받은 CPU자원이 부족한 경우.

_장애현상_

- 배치와 같은 백그라운드 작업은 오래 걸릴 뿐
- 웹 어플리케이션과 같은 실시간 처리는 성능이 감소 -> 실시간 요청 처리 실패 -> 서비스 장

_해결방법_

벤더사

리소스의 제한 설정을 조절. 특정 서버에서 실행되는 VM 자원 사용률에 대한 설정 조절

하이퍼바이저 업그레이드

물리 서버으 업그레이드

VM을 다른 물리서버로 이전  -> VM 최적 배치와 연관

고객사

더 좋은 인스턴스를 구매하여 사용

다른 VM으로 재배포

## 2.8 system
시스템(커널)에 의해서 사용되는 CPU 시간의 비율

- system.cpu.system.norm.pct

system.cpu.system.pct

system.cpu.system.ticks

2.9 user
시스템(커널)에서 사용하는 프로세스를 제외한 사용자 어플리케이션에 의해 사용되는 CPU 비율

system.cpu.user.norm.pct

system.cpu.user.pct

system.cpu.user.ticks

2.10 total
위의 메트릭 항목들을 가지고 계산하여 도출된 CPU 사용량

system.cpu.total.norm.pct

system.cpu.total.pct

소스코드 분석
normalized


func (m *Metrics) NormalizedPercentages() Percentages {
	return cpuPercentages(m.previousSample, m.currentSample, 1)
}
percentage


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
 

Sigar(System Information Gatherer And Reporter) 

Metric beats에서 사용하는 sigar.Cpu 은 SIGAR 이라는 Library를 사용한다.

Sigar github repository

 

3. 기타 분석사항
3.1 ticks
3.2 CPU cycle
 

 

 

 

 

 