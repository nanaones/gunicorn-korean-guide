---
title: Integration
date: 2020-06-13 15:08:16
---

Gunicorn은 Django와 Paste Deploy 같은 어플리케이션들 과의 integration(통합) 을 지원합니다.

### 장고(Django)  
------

Gunicorn은 지정되지 않은 경우 'application'이라는 WSGI 호출 가능 파일을 찾습니다. 따라서 일반적인 Django 프로젝트의 경우 Gunicorn을 다음과 같이 호출합니다:  
``` {.bash}
$ gunicorn myproject.wsgi
```

이를 위해서는 프로젝트가 Python 경로에 있어야합니다.  
가장 간단한 방법은`manage.py` 파일과 같은 디렉토리에서이 명령을 실행하는 것입니다.


[\--env](http://docs.gunicorn.org/en/latest/settings.html#raw-env)
옵션을 불러들일 경로를 설정합니다.  
[\--pythonpath](http://docs.gunicorn.org/en/latest/settings.html#pythonpath)
필요한 경우, 응용 프로그램 경로를`PYTHONPATH`에 추가할 수 있습니다.

option:

``` {.bash}
$ gunicorn --env DJANGO_SETTINGS_MODULE=myproject.settings myproject.wsgi
```

### Paste Deployment
----------------

Pyramid 및 Turbogear와 같은 프레임 워크는 일반적으로 Paste Deployment 구성 파일을 사용하여 구성됩니다.   
이 파일을 Gunicorn과 함께 사용하려면 두 가지 방법이 있습니다.

서버 실행자로서 Gunicorn은`pserve` 또는`gearbox`와 같은 프레임 워크의 명령을 사용하여 애플리케이션을 제공 할 수 있습니다.   
이러한 명령에 Gunicorn을 사용하려면 다음과 같이 설정 파일(configuration file)에서 Gunicorn을 서버로 지정하세요:  

``` {.ini}
[server:main]
use = egg:gunicorn#main
host = 127.0.0.1
port = 8080
workers = 3
```


이 방법은 Gunicorn을 시작하는 가장 빠른 방법이지만 몇 가지 제한 사항이 있습니다.   
Gunicorn은 응용 프로그램이 로드되는 방식을 제어 할 수 없으므로 [reload](http://docs.gunicorn.org/en/latest/settings.html#reload) 와 ​​같은 설정은 적용되지 않으며 Gunicorn은 실행중인 응용 프로그램을 핫 업그레이드합니다. [daemon](http://docs.gunicorn.org/en/latest/settings.html#daemon) 옵션을 사용하면 명령 줄 도구가 혼동 될 수 있습니다.
대신 해당 도구에서 제공하는 이러한 기능에 대한 기본 제공 지원을 사용하세요.   


예를 들어, 서버 설정 블록에서`reload = True`를 지정하는 대신`pserve --reload`를 실행하세요.   
다음과 같은 Gunicorn의 고급 구성`config` 키를 사용하여 Gunicorn 구성 파일을 지정하는 [Server Hook](http://docs.gunicorn.org/en/latest/settings.html#server-hooks) 가 지원됩니다.

  
Gunicorn의 리로딩 및 핫 코드 업그레이드의 모든 기능을 사용하려면 [paste option](http://docs.gunicorn.org/en/latest/settings.html#paste) 을 사용하여 대신 응용 프로그램을 실행하세요. 이런 방식으로 Gunicorn은 PasteDeploy 구성 파일에 의해 정의 된 응용 프로그램을 사용하지만 Gunicorn은 파일에 정의 된 서버 구성을 사용하지 않습니다. 대신 [configure gunicorn](http://docs.gunicorn.org/en/latest/configure.html) 을 사용합니다.

예:

``` {.bash}
$ gunicorn --paste development.ini -b :8080 --chdir /path/to/project
```

다른 응용프로그램을 사용할 경우:

``` {.bash}
$ gunicorn --paste development.ini#admin -b :8080 --chdir /path/to/project
```

두 가지 접근 방식 모두 추가 [logging settings](http://docs.gunicorn.org/en/latest/settings.html#logging) 을 지정하면 별도의 지시가없는 한 Gunicorn은 Paste Deployment 구성 파일에있는 모든 로거 섹션을 사용합니다.