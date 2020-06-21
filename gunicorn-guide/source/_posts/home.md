---
title: home
date: 2020-04-13 16:00:29
tags:
---

# Gunicorn Korean Guide

gunicorn 의 공식 문서에 대한 번역 문서 프로젝트입니다.

gunicorn 문서의 한글화를 통한 국내 사용자들의 불편을 해소하는것, 설정의 간편화, 이해도의 향상을 목표로 하고 있습니다. 

이 문서는 `Hexo` 를 사용한 문서입니다. `Hexo`를 사용한 이유는, MD 파일로 간단하게 정적 페이지를 작성할 수 있기 때문입니다.


# 기여하는 방법

수정 및 번역이 필요하다고 생각하는 부분에 대한 번역을 진행한 후, PR을 통해 참여해주세요.

## 로컬에서 Hexo 문서 서버 띄우기

### 설치하기
``` shell
# install 
$ sudo apt update && sudo apt install -y nodejs npm
$ sudo npm install -g hexo-cli
```

### 포스트( 새 페이지 )를 만드는 방법

```
$ hexo new [layout] <title> 
$ hexo new post '페이지명'
```
만들어지는 파일은 `source/` 아래에 생깁니다. 
디버그모드를 통해 적용되는 내용을 확인하면서 문서를 작성할 수 있습니다. 

### 변경사항을 반영하는 방법

```
$ hexo g
```

### 로컬에서 Hexo 서버를 디버그모드로 실행하는 방법
```
$ hexo s --debug 
```


