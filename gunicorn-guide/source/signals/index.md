---
title: signals
date: 2020-08-13 00:41:17
---
Settings
========


이문서는 Gunicorn에서 설정가능한 모든 옵션에 대한 목록입니다. 
일부 설정은 설정 파일에서만 설정할 수 있습니다. 
`설정 이름` 은 설정 파일에서 사용해야하는 이름입니다. 커멘드라인에서 설정시 참조 할 수 있도록 커멘드라인 인수도 함께 기록되어있습니다. 

> **note**
>
> `GUNICORN_CMD_ARGS` 환경 변수를 사용하여 설정을 지정할 수 있습니다.  
> 이 환경변수를 사용하면, 사용 가능한 모든 커멘드라인 인수를 사용할 수 있습니다.  
> 예를 들어, `bind`와 `workers`를 지정하려면 다음과 같이 실행해 볼 수 있습니다. :  
>
>     $ GUNICORN_CMD_ARGS="--bind=127.0.0.1 --workers=3" gunicorn app:app

Config File
-----------

### config

-   `-c CONFIG, --config CONFIG`
-   `None`

Gunicorn의 설정파일을 의미합니다. 

`PATH`, `file:PATH`, 혹은 `python:MODULE_NAME` 형태의 문자열입니다. 

커멘드라인에서 또는 응용 프로그램 특정 구성의 일부로 지정된 경우에만 효과가 있습니다.

### wsgi\_app

-   `None`

`$(MODULE_NAME):$(VARIABLE_NAME)` 패턴의 WSGI 응용 프로그램 경로를 말합니다.

Debugging
---------

### reload

-   `--reload`
-   `False`

코드에 변경사항이 있으면, 워커를 재로드하는 옵션입니다. 

**이 옵션은 개발환경 전용 옵션입니다.** 응용프로그램의 코드가 변경될 때 마다 워커의 재시작을 일으킵니다. 

리로더가 Application preloading과 호환되지 않습니다.   
설정을 붙여넣을 경우, 서버 블록의 응용 프로그램 코드를 가져 오지 않도록하십시오. 그렇지 않으면 preloading이 설계된대로 작동하지 않습니다.   

이 기능의 기본 살장은 파일 시스템으로부터 받는 폴링대신 inotify를 시도하는 것입니다. inotify를 사용하는 이유는 일반적으로 inotify는 시스템 리소스를 덜 소비하기 때문입니다.

> **note**
>
> inotify reloader를 사용하려면  `inotify` 패키지가 설치되어 있어야 합니다.  


### reload\_engine

-   `--reload-engine STRING`
-   `auto`

The implementation that should be used to power reload.
Gunicorn의 엔진이 재시작을 수행할때 사용해야하는 방식을 정의합니다.

사용 가능한 엔진은 다음과 같습니다:

-   'auto'
-   'poll'
-   'inotify' (requires inotify)

### reload\_extra\_files

-   `--reload-extra-file FILES`
-   `[]`

추가적으로 바라볼 파일들을 추가합니다.   

리로드 옵션을 확장하여 설정한 추가 파일을보고 리로드합니다.
(예를 들자면, 템플릿, 설정파일, 사양, 등이 있습니다.)
(e.g., templates, configurations, specifications, etc.).

### spew

-   `--spew`
-   `False`

서버가 실행하는 모든 라인을 뿜어내는(보여주는) 추적 기능을 설치합니다.

This is the nuclear option.
(역자주) 아주 극단적인 선택지 입니다. 

### check\_config

-   `--check-config`
-   `False`

설정을 확인할 것 인지에 대한 설정입니다.

### print\_config

-   `--print-config`
-   `False`

완전히 해결 된대로 구성 설정을 인쇄합니다.
설정이 완전히 적용된 최종상태를 전체적으로 출력합니다. 설정 체크를 의미합니다. 


Logging
-------

### accesslog

-   `--access-logfile FILE`
-   `None`

접속 로그를 기록할 파일을 의미합니다. 

`'-'` 는 stdout을 의미합니다.

### disable\_redirect\_access\_to\_syslog

-   `--disable-redirect-access-to-syslog`
-   `False`

syslog에 대한 리디렉션 액세스 로그를 비활성화합니다.


### access\_log\_format

-   `--access-logformat STRING`
-   `%(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s"`

