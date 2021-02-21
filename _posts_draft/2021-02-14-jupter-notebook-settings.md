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




## 테마 세팅
```shell
jt -t chesterish -f roboto -fs 12 -altmd -tfs 12 -nfs 125 -cellw 80% -T -N -vim
```
`-t` : 테마 이름
`-f` : 폰트 설정
`-fs` : 폰트 사이즈
`-tfs` : 마크다운 폰트 사이즈
`-nfs` : 노트북 메뉴 폰트 사이즈
`-cellw` : 셀 가로 길이 %
`-T` : 툴바표시
`-N` : 파일 제목 표시


Usage help	-h	–
List Themes	-l	–
Theme Name to Install	-t	–
Code Font	-f	–
Code Font-Size	-fs	11
Notebook Font	-nf	–
Notebook Font Size	-nfs	13
Text/MD Cell Font	-tf	–
Text/MD Cell Fontsize	-tfs	13
Pandas DF Fontsize	-dfs	9
Output Area Fontsize	-ofs	8.5
Mathjax Fontsize (%)	-mathfs	100
Intro Page Margins	-m	auto
Cell Width	-cellw	980
Line Height	-lineh	170
Cursor Width	-cursw	2
Cursor Color	-cursc	–
Alt Prompt Layout	-altp	–
Alt Markdown BG Color	-altmd	–
Alt Output BG Color	-altout	–
Style Vim NBExt*	-vim	–
Toolbar Visible	-T	–
Name & Logo Visible	-N	–
Kernel Logo Visible	-kl	–
Reset Default Theme	-r	–
Force Default Fonts	-dfonts	–