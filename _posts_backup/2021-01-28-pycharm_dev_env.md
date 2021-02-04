---
layout:     post
title:      "Pycharm remote 개발 환경 구성"
subtitle:   "Python 프로젝트를 위하여"
date:       2021-01-28 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Pycharm
    - Development environment
    - Python
    - SFTP
---
# Pycharm remote 개발환경 구성

## 0.Introduction

파이썬 개인 프로젝트를 하나의 서버에 배포를 하며 노트북과 데스트탑 두 곳에서 개발을 하고 싶었다.  
다른 배포 S/W 없이 Git으로만 하자고 하니 3곳에서 commit, push, pull을 모두 해야 하는 것이 너무 번거롭고 소스가 꼬일 것 같았다.  
그러면서 노트북과 데스크탑, 서버가 모두 동기화는 되었으면 싶었다.   

> 원격서버에 Virtualenv

**Pycharm의 기능 원격서버 파이썬 인터프리터 연결을 이용한다**


## 1.Remote, Local 환경 및 구성

  - 원격서버
    - Ubuntu 18.04
    - python 3.6.5
    - Virtualenv
    
  - 노트북, 데스크탑
    - Window 10
    - Pycharm Pro (Community 버전은 되는 지 확인 안해봄)
    

## 2. Remote server 준비사항
    - Python
    - SSH
    - Venv
    
## 3. Pycharm 설정
    - 새로운 프로젝트 생성
        - Exisiting Interpreter 선택
        - 새로운 Interpreter 생성
        - SSH Interpreter 설정
        - venv 환경 파이썬 경로 입력
        
    - pip 설치
    - 파이썬 파일 실행
        - 파이썬 파일 경로 설정
## 4. 다른 곳에서 작업 계속하기
    - Browse remote server
    - download from 
        
       