엑세스 로그의 포멧을 의미합니다.   

|Identifier    |Description|
|:---|:---|
|h             |원격지 주소  |
|l             |`'-'`  |
|u             |사용자 이름  |
|t             |요청일    |
|r             |상태표시줄 (e.g. `GET / HTTP/1.1`)  |
|m             |request method  |
|U             |쿼리가 없는 URL path  |
|q             |쿼리  |
|H             |프로토콜  |
|s             |상태  |
|B             |응답 길이  |
|b             |응답 길이 혹은  `'-'` (CLF format) |
|f             |HTTP 리퍼러  |
|a             |user agent  |
|T             |요청시간 ( 초 )  |
|M             |요청시간 ( 초/10*(-2) )  |
|D             |요청시간 ( 초/10*(-6) )  |
|L             |요청시간 ( 초 )  |
|p             |프로세스 ID  |
|{header}i     |요청 헤더  |
|{header}o     |응답 헤더  |
|{variable}e   |환경변수  |


헤더 및 환경변수 이름에 소문자를 넣고, `{...}x`를  `%(...)s` 안에 넣습니다.   
예를들면 다음과 같습니다. 

    %({x-forwarded-for}i)s

### errorlog

-   `--error-logfile FILE, --log-file FILE`
-   `-`

접속 로그를 기록할 파일을 의미합니다. 

`'-'` 는 gunicorn 로그를 stder로 기록하는것을 말합니다.

### loglevel

-   `--log-level LEVEL`
-   `info`

오류 로그 출력의 레벨을 말합니다.   
유효한 레벨 이름들은 다음과 같습니다:

-   debug
-   info
-   warning
-   error
-   critical

### capture\_output

-   `--capture-output`
-   `False`

stdout / stderr을 오류 로그의 지정된 파일로 리디렉션합니다.


### logger\_class

-   `--logger-class STRING`
-   `gunicorn.glogging.Logger`

Gunicorn에서 이벤트를 기록하는 데 사용할 로거를 말합니다.  

기본 클래스 ( gunicorn.glogging.Logger)는 로깅에서 대부분의 일반적인 사용을 처리 합니다.   
이는 오류 및 액세스 로깅을 제공합니다.  

Gunicorn에 `gunicorn.glogging.Logger` 와 같은 하위 클래스에 대한 Python 경로를 제공하여 자체 로거를 사용 할 수 있습니다.  

### logconfig

-   `--log-config FILE`
-   `None`

사용할 로그 구성 파일입니다.   
Gunicorn은 표준 Python 로깅 모듈의 설정 파일 형식을 사용합니다.  

### logconfig\_dict

-   `--log-config-dict`
-   `{}`



표준 Python 로깅 모듈의 사전 구성 형식을 사용하여 사용할 로그 설정 딕셔너리입니다.  
이 옵션은 이전 파일 구성 형식을 사용하는 logconfig 옵션보다 우선으로 적용됩니다.    

Format:
<https://docs.python.org/3/library/logging.config.html#logging.config.dictConfig>

### syslog\_addr

-   `--log-syslog-to SYSLOG_ADDR`
-   `unix:///var/run/syslog`

syslog 메시지를 보낼 주소입니다.

주소는 다음 형식의 문자열입니다:  

-   `unix://PATH#TYPE` : 유닉스 도메인 소켓의 경우. `TYPE` 은  스트림드라이버의 경우 `stream`, 
`dgram` 드라이버의 경우, `stream`이 기본 드라이버 입니다. 
-   `udp://HOST:PORT` : UDP 소켓 전용
-   `tcp://HOST:PORT` : TCP 소켓 전용

### syslog

-   `--log-syslog`
-   `False`

*Gunicorn* 로그를 syslog로 보냅니다.    


### syslog\_prefix

-   `--log-syslog-prefix SYSLOG_PREFIX`
-   `None`

Gunicorn이 syslog 항목에서 프로그램 이름으로 매개 변수를 사용하도록합니다.

모든 항목의 앞에는 `gunicorn.<prefix>`이 붙습니다.   
기본적으로, 프로그램의 이름은 프로세스의 이름입니다.  


### syslog\_facility

-   `--log-syslog-facility SYSLOG_FACILITY`
-   `user`

Syslog facility 이름

### enable\_stdio\_inheritance

