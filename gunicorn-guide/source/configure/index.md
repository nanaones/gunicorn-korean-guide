---
title: configure
date: 2020-07-11 16:08:05
---

이 페이지 에서는 간단한 Gunicorn 설정법에 대해서 설명합니다. 

---

Gunicorn은 총 다섯 군데에서 설정을 읽어들입니다.

1. `환경 변수`  
    Gunicorn은 먼저 `설정 파일`로부터 환경 변수를 읽습니다.
2. `프레임 워크별 구성(설정파일)`  
    Gunicorn은 `프레임 워크별 구성(설정파일)`에서 구성을 읽습니다. 현재는 Paster 응용 프로그램에만 영향을줍니다.
3. `gunicorn.conf.py`  
    현재 작업 경로에서 검색되거나, 명령 행 인수로 지정하여 사용할 수 있습니다.  
4. `GUNICORN_CMD_ARGS`   
    환경 변수에 저장된 커멘드라인 인수입니다.
5.  명령줄 인수 지정(커멘드라인)  
    명령줄로 인수를 지정하는 것은 가장 마지막에 적용됩니다. (가장 마지막에 적용되어, 모든 설정을 덮어씁니다.)  

    커멘드라인을 사용하여 구성된 설정을 콘솔에 출력하려면 다음 명령을 실행하여 확인할 수 있습니다.
    (이 명령어는 응용프로그램의 성공적인 실행 유무와는 별개로, 설정만을 불러오는 기능입니다.)

        $ gunicorn --print-config APP_MODULE



### Command Line
---

**커멘드라인에 옵션이 지정된 경우 앱별 설정 또는 선택적 구성 파일에 지정된 다른 모든 값이 재정의됩니다.** 
커멘드라인에서 모든 Gunicorn 설정을 정의할 수있는 것은 아닙니다. 
전체 커멘드라인 설정 목록을 보려면 다음 명령어를 수행하세요.

    $ gunicorn -h

`--version` 플래그는 위 명령어를 통해 확인할 수 없지만 사용 가능합니다. (ver ~20.0.4)  


### Configuration File
---  

설정 파일은 다음 두 가지 조건을 만족하여야 합니다.  
1. **python extension**을 가진 유효한 파이썬 소스 파일이어야합니다 (예 :`gunicorn.conf.py`).  
2. 파일 시스템에서 읽을 수 있어야합니다.     

모듈 경로 (sys.path, PYTHONPATH)에있을 필요는 없습니다.  
Gunicorn을 시작할 때마다 실행됩니다. (재실행시에도 읽어들입니다.)

사용 방법은 다음과 같이 매개 변수를 설정하려면 할당하세요.
특별한 문법은 없으며, `Python`코드에서 변수를 할당 하는 것과 동일합니다.

설정 예:

    import multiprocessing

    bind = "127.0.0.1:8000"
    workers = multiprocessing.cpu_count() * 2 + 1

모든 설정은 `setting` 에 리스트로 기록되어있습니다. 


### Framework Settings
---

현재 Paster 응용 프로그램 만이 프레임 워크 별 설정에 액세스 할 수 있습니다.    

WSGI 애플리케이션에 설정을 제공하거나 `Django`의 `settings.py`에서 정보를 가져 오는 아이디어가 있다면   
언제든지 GitHub에서 이슈를 통해 제보 바랍니다.    
[issue 페이지 링크](https://github.com/benoitc/gunicorn/issues)

### Paster Applications
---

INI 파일에서 다음과 같이 Gunicorn을 서버로 사용하도록 지정할 수 있습니다.

    [server:main]
    use = egg:gunicorn#main
    host = 192.168.0.1
    port = 80
    workers = 2
    proc_name = brim

Gunicorn이 알고있는 모든 매개 변수는 기본 구성에 자동으로 삽입됩니다.
설정파일 및 자동으로 입력된 설정은 커멘드라인으로 입력된 변수에 덮어쓰기 된다는 것을 명심해 주세요.
