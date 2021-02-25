---
layout:     post
title:      "Python OS 관련 라이브러리 정리"
subtitle:   "Snippets"
date:       2021-02-04 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Python
    - Snippets
    - OS
---



1. 현재 작업 폴더
```python
os.getcwd()
# '/root'
```

2. 디렉토리 변경
```python
os.chdir('/root/projects')
os.getcwd()
# '/root/projects'
```

3. 특정 경로에 대해 절대 경로
```python
os.path.abspath("./test.py")
# '/root/projects/test.py'
```

4. 경로 중 폴더명만
```python
os.path.dirname("./test.py")
# .

os.path.dirname("/root/projects/test.py")
# '/root/projects'
```

5. 경로 중 파일명만
```python
os.path.basename('/root/projects/test.py')
# test.py
```

6. 경로 중 폴더명과 파일명 나누어서 얻기
```python
dir, file = os.apth.split('/root/projects/test.py')
# dir
# '/root/projects'

# file
# 'test.py'
```


7. 파일 각 경로를 나눠 리스트로 리턴
```python
'/root/projects/test.py'.split(os.path.sep)
# ['root', 'projects', 'test.py']
```

8. 경로를 병합하여 새 경로 생성
```python
os.path.join('/root', 'projects', 'test.py')
# /root/projects/test.py

```

9.  폴더 안의 파일/서브 폴더 리스트
```python
os.listdir('/root/projects')
# [test.py]
```
10. 파일 혹은 폴더 존재 확인

`os.path.exists('/root/proejcts')`

```python
os.path.exists('/root/projects')
```
11. 디렉토리 경로가 존재하는 지 확인

```python
os.path.isdir('/root/projects/test.py')
# True
```

12. 파일 경로가 존재하는 지 확인

```python
os.path.isfile('/root/projects/test.py')
# True
```

13. 파일 크기

```python
os.path.getsize('/root/projects/test.py')
# 1132
```