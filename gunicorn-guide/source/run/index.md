---
title: Run
date: 2020-06-13 13:24:20
---

명령을 사용하여 Gunicorn을 실행하거나 Django, Pyramid 또는 TurboGears와 같은 널리 사용되는 프레임 워크와 통합 할 수 있습니다.  
프로덕션에서 Gunicorn을 배포하려면`deploy`를 참조하세요.

### 명령어
---

Gunicorn을 설치한 이후에는, 다음과 같이, `gunicorn` 명령어를 통해서 접근할 수 있습니다. 

gunicorn
---

Basic usage:

``` {.bash}
$ gunicorn [OPTIONS] [WSGI_APP]
```


여기서 WSGI_APP는`$(MODULE_NAME): $(VARIABLE_NAME)`패턴입니다.  
모듈 이름은 전체 점선 경로 일 수 있습니다. 변수 이름은 지정된 모듈에서 찾아야하는 WSGI Callable 객체를 나타냅니다.  

20.1.0 `[WSGI_APP]` 부분은 `config` 파일에서 지정하였을 경우 입력하지 않아도 됩니다.   


``` {.python}
def app(environ, start_response):
    """Simplest possible application object"""
    data = b'Hello, World!\n'
    status = '200 OK'
    response_headers = [
        ('Content-type', 'text/plain'),
        ('Content-Length', str(len(data)))
    ]
    start_response(status, response_headers)
    return iter([data])
```

위와 같은 app코드는 다음 명령어를 통해 실행 가능합니다:  
``` {.text}
$ gunicorn --workers=2 test:app
```
  
변수 이름은 함수 호출 일 수도 있습니다.  
이 경우 모듈에서 이름을 가져온 다음 어플리케이션 객체를 가져 오기 위해 호출됩니다.  
이런 패턴을 일반적으로 "애플리케이션 팩토리" 패턴이라고하며, 다음과 같이 gunicorn을 적용하여 사용할 수 있습니다.

``` {.python}
def create_app():
    app = FrameworkApp()
    ...
    return app
```

``` {.text}
$ gunicorn --workers=2 'test:create_app()'
```

위치 및 키워드 파라미터도 아래의 옵션을 통해서 사용할 수 있지만   
커멘드라인이 아닌 환경 변수에서 구성을 적용하는 것이 좋습니다.


### Commonly Used Arguments

-   `-c CONFIG, --config=CONFIG` 
    `$(PATH)`, `file:$(PATH)`, or `python:$(MODULE_NAME)` 형식으로 설정파일을 지정합니다.  
-   `-b BIND, --bind=BIND` - 바인딩할 서버 소켓을 지정합니다. 서버 소켓은  `$(HOST)`, `$(HOST):$(PORT)`, `fd://$(FD)`, 혹은 
    `unix:$(PATH)`. 다음과 같이 지정할 수 있습니다 . IP는 `$(HOST)`에 할당합니다.  
-   `-w WORKERS, --workers=WORKERS` - 워커 프로세스의 갯수를 지정합니다.   
    워커 프로세스의 갯수는 보통 서버의 코어당 2-4개 사이에서 결정합니다. `faq` 를 체크하여서 이 파라미터에 대한 설계들을 확인해 보세요.  
-   `-k WORKERCLASS, --worker-class=WORKERCLASS` - 실행할 워커 프로세스의 타입을    
    말합니다. 이 파라미터를 사용하시기 전에는 production 페이지를 반드시 읽어주시기 바랍니다.
    `$(NAME)` 의 변경을 통해서 설정할 수 있습니다. 각 프로세스 워커의 이름들은 `sync`, `eventlet`, `gevent`, `tornado`, `gthread`. 가 있으며, 기본값으로는 `sync`가 설정되어 있습니다. 자세한 정보를 알고싶으신 경우, `worker-class` 문서를 읽어주시기 바랍니다.   
-   `-n APP_NAME, --name=APP_NAME` - 만약
    [setproctitle](https://pypi.python.org/pypi/setproctitle) 가 설치되어 있다면, 프로세스 시스템 테이블에 나타나는 Gunicorn 프로세스의 이름을 이 파라미터를 통해서 변경할 수 있습니다.(`ps` 와 `top`같은 도구에 영향을 끼칩니다.)

환경 변수를 사용하여 설정을 지정할 수 있습니다.   
`GUNICORN_CMD_ARGS <settings>`  

자세한 사용 방법은 `configuration`과 `settings`를 참고 주시기 바랍니다.


---
역자주)
짧은 파이썬 코드를 활용한 실행방법의 예:

``` {.python}
from flask import Flask
app = Flask(__name__)


@app.route('/')
def hello():
    return "Hello World!"

if __name__ == '__main__':
    app.run()
```

실행시킬 python flask app 이 위와 같다면, 실행 구문은 다음과 같습니다.   
``` {.bash}
$ gunicorn [OPTIONS] main:app
```
