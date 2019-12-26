---
layout: post
comments: true
title:  "[Nginx] 정적 파일 호스팅 서버, 프록시 서버 설정 하기"
date:   2019-12-27
author: Cory
categories: Web
permalink : /dev/nginx/setting
tags: webserver nginx
---

[이전 포스팅](https://kscory.com/dev/aws/eks-efs) 에서 Nginx 를 설치하고 환경 변수에 대해서 알아보았었습니다.

이번에는 nginx 를 활용해서 정적 파일을 제공하는 웹서버, 프록시 서버를 구축해보겠습니다. 이번 포스팅은 __ubuntu__ 기반으로만 진행할 예정이니 참고 바랍니다.

## 정적 파일 웹 서버 구축하기 

이 챕터에서는 정적 파일을 제공하는 웹 서버를 구축하도록 하겠습니다. 예전에 __react__ 기반의 정적 파일을 호스팅하다가 삽질한 경험이 있어 실습을 위한 정적 파일은 __react__ 기반의 샘플 프로젝트로 사용하겠습니다. 먼저 샘플 프로젝트를 이용해서 빌드 파일을 만들도록 하겠습니다. (사전작업으로 nodejs 가 설치되어 있어야 합니다.)

```bash
# 샘플 프로젝트 clone
git clone -b demo-react-router-app-basic --single-branch https://github.com/Lee-KyungSeok/blog-sample-projects.git

# 프로젝트 폴더로 이동
cd ./blog-sample-projects/demo-app

# 모듈 다운로드
yarn # 혹은 npm install

# 빌드
yarn build # 혹은 npm run build
```

그럼 프로젝트 폴더 안에 `build` 디렉토리가 생길 것입니다. 저희는 이 build 디렉토리에 있는 정적 파일을 제공해주도록 웹 서버를 설정하도록 하겠습니다. 이 디렉토리를 `/usr/share/nginx` 디렉토리에 `demo` 라는 이름으로 복사하도록 하겠습니다.

```bash
sudo cp -r ./build/ /usr/share/nginx/demo/
```

이제 nginx 를 설정해 주도록 하겠습니다. 이전 포스팅에서 가상 호스팅 설정파일은 `sites-available` 디렉토리에 설정한다는 주석을 보았을 것입니다. 따라서 정적파일을 제공하기 위한 환경설정 파일은 이 디렉토리 상에 위치시키도록 하겠습니다.

아래 명령어를 통해 설정을 위한 파일을 만들겠습니다.

```bash
sudo mkdir /etc/nginx/sites-available
# 설정 파일 생성
sudo touch /etc/nginx/sites-available/demo.conf

# 로그파일, 에러파일이 들어갈 디렉터리 생성
sudo mkdir /var/log/nginx/demo/
sudo mkdir /var/log/nginx/demo/
```

그 후 `demo.conf` 파일을 열어 아래와 같이 작성해줍니다.

```bash
server{
  # 서버 포트를 80 번으로 오픈
  # - [::] 는 ipv6 를 오픈한다는 것인데 에러가 날 수 있으므로 끄는 것이 좋다.
  listen  80;
  # listen  [::]:80;

  # 설정할 도메인(IP) 지정 (스페이스로 도메인 구분)
  # - ex1> kscory.com www.kscory.com
  # - ex2> 54.180.90.31
  server_name {nginx server ip 혹은 domain 설정};

  # 엑세스 로그, 오류 로그를 남길 파일 경로 지정
  access_log /var/log/nginx/demo/access.log;
  error_log /var/log/nginx/demo/error.log;

  location / {
    # HTML 파일이 위치할 루트를 설정
    root   /usr/share/nginx/demo/;

    # 사이트의 index 페이지로 할 파일명 설정 (demo 폴더 안에 index.html 이 존재)
    index  index.html index.htm;
  }  
}
```

이 설정 파일을 nginx 에 적용해주도록 합니다. `/etc/nginx/nginx.conf` 에서 nginx.conf 파일을 고쳐주도록 합니다.

```bash
# ... 생략 (이전 포스팅 참조)
http {
    # ... 생략
    include /etc/nginx/conf.d/*.conf;

    # sites-available 디렉터리에서 .conf 파일을 모두 읽어 들입니다.
    include /etc/nginx/sites-available/*.conf;
}
```

그 후 nginx 를 설정이 제대로 되었는지 확인 후 재실행 해 봅시다!

```bash
# config 가 올바로 되었는지 테스트
sudo service nginx configtest

# nginx 재실행
sudo service nginx restart
```

이제 설정한 도메인 혹은 IP 주소로 들어가 보도록 합니다. 아래와 같이 나오면 성공입니다!!.

<img src="https://lh3.googleusercontent.com/5fUoCvZ8KYVbSiEKqJbS-_-hUTvEtvcIakRw1klgbstYedqzF1K8ToPUiGvB-nXeVICSJkZC0CRPBbaLKxj763mLAi7TwiaQ5O0XoMU9VPgaZggcQUYA0R2dLz0PcV2vCgG52ctOOqBg6G3qnK0tThTQCTum4YHN-04JjuGDESkhZ1VPXK_1YZx12ysrtVy-_vhaNIpbFhmw5Zw1UiV_hWwtJmq7jXAdNoTi1WaI2XGeIkT_zoQ8See1PpuikvzZHipob0Zx6r3kLtuMGi3VlvHieAbwOf77EXq7eejEEGc3hYvN0IMV03eih4R0Vaoux7drN5KxNy7FSZ0bzOEXlRSY0m3lRLFc_FPFfXGTSoLpPQxUAYAI60Uop47fkX_CssBIc8kYjLXFs1WuvH_GCTvKIyChsJvfo4IpupoY8Qu6T-5MDPbs_HCglnxTdo4BymP7MybCaSZFx8wBCn4vfTuo_BA-qMaYdiy9Kmey1M7B_w3a58A5wimTJErT8utI_b0DdlyA9i_Iq67K4xmZ5J8sqebKT-0Z30WDokh8ypwTD9SFEhlb8zR61Cs0vJz9acStXshvUF00MF8e4dTquH_8Xf69ET0oPiWlqM4zpvisEfG69IZYQg-o2IXJV7z3Y5oi_bH4Z5uBw-oYWQkg8a7pn_IEf9vOxRo-NVSwErSFcAj6llOW0Jw=w1680-h496-no" alt="nginx-setting-01">

그런데 한번 `{domain}/about` 으로 새로 접속해보시기 바랍니다. __404 not Found__ 페이지가 나오지 않으신가요?? `react` 를  `react-router` 와 함께 사용하는 정적 페이지를 가지고 `http://{domain}/{path}` 와 같은 경로로 브라우저에서 바로 접속할 경우 `404` 에러가 발생하게 됩니다. 

그 이유는 __SPA__ 의 경우 `{domain}/about` 도 `index.html` 파일을 연결시켜 라우팅을 시켜야 하는데 nginx 에서 이를 모르고 `/about` 에 맞는 HTML 파일을 찾으려 하기 때문입니다. 즉, 이를 해결하기 위해서는 다른 path 로 접근하더라도 `index.html` 을 제공할 수 있도록 해야 합니다.

그럼 다시 `/etc/nginx/sites-available/demo.conf` 의 설정 파일을 수정하겠습니다. 여기서 location 부분을 수정해 주면 됩니다.

```bash 
server{
  listen  80;
  server_name {nginx server ip 혹은 domain 설정};

  access_log /var/log/nginx/demo/access.log;
  error_log /var/log/nginx/demo/error.log;

  location / {
    root   /usr/share/nginx/demo/;
    index  index.html index.htm;

    # try_files 는 최하위 우선순위를 갖는 슈도 location 블럭을 생성한다.
    # - 특정 uri 로 들어왔는데 매칭되는 것이 없는 경우 index.html 을 보낸다.
    try_files $uri $uri/ /index.html;
  }  
```

이제 `{domain}/about` 으로 접속했을 때 아래처럼 실행 된다면 성공입니다!!

<img src="https://lh3.googleusercontent.com/HqfBtL-cc6k0mhmFZDxLVOMojPWmk-qFDHdo4YwmHEELeo0bH6Wu3X0_QK0xWpUZtqBAFGHtBj_mZF2vuLU5JAQQkZ4z8A6aCmM0Fy7Q3ohzOSnOenY7Z9IJzr1MZO6ZqMhtXL2qLXJ38C0fhNOD7W65QGMZRmqvzeG00rm8F3j-xsoQFxkejEECiiR2MSESfeOTcwKKW3V4Nf3Q5u78vN2Quf8zh4n_-JT73lURz5LR376WocjqEahToNcjUa18_Ic9VFlLtWgzlI8v3nh8XbFulVzU4iS4Gnc11BMt0mpJAN9SYblagKVMzV0r3s-mNsVD-Idj9TnLDJmo4o6C51Zdp-rueXwBGROVYItF9OcN9KLz309FwVK7zDrJgIJHGJqNd9XS0AXTKNO930TcP5lVoBSs8c1yqf0DycThGtj5bo1JCtoZNoKVnAHJagx9jcnwAqY7Q7Eg63TaWuH9qjEmU5lR9TmQpYaQp3kc8bry5VN09KUA8OGeMTPdLMXvvL3l3XgVv24xWwXALT4jCzVrAnaVYkNJ-kq8QTyK9tbo9R99BH7TMF8S_UNCKZFhoEgDbMJMrNNq1BaPkTXeJMKxABgrWwao7sy6QXYTDHFn4ulJVMX5prCPAb7oyoXFAh358_LhENtLIyqYEThxCbaksSY9kOiRC2GyBPR2ZIgn3g80DP_dYMk=w1680-h516-no" alt="nginx-setting-01">

## 프록시 서버 구축하기

