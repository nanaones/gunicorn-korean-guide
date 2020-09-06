# Gunicorn Korean Guide

gunicorn 의 공식 문서에 대한 번역 문서 프로젝트입니다.

gunicorn 문서의 한글화를 통한 국내 사용자들의 불편을 해소하는것, 설정의 간편화, 이해도의 향상을 목표로 하고 있습니다. 

이 문서는 `Hexo` 를 사용한 문서입니다. `Hexo`를 사용한 이유는, MD 파일로 간단하게 정적 페이지를 작성할 수 있기 때문입니다.


# 기여하는 방법

수정 및 번역이 필요하다고 생각하는 부분에 대한 번역을 진행한 후, PR을 통해 참여해주세요.  
브랜치에 대한 컨벤션은 따로 없지만 커밋에 대한 컨벤션은 있습니다. 

1. 포크해 주세요
2. 브랜치를 만들어주세요  
    * 컨벤션은 따로 없습니다.   
        다만, 브랜치명은 보내주시는 PR이라는 책의 제목처럼 대표하는 내용으로 해 주시면 좋겠습니다.  

3. 수정 및 번역이 필요하다고 생각하는 부분에 대한 수정 & 번역작업을 합니다.  
    * 수정하는경우
        ```shell
        $ git commit -m "modify: configure 페이지를 수장한다"
        ```
    * 추가하는경우
        ```shell
        $ git commit -m "add: configure 페이지를 추가한다"
        ```
    * 내용 추가 없이, 페이지의 형태를 수정한 경우
        ```shell
        $ git commit -m "style: configure 페이지에서 번호가 깨지는 문제를 수정한다"
        ```
4. 작업이 완료되면 Push 하고, PR을 보내주세요!
    * Description에는 작업하신 내용과 필요한 이유를 기술해 주세요 

## 로컬에서 Hexo 문서 서버를 띄워봅시다.

### install in ubuntu
``` shell
$ git clone https://github.com/nanaones/gunicorn-korean-guide
$ cd ./gunicorn-korean-guide/gunicorn-guide
$ sudo apt update && sudo apt install -y nodejs npm
$ npm install -g hexo-cli yarn
$ yarn build && yarn start
```
### install in OS X
``` shell
$ git clone https://github.com/nanaones/gunicorn-korean-guide
$ cd ./gunicorn-korean-guide/gunicorn-guide
$ brew install nodejs npm
$ npm install -g hexo-cli yarn
$ yarn build && yarn start
```


## 커밋메세지 컨벤션  

커밋 메세지 앞에 타입을 붙여주세요 타입의 종류는 다음과 같습니다.
 
```
feat: 기능을 추가하거나, 편의기능을 추가한 경우
add : [페이지명] 페이지를 추가한 경우
modify : [페이지명] 페이지의 내용을 수정한 경우
style : [페이지명] 페이지에서 내용이 바뀌지 않는 스타일적인 부분의 수정
```