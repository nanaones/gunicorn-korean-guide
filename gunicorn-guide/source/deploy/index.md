---
title: deploy
date: 2020-09-14 22:50:06
---


gunicorn을 프록시 서버의 뒤에서 사용하는것을 권장합니다. 
-------------------

사용 가능한 HTTP 프록시가 많지만 [Nginx] (https://nginx.org/)를 사용하는 것이 좋습니다.
다른 프록시 서버를 선택하는 경우 기본 Gunicorn 작업자를 사용할 때 느린 클라이언트를 버퍼링하고 있는지 확인해야합니다.
이 버퍼링이 없으면 Gunicorn은 서비스 거부 공격에 쉽게 취약합니다. [Hey] (https://github.com/rakyll/hey)를 사용하여 프록시가 제대로 작동하는지 확인할 수 있습니다.

다음은 [Nginx](https://nginx.org/)를 사용하는 빠른 클라이언트 전용 [설정파일의 예시입니다.](https://github.com/benoitc/gunicorn/blob/master/examples/nginx.conf)


스트리밍 요청 / 응답 또는 Comet, Long polling 또는 웹 소켓과 같은 기타 멋진 기능을 처리하려면 프록시 버퍼링을 꺼야합니다. 
**이 작업을 수행 할 때 비동기 작업자 클래스 중 하나로 실행해야합니다.**
버퍼링을 끄기 위해서는 `proxy_buffering off;`를 Nginx 설정파일의 `location` 블록에 추가하면 됩니다. 

    ...
    location @proxy_to_app {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_buffering off;

        proxy_pass http://app_server;
    }
    ...

프로토콜 정보를 Gunicorn에 전달하는 것이 좋습니다. 
많은 웹 프레임 워크는 이 정보를 사용하여 URL을 생성합니다. 
이 정보가 없으면 애플리케이션이 'https'응답에 실수로 'http'URL을 생성하여 혼합 콘텐츠 경고(`mixed content warnings`) 또는 애플리케이션 손상을 일으킬 수 있습니다. 
Nginx가 적절한 헤더를 전달하도록 구성하려면`proxy_set_header` 지시문을`location` 블록에 추가해 주세요

    ...
    proxy_set_header X-Forwarded-Proto $scheme;
    ...

Gunicorn이 아닌 다른 호스트에서 Nginx를 실행하는 경우 Nginx에서 보낸 `X-Forwarded- *` 헤더를 신뢰하도록 Gunicorn에 알려야합니다. 
기본적으로 Gunicorn은 연결이 localhost에서 오는 경우에만 이러한 헤더를 신뢰합니다. 이는 악의적 인 클라이언트가 다음 헤더를 위조하는 것을 방지하기위한 것입니다.

    $ gunicorn -w 3 --forwarded-allow-ips="10.170.3.217,10.170.3.220" test:app

Gunicorn 호스트가 외부 네트워크에서 완전히 방화벽에 보호되어 모든 연결이 신뢰할 수있는 프록시 (예 : Heroku)에서 오는 경우이 값을 '*'로 설정할 수 있습니다. 
애플리케이션이 안전하지 않은 연결 * \ *을 통해 SSL 전용 콘텐츠를 제공하도록 속일 수 있으므로 신뢰할 수없는 프록시에서 또는 클라이언트에서 Gunicorn에 대한 연결이 직접 올 수있는 경우, 이 값을 사용하는 것은 잠재적으로 위험합니다.

Gunicorn 19는 `REMOTE_ADDR`이 처리되는 방식에 큰 변화를 가져 왔습니다. Gunicorn 19 이전에는 신뢰할 수있는 프록시에서받은 경우 `X-Forwarded-For` 값으로 설정되었습니다. 그러나 이것은 3875와 호환되지 않았으므로`REMOTE_ADDR`은 이제 **프록시**의 IP 주소이며 **실제 사용자 주소가 아닙니다.**

프록시 될 때 액세스 로그가 **실제 사용자의** IP를 나타내도록하려면 access-log-format을 `X-Forwarded-For`를 포함하는 형식으로 설정해야합니다. 예를 들어이 형식은 `REMOTE_ADDR` 대신 `X-Forwarded-For`를 사용합니다.

    %({x-forwarded-for}i)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s"

Gunicorn을 TCP `host : port` 튜플이 아닌 UNIX 소켓에 바인딩하면 `REMOTE_ADDR`이 완전히 비어 있다는 점도 주목할 가치가 있습니다.

Using Virtualenv
----------------
[Virtualenv](https://pypi.python.org/pypi/virtualenv)위에서 응용프로그램을 제공하려면 일반적으로 Gunicorn을 Virtualenv에 직접 설치하는 것이 가장 쉽습니다. 
그렇게 되면 애플리케이션을 정상적으로 실행하는 데 사용할 수있는 Virtualenv 용 Gunicorn 스크립트 세트가 생성됩니다.


Virtualenv가 설치되어 있으면 다음과 같이 할 수 있습니다.

    $ mkdir ~/venvs/
    $ virtualenv ~/venvs/webapp
    $ source ~/venvs/webapp/bin/activate
    $ pip install gunicorn
    $ deactivate

그런 다음 `~/venvs/webapp/bin`에 설치된 세 개의 Gunicorn 스크립트 중 하나를 사용해야합니다.

Note: `-I` 또는 `--ignore-installed` 옵션을 pip에 전달하여 Virtualenv에 Gunicorn을 강제로 설치할 수 있습니다.

    $ source ~/venvs/webapp/bin/activate
    $ pip install -I gunicorn

Monitoring
----------

**note**
> 서비스 모니터 중 하나를 사용할 때 Gunicorn의 데몬 모드를 활성화하지 마세요.
> 모니터는 실행중인 프로세스가 모니터링해야하는 프로세스 일 것으로 예상합니다.
> 데몬 화는 모니터링되지 않는 프로세스를 생성하고 일반적으로 모니터 서비스를 혼동하는 fork-exec를 수행합니다.

### Gaffer

#### Using Gafferd and gaffer

[Gaffer](https://gaffer.readthedocs.io/) 는 Gunicorn을 모니터링 할 수 있습니다. 
    간단한 설정법은 다음과 같습니다:  

    [process:gunicorn]
    cmd = gunicorn -w 3 test:app
    cwd = /path/to/project

그런 다음 Gunicorn을 사용하여 쉽게 관리 할 수 ​​있습니다. [Gaffer](https://gaffer.readthedocs.io/).

#### Using a Procfile

프로젝트에서`Procfile`을 만듭니다:

    gunicorn = gunicorn -w 3 test:app

동시에 시작해야하는 다른 애플리케이션을 시작할 수 있습니다.

그런 다음 [Gaffer](https://gaffer.readthedocs.io/)를 사용하여 Gunicorn 애플리케이션을 시작할 수 있습니다.

    gaffer start

gafferd가 시작되면 Procfile을 직접로드 할 수도 있습니다.

    gaffer load

모든 응용 프로그램은 gafferd이 관리합니다.


### Runit

Gunicorn을 배포하는 일반적인 방법은 [runit](http://smarden.org/runit/)에서 모니터링하는 것입니다. 
다음은 [서비스 예제입니다.](https://github.com/benoitcgunicorn/blob/master/examples/gunicorn_rc)

definition:

    #!/bin/sh

    GUNICORN=/usr/local/bin/gunicorn
    ROOT=/path/to/project
    PID=/var/run/gunicorn.pid

    APP=main:application

    if [ -f $PID ]; then rm $PID; fi

    cd $ROOT
    exec $GUNICORN -c $ROOT/gunicorn.conf.py --pid=$PID $APP

이것을 `/etc/sv/[app_name]/run`으로 저장하고, (권한을 부여하여)실행 가능하게 만듭니다 (`chmod u+x /etc/sv/[app_name]/run`). 
그런 다음 `ln -s /etc/sv/[app_name] /etc/service/[app_name`을 실행합니다.

자동으로 시작되지 않으면 스크립트를 직접 실행하여 문제를 해결하세요.  


### Supervisor

Gunicorn을 모니터링하고 제어하는 ​​또 다른 유용한 도구는 [Supervisor](http://supervisord.org/) 입니다.  
[간단한 예제는 다음과 같습니다.](https://github.com/benoitc/gunicorn/blob/master/examples/supervisor.conf):

    [program:gunicorn]
    command=/path/to/gunicorn main:application -c /path/to/gunicorn.conf.py
    directory=/path/to/project
    user=nobody
    autostart=true
    autorestart=true
    redirect_stderr=true

### Upstart


upstart와 함께 Gunicorn을 사용하는 것은 간단합니다.    
이 예시 에서는 virtualenv에서 `myapp`앱을 실행합니다.   
모든 오류는 `/var/log/upstart/myapp.log` 로 이동합니다.  


**/etc/init/myapp.conf**:

    description "myapp"

    start on (filesystem)
    stop on runlevel [016]

    respawn
    setuid nobody
    setgid nogroup
    chdir /path/to/app/directory

    exec /path/to/virtualenv/bin/gunicorn myapp:app

### Systemd


Linux 시스템에서 일반적으로 사용되기 시작한 도구는 [Systemd](https://www.freedesktop.org/wiki/Software/systemd/)입니다.  
Systemd는 엄격한 프로세스 관리, 리소스 및 권한 제어를 허용하는 시스템 서비스 관리자입니다.

다음은 systemd를 사용하여 들어오는 Gunicorn 요청에 대한 유닉스 소켓을 만드는 데 필요한 설정 파일 및 지침입니다. 
Systemd는 이 소켓을 수신하고 gunicorn이 자동으로 트래픽에 대한 응답을 시작하도록 합니다.

이 섹션의 뒷부분에서 웹 트래픽을 새로 생성 된 Unix 소켓으로 전달하도록 Nginx를 구성하는 방법에 대해서 기록하였습니다.


**/etc/systemd/system/gunicorn.service**:

    [Unit]
    Description=gunicorn daemon
    Requires=gunicorn.socket
    After=network.target

    [Service]
    Type=notify
    # the specific user that our service will run as
    User=someuser
    Group=someuser
    # another option for an even more restricted service is
    # DynamicUser=yes
    # see http://0pointer.net/blog/dynamic-users-with-systemd.html
    RuntimeDirectory=gunicorn
    WorkingDirectory=/home/someuser/applicationroot
    ExecStart=/usr/bin/gunicorn applicationname.wsgi
    ExecReload=/bin/kill -s HUP $MAINPID
    KillMode=mixed
    TimeoutStopSec=5
    PrivateTmp=true

    [Install]
    WantedBy=multi-user.target

**/etc/systemd/system/gunicorn.socket**:

    [Unit]
    Description=gunicorn socket

    [Socket]
    ListenStream=/run/gunicorn.sock
    # Our service won't need permissions for the socket, since it
    # inherits the file descriptor by socket activation
    # only the nginx daemon will need access to the socket
    SocketUser=www-data
    # Optionally restrict the socket permissions even more.
    # SocketMode=600

    [Install]
    WantedBy=sockets.target

다음으로 소켓을 활성화하고 시작합니다. (부팅시에도 자동으로 시작됩니다.)

    systemctl enable --now gunicorn.socket

이제 nginx 데몬이 소켓에 연결할 수 있는지 살펴 보겠습니다.
`sudo -u www-data curl --unix-socket /run/gunicorn.sock http` 를 실행하면 Gunicorn 서비스가 자동으로 시작되고 터미널에서 서버의 일부 HTML을 볼 수 있습니다.

> **note**
>
> systemd는 cgroup을 사용하여 서비스 프로세스를 추적하므로 pid 파일이 필요하지 않습니다.
> 드물게 서비스 주 PID를 찾아야하는 경우, `systemctl show --value -p MainPID gunicorn.service`를 사용할 수 있습니다. 
> 그러나 신호를 보내고 싶다면 `systemctl kill -s HUP gunicorn.service` 가 더 나은 옵션입니다.


> **note**
>
> `www-data` is the default nginx user in debian, other distributions
> use different users (for example: `http` or `nginx`). Check your
> distro to know what to put for the socket user, and for the sudo
> command.

You must now configure your web proxy to send traffic to the new
Gunicorn socket. Edit your `nginx.conf` to include the following:

**/etc/nginx/nginx.conf**:

    user www-data;
    ...
    http {
        server {
            listen          8000;
            server_name     127.0.0.1;
            location / {
                proxy_pass http://unix:/run/gunicorn.sock;
            }
        }
    }
    ...

> **note**
>
> The listen and server\_name used here are configured for a local
> machine. In a production server you will most likely listen on port
> 80, and use your URL as the server\_name.

Now make sure you enable the nginx service so it automatically starts at
boot:

    systemctl enable nginx.service

Either reboot, or start Nginx with the following command:

    systemctl start nginx

Now you should be able to test Nginx with Gunicorn by visiting
<http://127.0.0.1:8000/> in any web browser. Systemd is now set up.

Logging
-------

Logging can be configured by using various flags detailed in the
[configuration
documentation](http://docs.gunicorn.org/en/latest/settings.html#logging)
or by creating a [logging configuration
file](https://github.com/benoitc/gunicorn/blob/master/examples/logging.conf).
Send the `USR1` signal to rotate logs if you are using the logrotate
utility:

    kill -USR1 $(cat /var/run/gunicorn.pid)

> **note**
>
> Overriding the `LOGGING` dictionary requires to set
> `disable_existing_loggers: False` to not interfere with the Gunicorn
> logging.

> **warning**
>
> Gunicorn error log is here to log errors from Gunicorn, not from
> another application.
