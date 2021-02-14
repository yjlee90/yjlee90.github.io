---
layout:     post
title:      "Jupyter notebook settings"
subtitle:   ""
date:       2021-02-14 15:30:00
author:     "YooJong"
header-img: "img/post-bg-web.jpg"
catalog: true
tags:
    - Jupyter Notebook 
    - Snippet
---

# Jupyter Notebook

## 설치  

```python
pip install notebook
```


## 서버 실행  

```python
jupyter notebook --allow-root --host=0.0.0.0 --port=9999
```


## 플러그인 설정
```shell
# Clone the repository
cd $(jupyter --data-dir)/nbextensions
git clone https://github.com/lambdalisue/jupyter-vim-binding vim_binding
# Activate the extension
jupyter nbextension enable vim_binding/vim_binding
```

## 자동완성 에러 해결
```python
TypeError: __init__() got an unexpected keyword argument 'column'
```
위와 같은 에러가 나올시에 jedi를 삭제한다.

```
pip uninstall jedi
```
[jupyter lab 에서 자동완성(autocomplete)이 안될 때 해결법](https://woongheelee.com/entry/jupyter-lab-%EC%97%90%EC%84%9C-%EC%9E%90%EB%8F%99%EC%99%84%EC%84%B1autocomplete%EC%9D%B4-%EC%95%88%EB%90%A0-%EB%95%8C-%ED%95%B4%EA%B2%B0%EB%B2%95)