-   `-R, --enable-stdio-inheritance`
-   `False`

stdio 상속을 활성화합니다. 

데몬모드에서 stdio 파일 디스크립터(역자주, File Descriptors, 프로세스에서 특정 파일에 접근할 대 사용하는 추상적인 값)을 상속받을 수 있도록 한다.  

Note: Python stdout 버퍼링을 비활성화 하려면, `PYTHONUNBUFFERED`를 설정하세요.  

### statsd\_host

-   `--statsd-host STATSD_ADDR`
-   `None`

로그인 할 statsd 서버의 `host:port`.  

### dogstatsd\_tags

-   `--dogstatsd-tags DOGSTATSD_TAGS`
-   `(empty string)`

statsd 메트릭에 추가할 쉼표로 구분된 datadog statsd(dogstatsd) 태그 목록.  

### statsd\_prefix

-   `--statsd-prefix STATSD_PREFIX`
-   `(empty string)`

statsd 메트릭을 생성할 때 사용할 접두사(제공되지 않을 경우 후행 `.` 이 추가됩니다.).  

Process Naming
--------------

### proc\_name

-   `-n STRING, --name STRING`
-   `None`

프로세스 이름 지정을 위해 setproctitle과 함께 사용할 기본내용입니다. 

이 설정은 `ps`나 `top` 같은 명령어에 영향을 끼칩니다.   
둘 이상의 Gunicorn 인스턴스를 실행하는 환경에서 두 인스턴스를 구분하기 위해 이름을 정하고 싶을 경우.  
setproctitle 모듈을 설치해야 합니다.    

설정하지 않을경우, *default\_proc\_name* 설정이 이름으로 적용됩니다.   

### default\_proc\_name

-   `gunicorn`

각 애플리케이션 유형에 맞게 조정되는 내부 설정입니다.  


SSL
---

### keyfile

-   `--keyfile FILE`
-   `None`

SSL 키 파일

### certfile

-   `--certfile FILE`
-   `None`

SSL 인증서 파일

### ssl\_version

-   `--ssl-version`
-   `_SSLMethod.PROTOCOL_TLS`

사용할 SSL 버전입니다. 

  |--ssl-version | Description  |
  |:---|:---|
  |SSLv3           |SSLv3 안전하지 않으므로, 사용하지 않는것이 좋습니다.  |
  |SSLv23          |TLS를 위한 별칭입니다. Python 3.6에서 더이상 사용되지 않습니다, TLS를 사용하세요. |
  |TLS             |클라이언트 / 서버에서 사용 가능한 가장 최신 버전을 사용합니다. Python 3.6 이상부터는 SSL를 사용할 수 있습니다. |
  |TLSv1           |TLS 1.0|
  |TLSv1\_1        |TLS 1.1 (Python 3.4+)|
  |TLSv1\_2        |TLS 1.2 (Python 3.4+)|
  |TLS\_SERVER     | TLS와 같은 가장 높은 프로토콜 버전으로 자동 적용되지만, 서버측 SSL 소켓만 연결 가능합니다. Python 3.6 이상 사용 가능|  

### cert\_reqs

-   `--cert-reqs`
-   `VerifyMode.CERT_NONE`

클라이언트 인증서가 필요한지 여부 (stdlib ssl 모듈 참조)  


### ca\_certs

-   `--ca-certs FILE`
-   `None`

CA 인증서 파일  


### suppress\_ragged\_eofs

-   `--suppress-ragged-eofs`
-   `True`

비정형 EOF 억제 (stdlib ssl 모듈 참조)  


### do\_handshake\_on\_connect

-   `--do-handshake-on-connect`
-   `False`

