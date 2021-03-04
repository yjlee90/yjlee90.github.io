---
layout:     post
title:      "Nginx 튜닝"
subtitle:   ""
date:       2021-03-02 15:30:00
author:     "YooJong"
header-img: "img/background/post-bg-web.jpg"
catalog: true
tags:
    - Nginx
    - Server Tuning
    - Performance
---

# Nginx Tuning
사내에서 Locust를 활용하기 위해서 Django를 이용한 간단한 Web application을 만들었다. 이를 Nginx + Gunicorn을 이용해서 서비스를 배포했다. Locust를 이용해서 Web application에 정의된 기능들을 GET, POST로 Request를 날리고 있는데 계속해서 Fail이 되고 있었다. 원인을 찾아보니 Nginx와 gunicorn에서 받아주지 못하고 있었다. 그러다보니 오히려 CPU, Memory의 자원 사용량은 늘어나지 못했다. Linux 서버의 최적화와 Nginx의 최적화가 필요했다.

# Error Messages
## Client가 마주한 Error
```
HTTPError('500 Server Error: Internal Server Error for url: http://123.123.123.123:1234/',)

HTTPError('502 Server Error: Bad Gateway for url: http://123.123.123.123:1234/',)

HTTPError('504 Server Error: Gateway Time-out for url: http://123.123.123.123:1234/board',)

ConnectionError(ProtocolError('Connection aborted.', ConnectionResetError(104, 'Connection reset by peer')),)

ConnectionError(ProtocolError('Connection aborted.', RemoteDisconnected('Remote end closed connection without response',)),)
```


## Nginx Server Error messages
```

```


## Gunicorn Server Error messages
```
```

# Tuning 방법
1. linux open files
2. max user processes
3. file discripter 
4. keepalive_timeout
5. worker_connections
6. worker_rlimit_nofil;
7. kernel parameter


# 참조

[“Too many open files” Error는 어떻게?](https://medium.com/hbsmith/too-many-open-files-%EC%97%90%EB%9F%AC-%EB%8C%80%EC%9D%91%EB%B2%95-9b388aea4d4e)
[Nginx 튜닝](https://m.blog.naver.com/PostView.nhn?blogId=forioso&logNo=221100406400&proxyReferer=https:%2F%2Fwww.google.com%2F)