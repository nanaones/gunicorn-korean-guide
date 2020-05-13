---
title: Install
date: 2020-05-14 01:27:59
---



### python 버전
---
파이썬 3.0 ~ 3.5 버전이 필요합니다.   



### pip를 사용해서 설치하기
---
가장 최신의 gunicorn 배포버전을 설치하는 방법은 다음과 같습니다. 
``` {.bash}
$ pip install gunicorn

OR

$ pip3 install gunicorn
```


### 소스코드에서 빌드하기
---

다른 파이썬 소스코드와 동일한 방법으로 소스코드로부터 설치할 수 있습니다. 

``` {.bash}
$ pip install git+https://github.com/benoitc/gunicorn.git
```

아니면, 아래와 같은 방법으로 GitHub에 있는 최신 소스코드로 설치할 수 있습니다. 

``` {.bash}
$ pip install -U git+https://github.com/benoitc/gunicorn.git
```

### 비동기 워커(Async Worker)
---

서버가 요청을 처리하는 동안 어플리케이션이 오랫동안 정지하는 경우에는 
[Eventlet](http://eventlet.net) 혹은 [Gevent](http://www.gevent.org/)를 설치하여 사용할 수 있습니다. 자세한 내용은 
서버가 사용할 워커(Worker)의 타입을 변경하기 위해서 고려할 때 에는 [design docs](design.html) 를 확인해 주세요.

역자 주) 
Gunicorn은 비동기 워커(Worker)를 사용한 비동기 작업이 가능합니다. 비동기 작업은 주로, 네트워크 통신 시간이 서버의 응답시간중 큰 부분을 차지할 경우 사용하게 됩니다. 

*  `Eventlet`만 사용할 경우
    ``` {.bash}
    $ pip install greenlet, eventlet, gunicorn[eventlet]
    ```

* `Gevent`만 사용할 경우
    ``` {.bash}
    $ pip install greenlet, gevent, gunicorn[gevent] 
    ```

`Eventlet`,  `Gevent` 둘 다 `greenlet`이 필요하며 각 패키지를 섪치 할 때 자동으로 설치됩니다. 설치에 실패하는 경우, Python 헤더를 설치해야합니다. 이 헤더는 대부분의 패키지 관리자에서 사용할 수 있습니다. 우분투에서 `apt`의 패키지 이름은`python-dev`입니다.

[Gevent](http://www.gevent.org/)에는`libevent` 1.4.x 또는 2.0.4가 설치되어 있어야합니다.  
이 버전은 패키지 관리자(`pip`) 에서 사용 가능한 것보다 최신 버전 일 수 있습니다. [libevent](http://libevent.org/)가 설치되어 있어도 [Gevent](http://www.gevent.org/)가 빌드되지 않으면 가장 문제를 일으킬 가능성이 높습니다.


추가 패키지
---

일부 Gunicorn 옵션에는 추가 패키지가 필요합니다. `[extra]`구문을 사용하여 Gunicorn과 동시에 설치할 수 있습니다.

기본 워커(Worker)가 아닌, 옵션을 통해 사용되는 워커의 경우에는 대부분의 추가 패키지가 필요합니다. 워커(Worker)의 변화를 고려할시기에 대한 자세한 내용은 [design docs](design.html)를 참고하세요.  

-   `gunicorn[eventlet]` - Eventlet에 기반한 greenlets 워커(Worker)
-   `gunicorn[gevent]` - Gevent에 기반한 greenlets 워커(Worker)
-   `gunicorn[gthread]` - Threaded 워커(Worker)
-   `gunicorn[tornado]` - Tornado에 기반한 워커(Worker), 권장하지 않습니다.  

둘 이상의 Gunicorn 인스턴스를 실행중인 경우, `proc-name` {.interpreted-text role = "ref"}의 설정은 `ps` 및`top`과 같은 도구에서 각 인스턴스를 구별하는 데 도움이됩니다.

-   `gunicorn[setproctitle]` - 프로세스 이름 설정


아래와 같이 여러개의 엑스트라를 결합할 수 있습니다 .  
`pip install gunicorn[gevent,setproctitle]`

DebianGNU-Linux
================

Debian GNU/Linux 를 사용하는 경우 virtualenv와 함께 다른 버전의 Gunicorn을 사용하려는 경우를 제외하고 시스템 패키지를 사용하여 Gunicorn을 설치하는 것이 좋습니다. 여기에는 여러 가지 장점이 있습니다.

- 간편한 설치 :`/etc/gunicorn.d`에 정의 된 구성에 따라 여러 개의 Gunicorn 인스턴스가 자동으로 시작됩니다.
- 로그의 기본 위치 (`/var/log/gunicorn`) `logrotate`를 사용하여 로그를 자동으로 회전하고 압축 할 수 있습니다.
- 보안 강화 : 전용 UNIX 사용자/그룹으로 각 Gunicorn 인스턴스를 쉽게 실행할 수 있습니다.
- 현명한 업그레이드 경로 : 최신 버전으로 업그레이드하면 가동 중지 시간이 줄어들고 구성 옵션의 충돌하는 변경 사항을 처리하며 비 호환성 인 경우 신속하게 롤백 할 수 있습니다. 패키지는 몇 초 만에 시스템에서 완전히 제거 할 수 있습니다.


Ubuntu
---

[Ubuntu](https://www.ubuntu.com/) 12.04 (trusty) 이상에는 기본적으로 Gunicorn 패키지가 포함되어 있으므로 일반적인 방법으로 설치할 수 있습니다.

``` {.bash}
$ sudo apt-get update
$ sudo apt-get install gunicorn
```

stable (\"stretch\")
---

[Debian](https://www.debian.org/)의 "stable\" 배포판의 Gunicorn 버전은 19.6.0 (2017 년 6 월)입니다. 다음을 사용하여 설치할 수 있습니다.

``` {.bash}
$ sudo apt-get install gunicorn
```

[Debian Backports](https://backports.debian.org/)를 사용하여 최신 버전을 사용할 수도 있습니다.   
먼저, 다음 줄을 `/etc/apt/sources.list`에 복사하십시오:

``` {.bash}
deb http://ftp.debian.org/debian stretch-backports main
```

다음으로 로컬 패키지 목록을 업데이트하십시오.
``` {.bash}
$ sudo apt-get update
```

다음을 사용하여 최신 버전을 설치할 수 있습니다.  
``` {.bash}
$ sudo apt-get -t stretch-backports install gunicorn
```

oldstable ("jessie")
---

[Debian](https://www.debian.org/) "oldstable" 배포판의 Gunicorn 버전은 19.0 (2014 년 6 월)입니다. 다음을 사용하여 설치할 수 있습니다.

``` {.bash}
$ sudo apt-get install gunicorn
```

[Debian Backports](https://backports.debian.org/)를 사용하여 최신 버전을 사용할 수도 있습니다. 먼저, 다음 내용을 `/etc/apt/sources.list`에 복사하십시오:

``` {.bash}
deb http://ftp.debian.org/debian jessie-backports main
```

그런 다음 로컬 패키지 목록을 업데이트하십시오:

``` {.bash}
$ sudo apt-get update
```

다음을 사용하여 최신 버전을 설치할 수 있습니다:

``` {.bash}
$ sudo apt-get -t jessie-backports install gunicorn
```

Testing ("buster") / Unstable ("sid")
---

"buster" 과 "sid" 에는 최신 버전의 Gunicorn이 포함되어 있습니다.
일반적인 방법으로 설치할 수 있습니다.

``` {.bash}
$ sudo apt-get install gunicorn
```