소켓 연결에서 SSL 핸드 셰이크를 수행할지 여부 (stdlib ssl module's)

### ciphers

-   `--ciphers`
-   `None`

OpenSSL 암호 목록 형식으로 사용할 SSL 암호 제품군입니다.  

기본적으로 우리는 Python의 `ssl` 모듈의 기본 암호 목록을 사용합니다. 이 목록에는 파이썬이 출시될 때마다 강력한 암호로 간주되는 암호들이 포함되어 있습니다.  

권장되는 대안으로 OWASP (Open Web App Security Project)는 [A +~ C- 등급의 검증 된 강력한 암호 문자열 집합을 제공 합니다.](https://www.owasp.org/index.php/TLS_Cipher_String_Cheat_Sheet)
OWASP는 각 보안 수준 에서 사용자 에이전트 호환성에 대한 세부 정보를 제공 합니다.

OpenSSL 암호 목록 형식에 대한 자세한 내용은 [OpenSSL 암호 목록 형식 설명서](https://www.openssl.org/docs/manmaster/man1/ciphers.html#CIPHER-LIST-FORMAT) 를 참조하세요.


Security
--------

### limit\_request\_line

-   `--limit-request-line INT`
-   `4094`

HTTP 요청 행의 최대 크기 (바이트)입니다.

이 파라미터는 클라이언트의 HTTP 요청 라인의 허용 크기를 제한하는 데 사용됩니다. 클라이언트의 요청 라인은 HTTP 메서드, URI, 프로토콜 버전으로 구성되므로, 이 지시문은 서버에 대한 요청에 허용된 요청-URI의 길이를 제한합니다. 

서버는 GET 요청의 쿼리 부분에 전달될 수 있는 정보를 포함하여 리소스 이름을 저장할 수 있을 만큼 충분히 큰 값을 필요로 합니다. 값의 범위는 0(무제한)부터 8190까지의 숫자입니다.

이 매개 변수는 DDOS 공격을 방지하는 데 사용할 수 있습니다.


### limit\_request\_fields

-   `--limit-request-fields INT`
-   `100`

요청의 HTTP 헤더 필드 수를 제한합니다.  


이 매개변수는 디도스 공격을 방지하기 위한 요청의 헤더 수를 제한하는 데 사용됩니다. *limit\_request\_field\_size*와 함께 사용할 때 안전성이 더욱 향상됩니다.  
이 값은 100이 기본값이며, 32768보다 클 수 없습니다.  

### limit\_request\_field\_size

-   `--limit-request-field_size INT`
-   `8190`

HTTP 요청 헤더 필드의 허용 크기 제한  

값의 범위는 양수 또는 0 입니다. 
0으로 설정하게 되면, 헤더 필드 크기는 무제한이 됩니다. 

> **warning**
>
> 이 매개변수를 매우 높은값 혹은 무제한으로 설정할 경우, DDOS 공격을 받을 수 있습니다.   


Server Hooks
------------

### on\_starting

-   def on_starting(server):
            pass

마스터 프로세스가 초기화되기 직전에 호출됩니다.  
호출된 프로세스는 Arbitor에 대한 단일 인스턴스 변수를 허용해야합니다.   


### on\_reload

-   def on_reload(server):
            pass

SIGHUP를 통해 리로드하는 동안 워커를 재활용하도록 호출됩니다.  
호출된 프로세스는 Arbitor에 대한 단일 인스턴스 변수를 허용해야합니다.   


### when\_ready

-   def when_ready(server):
            pass

서버가 시작된 직후에 호출됩니다.  
호출된 프로세스는 Arbitor에 대한 단일 인스턴스 변수를 허용해야합니다.   

### pre\_fork

-   def pre_fork(server, worker):
            pass

워커가 분기되기 직전에 호출됩니다.  
호출된 프로세스는 Arbitor와 새로운 워커에 대한 단일 인스턴스 변수를 허용해야합니다.   

### post\_fork

-   def post_fork(server, worker):
            pass

워커가 분기된 직후에 호출됩니다.  
호출된 프로세스는 Arbitor와 새로운 워커에 대한 단일 인스턴스 변수를 허용해야합니다.   

### post\_worker\_init

-   def post_worker_init(worker):
            pass

워커가 응용프로그램을 초기화한 직후에 호출됩니다.  
호출된 프로세스는 워커에 대한 단일 인스턴스 변수를 허용해야합니다.   

### worker\_int

-   def worker_int(worker):
            pass

워커가 SIGINT 또는 SIGQUIT 으로 종료된 직후에 호출됩니다.   
호출된 프로세스는 워커에 대한 단일 인스턴스 변수를 허용해야합니다.   

### worker\_abort

-   def worker_abort(worker):
            pass


워커가 SIGABRT 신호를 받으면 호출됩니다.  
이 호출은 일반적으로 timeout 시 발생됩니다.  
호출된 프로세스는 워커에 대한 단일 인스턴스 변수를 허용해야합니다.   


### pre\_exec

-   def pre_exec(server):
            pass

새 마스터 프로세스가 분기되기 직전에 호출됩니다.  
호출된 프로세스는 Arbitor에 대한 단일 인스턴스 변수를 허용해야합니다.   

### pre\_request

-   def pre_request(worker, req):
            worker.log.debug("%s %s" % (req.method, req.path))

워커가 요청을 처리하기 직전에 호출됩니다. 
호출된 프로세스는 워커와 요청에 대한 두 개의 인스턴스 변수를 허용해야합니다.   

### post\_request

-   def post_request(worker, req, environ, resp):
            pass

워커가 요청을 처리한 후 호출됩니다. 
호출된 프로세스는 워커와 요청에 대한 두 개의 인스턴스 변수를 허용해야합니다.   

### child\_exit

-   def child_exit(server, worker):
            pass


마스터 프로세스에서 작업자가 종료 된 직후에 호출됩니다.  
호출된 프로세스는 Arbiter와 방금 종료 된 Worker에 대해 두 개의 인스턴스 변수를 허용해야합니다.

### worker\_exit

-   def worker_exit(server, worker):
            pass

작업자 프로세스에서 작업자가 종료 된 직후에 호출됩니다.  
호출된 프로세스는 Arbiter와 방금 종료 된 Worker에 대해 두 개의 인스턴스 변수를 허용해야합니다.

### nworkers\_changed

-   def nworkers_changed(server, new_value, old_value):
            pass

*num\_workers* 가 변경된 직후에 호출 됩니다.  
호출된 프로세스는 Arbiter의 인스턴스 변수 와 변경 전후의 작업자 수에 대한 두 개의 정수 를 받아야합니다.  
작업자 수가 처음으로 설정된 경우 *old\_value* 는 `None` 입니다.   


### on\_exit

-   def on_exit(server):
            pass

Gunicorn을 종료하기 직전에 호출됩니다.  


호출된 프로세스는 Arbiter에 대한 단일 인스턴스 변수를 허용해야합니다.  


Server Mechanics
----------------

### preload\_app

-   `--preload`
-   `False`

작업자 프로세스가 분기되기 전에 애플리케이션 코드를로드합니다.  

애플리케이션을 미리 로드하여 RAM 리소스를 절약하고 서버 부팅 시간을 단축할 수 있습니다. 물론, 각 워커 프로세스에 대한 응용 프로그램의 로드를 연기하는 경우, 워커를 재시작하여 응용 프로그램 코드를 쉽게 다시 로드할 수 있다.  


### sendfile

-   `--no-sendfile`
-   `None`

`sendfile()`의 사용을 비활성화합니다.  

설정하지 않으면 `SENDFILE` 환경 변수 의 값 이 사용을 활성화하거나 비활성화하는 데 사용됩니다.


### reuse\_port

-   `--reuse-port`
-   `False`

`SO_REUSEPORT` 사용중인 포트에 flag 를 설정하세요.  

### chdir

-   `--chdir`
-   `/usr/src/app`

앱을 로드하기 전에 지정된 파일 경로(디렉터리)에 대한 chdir

### daemon

-   `-D, --daemon`
-   `False`

Gunicorn 프로세스를 데몬 화합니다.    

제어 터미널에서 서버를 분리하고, 백그라운드프로세스로 들어갑니다.  


### raw\_env

-   `-e ENV, --env ENV`
-   `[]`

환경 변수 (key = value)를 설정합니다.    

다음과 같이 실행 환경에 변수를 전달하는 경우,   

    $ gunicorn -b 127.0.0.1:8000 --env FOO=1 test:app

FOO 변수는 응용프로그램 내에서 사용 할 수 있습니다.   

### pidfile

-   `-p FILE, --pid FILE`
-   `None`

PID 파일에 사용할 파일 이름입니다.   
설정되지 않으면, PID 파일이 기록되지 않습니다.   


### worker\_tmp\_dir

-   `--worker-tmp-dir DIR`
-   `None`

워커의 `heartbeat`를 임시로 저장할때 사용할 디렉토리입니다.  
설정하지 않으면 기본 임시 디렉토리가 사용됩니다.  
(역자주, `heartbeat`, 일정한 간격으로 heartbeat 이벤트를 통해, 워커의 생존 유무를 체크합니다.)  



> **note**
>
> 현재의 heartbeat 시스템은 임시 파일 핸들러에서 os.fchmod를 호출하는 것을 포함하며, 디렉토리가 디스크 백업 파일 시스템에 있는 경우 임의의 시간 동안 작업자를 차단할 수 있습니다.
>
> 자세한 내용은 차단-os-fchmod를 참조하고 이 문제를 방지할 수 있는 해결 방법을 참조하세요.


### user

-   `-u USER, --user USER`
-   `501`

프로세스의 user를 변경하고 싶을경우 사용합니다. 

유효한 사용자 ID(`int`) 또는 worker process 사용자를 변경하지 않기 위해 `pwd.getpwnam(value)` 또는 `none`으로 호출하여 검색할 수 있는 사용자 이름을 설정합니다.  

### group

-   `-g GROUP, --group GROUP`
-   `20`

프로세스의 group을 변경하고 싶을경우 사용합니다. 

유효한 사용자 ID(`int`) 또는 worker process 사용자를 변경하지 않기 위해 `pwd.getpwnam(value)` 또는 `none`으로 호출하여 검색할 수 있는 사용자 이름을 설정합니다.  

### umask

-   `-m INT, --umask INT`
-   `0`

Gunicorn이 작성한 파일의 파일 모드에 대한 비트 마스크입니다.  

이설정은 유닉스 소켓 권한에 영향을 미칩니다.  

`os.umask(mode)` 호출에 유효한 값 또는 `int(value, 0)` `0`과 호환되는 문자열 (`0`은 파이썬이 베이스를 추측한다는 의미여서 `0`, `0xFF`, `0022`와 같은 값은 십진수, 16진수, 8진수 표시에 유효합니다.)  


### initgroups

-   `--initgroups`
-   `False`

`True` 로 설정된 경우, 지정된 사용자 이름이 구성원인 모든 그룹과 지정된 그룹 ID 를 사용하여 작업자 프로세스의 그룹 엑세스 목록을 설정해야 합니다.   


### tmp\_upload\_dir

-   `None`

읽을 때 임시 요청 데이터를 저장할 디렉토리입니다.

삭제 예정인 변수입니다. 

이 경로는 Gunicorn 작업자에 대해 설정된 프로세스 권한으로 쓰기가 가능해야 합니다. 지정되지 않으면 Gunicorn은 시스템에서 생성된 임시 디렉토리를 선택할 것입니다.

### secure\_scheme\_headers

-   `{'X-FORWARDED-PROTOCOL': 'ssl', 'X-FORWARDED-PROTO': 'https', 'X-FORWARDED-SSL': 'on'}`

프론트 엔드 프록시가 HTTPS 요청을 나타내기 위해 사용하는 헤더 및 값을 포함하는 dictionary 자료형을 말합니다.  
이를 통해 Gunicorn 에게 `wsgi.url_scheme`을 `https`로 설정하도록 지시하므로, 사용자의 요청이 안전하다는것을 알 수 있습니다. 

dictionary는 대문자 헤더 이름을 문자열 값에 매핑해야 합니다. 값 비교는 헤더 이름과 달리 대소문자를 구분하므로 HTTPS 요청을 처리할 때 프런트 엔드 프록시가 보내는 것과 정확히 일치하는지 확인해야 합니다.

프론트 엔드 프록시 구성이 여기서 정의한 헤더를 클라이언트에서 직접 전달하지 않도록 하는 것이 중요합니다.

### forwarded\_allow\_ips

-   `--forwarded-allow-ips STRING`
-   `127.0.0.1`

설정된 보안 헤더를 처리 할 수있는 프론트 엔드의 IP입니다. (쉼표로 구분).

프런트 엔드 IP의 확인을 비활성화하려면 '*'로 설정하면 됩니다.(프론트 엔드 IP 주소를 미리 알 수는 없지만 환경을 신뢰하는 설정에 유용함).

'FORWARDED_ALLOW_IPS' 환경 변수의 기본값은 환경변수를 따릅니다.  정의되지 않은 경우 기본값은 "127.0.0.1" 입니다.   

### pythonpath

-   `--pythonpath STRING`
-   `None`

Python 경로에 추가 할 쉼표로 구분 된 디렉토리 목록입니다.  

e.g. `'/home/djangoprojects/myproject,/home/python/mylibrary'`.

### paste

-   `--paste STRING, --paster STRING`
-   `None`

PasteDeploy 구성 파일을 로드하는 경우, 인수에는 설정 파일에서 앱 섹션 이름(예: `production`) 뒤에 `#` 기호가 포함될 수 있습니다.

e.g. `production.ini#admin.`


현재, 대체 서버 블록 사용은 지원되지 않습니다.  
대신 명령줄 인수를 사용하여 서버 구성을 제어하세요.  


### proxy\_protocol

-   `--proxy-protocol`
-   `False`

프록시 프로토콜 감지를 활성화합니다 (PROXY 모드).


HTTP와 프록시를 함께 사용할 수 있습니다.
stunnel을 HTTPS 프론트 엔드로, Gunicorn을 HTTP 서버 로 사용하는 데 유용 할 수 있습니다.

PROXY protocol:
<http://haproxy.1wt.eu/download/1.5/doc/proxy-protocol.txt>

stunnel 설정 사용예 :

    [https]
    protocol = proxy
    accept  = 443
    connect = 80
    cert = /etc/ssl/certs/stunnel.pem
    key = /etc/ssl/certs/stunnel.key

### proxy\_allow\_ips

-   `--proxy-allow-from`
-   `127.0.0.1`

프록시 요청을 수락할 수 있는 프론트 엔드의 IP(쉼표 구분)

프런트 엔드 IP의 확인을 비활성화하려면 `*`로 설정하세요 (프론트 엔드 IP 주소를 미리 알 수는 없지만 환경을 신뢰하는 설정에 유용함).

### raw\_paste\_global\_conf

-   `--paste-global CONF`
-   `[]`

PasteDeploy 전역 구성 변수를 `key=value`양식으로 설정합니다 .
이 옵션은 여러 번 지정할 수 있습니다.  
변수는 PasteDeploy 진입 점으로 전달됩니다. 예:  

    $ gunicorn -b 127.0.0.1:8000 --paste development.ini --paste-global FOO=1 --paste-global BAR=2

### strip\_header\_spaces

-   `--strip-header-spaces`
-   `False`

헤더 이름과 `:` 사이에 있는 공간을 분리해 줍니다.

이는 취약점을 유발하는 것으로 알려져 있으며, HTTP / 1.1 표준을 준수하지 않습니다. 
[다음을 참고하세요.](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn) 


Server Socket
-------------

### bind

-   `-b ADDRESS, --bind ADDRESS`
-   `['127.0.0.1:8000']`

바인딩 할 소켓입니다.


형식: `HOST`, `HOST:PORT`, `unix:PATH`, `fd://FD`. IP는 유효한  `HOST`를 의미합니다.

여러 주소를 바인딩 할 수 있습니다. ex.:

    $ gunicorn -b 127.0.0.1:8000 -b [::1]:8000 test:app

ipv6 및 ipv4 인터페이스 전체 에서 localhost의 test : app 응용 프로그램을 바인딩합니다.
 
만약 `PORT` 환경변수가 정의되어있다면, 기본설정은 `['0.0.0.0:$PORT']` 입니다.   
그렇지 않다면  기본 설정은 `['127.0.0.1:8000']` 입니다.   

### backlog

-   `--backlog INT`
-   `2048`

보류중인 최대 연결 수입니다.    

이 변수는 서비스를 받기 위해 대기할 수 있는 클라이언트 수를 말합니다. 이 숫자를 초과하면 클라이언트가 연결을 시도할 때 오류가 발생합니다. 이는 상당한 부하를 받는 서버에만 영향을 미칩니다.  

양의 정수여야 하며, 일반적으로 64-2048의 범위로 설정합니다.  

Worker Processes
----------------

### workers

-   `-w INT, --workers INT`
-   `1`

요청을 처리하기위한 작업자 프로세스 수입니다.  

일반적으로 `2-4 x $(NUM_CORES)` 범위의 양의 정수를 사용합니다. 특정 응용프로그램의 작업 부하에 가장 적합한 항목을 찾기 위해 이 항목을 약간 변경하여 사용하세요.

기본으로, `WEB_CONCURRENCY`환경변수를 다릅니다. 만약 설정되어있지 않다면 `1`의 기본값을 가집니다.   
