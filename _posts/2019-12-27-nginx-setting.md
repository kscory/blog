---
layout: post
comments: true
title:  "[Nginx] 정적 웹 페이지 서버, 프록시 서버, 캐시 서버 구축하기"
date:   2019-12-27
author: Cory
categories: Web
permalink : /dev/nginx/setting
tags: webserver nginx
---

[이전 포스팅](https://kscory.com/dev/aws/eks-efs) 에서 Nginx 를 설치하고 환경 변수에 대해서 알아보았었습니다.

이번에는 nginx 를 활용해서 정적 파일을 제공하는 웹서버, 프록시 서버를 구축해보겠습니다. 이번 포스팅은 __ubuntu__ 기반으로만 진행할 예정이니 참고 바랍니다.

## 정적 웹 페이지 서버 구축하기 

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

이 설정 파일을 nginx 에 적용해주도록 합니다. `/etc/nginx/nginx.conf` 에서 __include__ 디렉티브를 사용하여 이 설정을 포함하면 됩니다.

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

그런데 한번 `{domain}/about` 으로 새로 접속해보시기 바랍니다. __404 not Found__ 페이지가 나오지 않나요? 

우리가 작성한 프로젝트가 __react-router-dom__ 라이브러리를 이용하는 경우 `http://{domain}/{path}` 와 같은 경로로 바로 접속 하게 되면 `404` 에러가 발생하게 됩니다. 

그 이유는 __SPA__ 의 경우 __index.html__ 하나의 정적 파일만을 가지고 있기 때문입니다. 즉, `{domain}/about` 로 접속했을 때도 `index.html` 파일을 연결시켜 라우팅을 시켜야 하는데 nginx 에서 이를 모르기 때문에 `/about` 에 맞는 HTML 파일을 찾으려 합니다. 따라서 이를 해결하기 위해서는 다른 path 로 접근하더라도 `index.html` 을 제공할 수 있도록 해야 합니다.

그럼 다시 `/etc/nginx/sites-available/demo.conf` 의 설정 파일을 수정하겠습니다. 여기서 location 부분에 __try_files__ 디렉티브 부분을 추가시키면 됩니다.

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

이제 `{domain}/about` 으로 접속했을 때 아래처럼 실행 된다면 성공입니다!! (nginx 는 다시 실행시켜 주세요)

<img src="https://lh3.googleusercontent.com/HqfBtL-cc6k0mhmFZDxLVOMojPWmk-qFDHdo4YwmHEELeo0bH6Wu3X0_QK0xWpUZtqBAFGHtBj_mZF2vuLU5JAQQkZ4z8A6aCmM0Fy7Q3ohzOSnOenY7Z9IJzr1MZO6ZqMhtXL2qLXJ38C0fhNOD7W65QGMZRmqvzeG00rm8F3j-xsoQFxkejEECiiR2MSESfeOTcwKKW3V4Nf3Q5u78vN2Quf8zh4n_-JT73lURz5LR376WocjqEahToNcjUa18_Ic9VFlLtWgzlI8v3nh8XbFulVzU4iS4Gnc11BMt0mpJAN9SYblagKVMzV0r3s-mNsVD-Idj9TnLDJmo4o6C51Zdp-rueXwBGROVYItF9OcN9KLz309FwVK7zDrJgIJHGJqNd9XS0AXTKNO930TcP5lVoBSs8c1yqf0DycThGtj5bo1JCtoZNoKVnAHJagx9jcnwAqY7Q7Eg63TaWuH9qjEmU5lR9TmQpYaQp3kc8bry5VN09KUA8OGeMTPdLMXvvL3l3XgVv24xWwXALT4jCzVrAnaVYkNJ-kq8QTyK9tbo9R99BH7TMF8S_UNCKZFhoEgDbMJMrNNq1BaPkTXeJMKxABgrWwao7sy6QXYTDHFn4ulJVMX5prCPAb7oyoXFAh358_LhENtLIyqYEThxCbaksSY9kOiRC2GyBPR2ZIgn3g80DP_dYMk=w1680-h516-no" alt="nginx-setting-02">

## 프록시 서버 구축하기

이번에는 프록시 서버를 구축하는 방법에 대해서 알아보겠습니다. 이전에 __nodejs__ 기반으로 __react__ 를 구현해보았으니 어플리케이션 서버 또한 __nodejs__ 로 구성해보겠습니다. 따로 코드를 작성하지는 않고 [express](expressjs.com) 에서 제공하는 generator 를 가지고 만들어 보겠습니다. 

```bash
# express 기반 nodejs 서버 코드 생성
npx express-generator demo-app-express

# 프로젝트 이동 & 모듈 설치
cd demo-app-express
npm install

# 프로젝트 시작
npm start
```

이 프로젝트를 시작하면 기본 포트는 3000 번 포트로 열리게 되며 접속이 가능하다면 아래와 같은 화면을 만나실 수 있습니다.

<img src="https://lh3.googleusercontent.com/oDKhwZh77ZRR_tSGZOLShzeLLeXRSp1RJWivDOQzjcLxbxgq21DhHeI7fuM0OJckxYLXAvbAizPRQ61bwFL_k1VZB8v7dzPLo9AZrcZRYYdhpCENuTqN-X00_osOrJNnwYoHvfzqfd1A6DW0qO7PhWz6xeJreBYeD6OiVi9IvGYgTbrvbkMbH0XR1nw6r6vgi0J557cZKwUhj4Zlz0OuCk70XSI_ic5SxgayyWzYU5E4WiwifT9cNipRnliXJT3_0cXOijJ7tR1oajI39PXMolx-LYFbsNU9Ina5bSfWqCFW79JwIEmNTQvryNECqCxXcBcAxZOcwTnaES3krOeoVAhugfKYMoO2BRzHzkn5BWZ_IQUCXzv-4OrVPgLWXPLpvFalmtkLPPzoSb6LVk1QF7fc0-fio5y9qxRxmD8hB9RJXi0AS8AXKil0eMYwC4KHTjMeZIqqaTaIMaSD5i6ud7RMA9nf5FlGBRqx4Gp_sY3E5x6kMYUdJAWDqIezAX6NNuatDz7poLKBnw7sQeYf-RCtfTwMRwX_AD9jWSS4NQ-aWHCsW6jWZlQeFfEo66ReoEMCc7_LQUT9OIQulvDJ3A9pJKIddd8hCE_a2S1qnAxp-GaWZm6jANUpfy-aXO0qIoyNl6UixgYBEUa_S31UBa--BNUYaj4AMDPqJBDTj6J1n69ZKb40JA8=w1680-h550-no" alt="nginx-setting-03">

그럼 이제 nginx 세팅을 해주도록 하겠습니다. 이전과 마찬가지로 `site-available` 내에서 설정파일을 만들어 주도록 하겠습니다. 또한 proxy_params 라는 기본 파라미터를 생성해 주도록 하겠습니다. 

```bash
sudo touch /etc/nginx/sites-available/proxy.conf
sudo touch /etc/nginx/proxy_params

# 로그파일, 에러파일이 들어갈 디렉터리 생성
sudo mkdir /var/log/nginx/proxy/
```

먼저 `proxy_params` 파일을 열어 기본적으로 들어가는 proxy 파라미터를 세팅해 줍니다. 

```bash
# 넘겨 받을 때 프록시 헤더 정보를 지정
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $http_host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-NginX-Proxy true;

# 업로드 가능한 최대 용량
# - default: 1M
client_max_body_size 256M;
# 클라이언트 버퍼 사이즈 (body 에 들어오는 사이즈)
# - default: 32bit-8K, 64bit-16K
client_body_buffer_size 1m;

# 백엔드 서버로부터의 응답을 버퍼링할지 여부를 정의
# - on: 버퍼가 제공하는 메모리 공간을 이용해 응답 데이터를 메모리에 저장
# - off: 응답을 직접 클라이언트에게 전달
proxy_buffering on;
# 백엔드 서버로부터의 응답 데이터를 읽는 데 사용할 버퍼의 수와 크기를 설정
proxy_buffers 256 16k;
# 백엔드 서버 응답의 첫 부분을 읽기 위한 버퍼 크기를 설정 (주로 간단한 헤더 데이터가 포함)
proxy_buffer_size 128k;
# 버퍼가 여기서 지정한 값을 초과하면 데이터를 클라이언트로 보내고 버퍼를 비웁
proxy_busy_buffers_size 256k;

# 한번에 쓸 수 있는 임시 파일 용량
proxy_temp_file_write_size 256k;
# 최대 쓸 수 있는 임시파일 용량
proxy_max_temp_file_size 1024m;

# 백엔드 서버 접속 제한시간을 정의
proxy_connect_timeout 300;
# 백엔드 서버로부터 데이터를 읽을 때의 제한시간
proxy_send_timeout 300;
# 백엔드 서버로 데이터를 전송할 때의 제한시간
proxy_read_timeout 300;
# 백엔드 서버가 보내오는 모든 에러 페이지를 엔진엑스가 직접 클라이언트에게 회신
proxy_intercept_errors on;
```

여기서 잠깐 __proxy_set_header__ 와 각각의 변수에 대해서 정리해 드리겠습니다. 아파치의 경우 이런 헤더들을 자동으로 설정해주곤 하는데 nginx 에서는 직접 설정해 주어야 하는 단점이 있습니다.

- X-Real-IP(요청한 클라이언트의 실제 IP)
  - `$remote_addr`: 요청한 클라이언트 주소
- X-Forwarded-For(클라이언트의 IP 주소, 이전에 프록시 서버가 또 있었다면 그 IP 를 의미한다는 것이 Real-IP 와의 차이)
  - 이를 통해 클라이언트 헤더의 주소를 알 수 있다.
  - __$proxy_add_x_forwarded_for__: 요청 헤더와 그 뒤에 따라오는 클라이언트의 원격 주소를 포함
- Host(호스트 명)
  - __$http_host__: http 요청이 들어 왔을 시 호스트 명
- X-Forwarded-Proto (스키마 이름)
  - __$scheme__: 요청된 스키마를 가지고 있음(ex> http, https)
- X-NginX-Proxy
  - 그냥 __nginx proxy__ 를 썼다는 뜻인데 안써도 무방하며 표준이 아니라는 점 참고

이제 parameter 를 정의했으니 `proxy.conf` 파일을 설정하겠습니다.

```bash
server {
  listen  8080;
  server_name {nginx server ip 혹은 domain 설정};

  access_log /var/log/nginx/proxy/access.log;
  error_log /var/log/nginx/proxy/error.log;

  location / {
    # 위에서 설정한 프록시 파라미터 설정
    include /etc/nginx/proxy_params;
    # 요청을 보낼 주소를 입력
    # - 다른 주소에 있다면 localhost(127.0.0.1) 대신 도메인 혹은 IP 를 적어주면 된다.
    proxy_pass http://127.0.0.1:3000;
  }
}
```

자 그럼 이제 `domain:8080` 으로 접속해 봅시다!! 우리는 서버를 3000 번 포트를 사용 했는데 잘 접속이 되었다면 proxy 설정이 잘 된 것입니다. (nginx 는 다시 실행시켜 주세요)

<img src="https://lh3.googleusercontent.com/F99LHKB6OxsERuCvHUBASmgDebbqByy-shdbsO6lPUdeO9DrVNbmgznww69xF0gO3fc6X3tsRo7n9fz7t6xwGZ8hSDSYl76llmRBfKEDPzBl2LmxhOwtQnwYmaVBgW8mVsepr6wCSv8CmOgsXpYgDopJphpvNSIvYl6KpL0ib4V89XINC_M43yiyN-jNcqkFJIBKcIvZGlg7GUlYNIWk1qQimOshvUwAHN8_6qHdHamPqeiPzetKWGunnGqIe3DNHHU5viWwACtiFbH_HnuSf4k5X7o5MNmXX4G0XNruecvZV98qefgoQ_TthRBjvNZx2x7cnnnSC7LXV7O5SvygCh7gSTIMCxNcdCF6x0Wgv35_xVeI4F_SqFRMyWfscNWqZWufEGCkaS-PbhVxukzPB6Fj3lNn8aF8yoILIxOcJAnPYsJoX9j69iRoziM67xRBcvVsEinxH46T4hVFk9Qt__f-p-NTadJ2EUeUMkkiRgZjPrbqcOVJiOR27et1X-1wp_Zho42hyju1qhMTlOaktIDNfCAnlwBwjQhudpAFoaqz0F9Kk_5c4qJdGLlb-DlnwFfT3cgwq5oOZPoj6p9k6qfoa1PiighjgWxtu78vyhcWcAbwtbS29EfVRGuX5e65NLOQbwaSqiiRqGkprAbiHfIizhl1ZpXgsEkj9XbvIVVv2fPnSU0uDLE=w1680-h442-no" alt="nginx-setting-04">

## 캐시 서버 만들기

[이전 포스팅](https://kscory.com/dev/aws/eks-efs)에서 웹서버를 __캐시 서버__ 로 이용하기 위해 구축하는 경우도 있다는 것을 말씀드렸었습니다. 그래서 이번 챕터에서는 캐시 서버를 구축할 예정입니다. 이전 챕터의 __proxy server__ 예제를 다시 이용하도록 하겠습니다.

먼저 cache 를 남길 디렉터리 경로를 설정해야 합니다. 기본 설정 파일인 `/etc/nginx/nginx.conf` 파일을 열어 아래 설정을 추가해 주도록 합니다.

```bash
# ... 생략 (이전 포스팅 참조)
http {
    # ... 생략

    # 캐시 저장소 설정
    # - levels: 하위 디렉토리의 깊이
    # - keys_zone: 캐시를 가리키는 이름, 차지하는 메모리 크기를 설정
    # - inactive: 캐시된 응답이 지정한 시간만큼 사용되지 않으면 캐시에서 제거
    # - max_size: 전체 캐시의 최대 크기
    proxy_cache_path /var/cache/nginx/cache/ levels=1:2 keys_zone=cache_zone:40m inactive=7d max_size=100m;
    # 임시파일 저장소 설정
    proxy_temp_path /var/cache/nginx/temp/;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-available/*.conf;
}
```

그럼 이제 proxy 로 설정한 config 파일에 cache 를 설정해 주도록 하겠습니다. `/etc/nginx/sites-available/proxy.conf` 열어 아래의 설정값으로 수정해 주도록 합니다.

```bash
server {
  listen  8080;
  server_name {nginx server ip 혹은 domain 설정};

  access_log /var/log/nginx/proxy/access.log;
  error_log /var/log/nginx/proxy/error.log;

  # 가장 우선순위가 낮다. 마지막에 읽치하지 않을 때 실행
  location / {
    include /etc/nginx/proxy_params;
    proxy_pass http://127.0.0.1:3000;
  }

  # Media 파일들인 경우 캐시를 지정 
  location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc)$ {
    include /etc/nginx/proxy_params;
    proxy_pass http://127.0.0.1:3000;
    # 캐시 존을 설정 (캐시 이름)
    proxy_cache cache_zone;
    # 200 302 코드는 20분간 캐싱
    proxy_cache_valid 200 302 20m;
    # 404 에러 코드는 20분간 캐싱
    proxy_cache_valid 404 20m;
    # X-Proxy-Cache 헤더에 HIT, MISS, BYPASS와 같은 캐시 적중 상태정보가 설정
    add_header X-Proxy-Cache $upstream_cache_status;
    # Cache-Control 은 public 으로 설정
    add_header Cache-Control "public";
    # 클라이언트의 헤더를 무시한다.
    # - “X-Accel-Expires”, “Expires”, “Cache-Control”, “Set-Cookie”, “Vary” 의 헤더는 캐시에 영향을 줄 수 있는 요소이므로 잘 설정할 것
    proxy_ignore_headers X-Accel-Expires Expires Cache-Control;
    # 만료기간을 1 달로 설정
    expires 1M;
    # access log 를 찍지 않는다.
    access_log off;
  }
}
```

테스트 이미지를 이용해서 캐시가 잘 되는지 확인해보겠습니다. nodejs 프로젝트의 image 디렉토리인 `demo-app-express/public/images` 로 이동하시기 바랍니다. 거기서 sample image 를 다운받아 봅니다. 이미지가 잘 받아와졌다면 아래와 같이 __354.jpg__ 이름의 이미지가 받아진 것이 보일 것입니다.

```bash
# picsum 의 샘플 이미지 다운
curl -O https://i.picsum.photos/id/83/536/354.jpg
```

<img src="https://lh3.googleusercontent.com/PWq9Yoyqn0ZgK3c0aaNs42LWFEFzadks1NFxo2tUzOP0m8Xp-vxod8526qxbL63SL146eRYffbDW4Cz1u2g-klHdMK4IqBMqzmp1PnJeFabw5fdfZa4OVs4yYDG3aVCOPriXC2BCKpCROQsPnTJEWc3ipKKfr6pIdX5oGDs0xMG-72PFo7wxgmaX41umOl6s1GFjhLKu23zPHYbr-VLnR6BBomjhzUA38hTBvVhgB1STe0o6ENbftjHm7SGJqsEdjJF4JhyBbuRCnHHuulJrZYKHXMeXZys1VVYGr6fi_6fS5S7rl0wxjJ9v3F_ArgnBwLOUMm3K9yXFeECihHMq9q24ciEdXuvlGfc6qpuLO0Ml6E2bGC3Zgh5R3Nd_1QWinD5E_MZgvDWy3N8ZfWXH7WFqnwmR2XOjdZ7nAMja6KlmSndHW9jxP63_I3H0yE859vut0joTOzx01skSsyimkQSB30phLhQQFbGLsL6t8YUX12l0-I3wsMrkkqD9bPR1DRtkS3ix4-1Kb4gMSlVSx5KJ5XhHw43mGzPPFWiaS_Veb01mAYibFEYzXxTQ1qiAmQH4p35X6Fx9ywfZtjQj6xhwQ772FDiobsxpYvwC76aPjYDD3YZns8BvTCmNnZjr1UY32gV5AHsaWtOg1sSDj9MTi7UfmmmReAbMyYxDvMmMYYFve2IJz4c=w961-h79-no" alt="nginx-setting-05">

그럼 curl 명령어릁 이용해서 이 이미지를 가져올 때 캐싱이 잘 되는지 확인해보겠습니다. 같은 명령어를 두번 입력해 주도록 합니다.

```bash
curl -I http://54.180.124.48:8080/images/354.jpg
curl -I http://54.180.124.48:8080/images/354.jpg
```

<img src="https://lh3.googleusercontent.com/I2ceKatfPJbjjiiFlFgm7RT5DF-hYfeGF3FpU7bCyX24ln_AdlTYIWEtitwx8eGv5qt8Pahucn6L8Nc82s18CEzaP1mr6-qi1r1Ln0xDinqXcmeDNUFHqVJA5wSzbxbtMferLasT5jxW_vRKamZ3D7VL3LGJYOzPEbRwal_gXXzNAhAdReSeoYqPmA6twIrUbahLNgN_iz9QyfX4W3tzW17vi5z56k_Hwi7g7hi3E7G7OYs28zXwLp3DNhlL0l8ef7ABpZzrvuc1ffoZmst4XRneNFZEx24awZ5tsgRxfczGUH5Q3u3JsfvMX314_pMO53zwOiCsbakOoYHjIIG1uqN9yg3CmWghvo1JJXaNgVsvjZoQBmkcf0kcLbaFG3JXvoAODzCaJFQ2jXN-bKJARmrIi_KXdrXxEnqrL6VtZeDa1538HWS4T_QLMrqjuoPt8ymAXK8GsdJNqrmwaDqAbgm4fyEYbToserDsOHYHQXtlEtbEgJK2Vvo1fMyLueoUfzzYe_ed9XqXD6aQI9pSOSu-RGoW8c_SwtQhXgafCNBUZlmbUQA8t5RiCPnQuf8c_0OY1M0bGDVAV80BNFCOCG8P7HG5tPWQJtH74w3UTjTtqHzHwB-Zl2o67YVZBjY_sfazgDjDXhx18R-zsvTQE_V78JPTpp3ELqbND2Lq577L9PooHx6ezCA=w727-h528-no" alt="nginx-setting-06">

저희는 캐시 설정에서 `add_header X-Proxy-Cache $upstream_cache_status;` 를 통해 header 에 __X-Proxy-Cache__ 설정을 했습니다. 이는 캐시를 사용했는지 여부를 알 수 있는 헤더인데 처음 명령을 확인해 보면 캐시가 되어 있지 않아 __MISS__ 를 반환하는 것을 알 수 있습니다. 하지만 다시 명령어를 입력하게 되면 캐시된 이미지를 반환하기 때문에 __HIT__ 가 헤더에 있는 것을 알 수 있습니다. 즉, 캐시가 잘 되었다는 증거입니다. 

웹사이트에서도 확인해 보겠습니다.

<img src="https://lh3.googleusercontent.com/HpyQkzxSD2PFdET7L4wxgeXvTQ6L2UXyZ5WWNrSVm963qgxAbn5vxp56CkOgAr35OBqW6WC8NwoOM7Tbd6ROFTFp-HtKkXvzY2ERYkypx6q63euLm1NT9nozK_-rIWF88Y5DmKYNIUET6Mv7Pm9Y-ylwP2S2DXJUmPIq61Fz7l4Zq-CYyxfW3PbLTXRyAWQYalh6vdbNmqTudPzb95nXd8R1ss6xVwWJVGYEKYZyKAYrDOFlBZIX2Nd3S0xXnR9lWJK-llbW0nbDD0kuC5WjVoEb-BIDfYegzb2bEqtchpApKaoAGNzMMtGsGVaVFCH7yP8BcMAdQ3nNz5bqQH56bXTdM8e_CPYLOJQLzu7idamK4ZYo1fpuJKzQ6Dl_9c4H-xZWFwScTgqpqnX4FgjJa_skCKO8Us99CtGDGBSuySphqKgXjIUQMbSNZz2KvIbfkafWrSo1ETp4eCU-B91qEQmUFTsMyV09myvw-mk_UrD1WXyIs15-TXMZHNotZanE0Y8gTdFVXo-kgAmjJCiEeox635IkzoIEF9CKlkmUcmLIsfXMJ3ScAYBmcjEdmukJjxEsGDkuI4fwjFU3K1Ts1J7Ks6hlcHUjJg8GQq-74QWl6T68gWKowmHYJAtLjr6BYcyk9v68P9GEODf56nD7_N5AVVUkdXRfwgK0_gIsQsUoUoAX4qcFzf0=w1032-h495-no" alt="nginx-setting-06">

오른쪽 화면을 보면 __전송됨__ 탭에서 __캐시됨__ 이라고 적혀 있으며 HTTP Status 코드도 __304__ 로 되어 있습니다. 캐시가 잘 되고 있네요.

이번에는 저희가 지정한 path 에 캐시된 데이터가 잘 저장되어 있는지 확인해 보겠습니다. 아래 명령어를 실행해 보세요

```bash
# 캐싱된 파일 확인
sudo grep -rnw '/var/cache/nginx/cache/' -e "354.jpg"
```

<img src="https://lh3.googleusercontent.com/BYYozMMFaUYGhrm6T97wx3j3hy1bPBxV6WYfN8cKyQDpWg0vvYgqtRV47jUPNxBE473qqNyiZm3t2QsjIeKbSjJgNg16Nnk3VobxfUTcBMM2wsIUPZzXQTeIbsphvwDdRGRyXWGsfS5fMw9n39BfqUr_Uy2uSalh-DoZBTFDP1XLQ6VZ_NqnNijHDHgOh8jVBiXbETJ-yBqkO-dvZYe-oIOtSKlEYj_024eWfMGlGb8zaOjIcVc1tbSkBjybywLOL88jgMkHnnA5AFve82CuOKcEFeRVs3Q951FHxjjR1kM1jQXGBMAec-RbI1SRPfyNrQqMaKfg2bdjYECY1xuT6q468PGaDtl_ypCSbwoVGTsKFX8lgq5nJzjSZK4cINSSP0MJJACJowzoVzTMenc6xoiPKfECUkMPZGFYZVTZsJmYkrDcVbq4HZsRUWWno54TMb7aceLjiTqbtxR4JL9nn7kLiShzxS8W4z3facSvdhSzNbz5HgBH0T_VP_eUTV7A5hiit4dis_cNHE0tcJ6142_ZzNjleVIe7HEYU4sVguX3MNbrezZFlkX4nuIc9pRhI4fieS3sn5hiFmp_7XsjyitjK5lPaUx0Sz0JgTjYWn_l6qAFriFuLvyauzQoC4oe6jR0Al2d33NsN1iDKUoRx2KLOlrKV9P-iFZmzAXIEbIVZ3VJwjDDGJ0=w949-h34-no" alt="nginx-setting-06">

바이너리 파일로 캐싱된 것이 보인다면 잘 작동하고 있다는 뜻입니다. 

이번 챕터에서는 간단히 캐시서버를 만드는 방법에 대해서 알아보았습니다. 사실 캐싱 전략은 여러 가지로 존재하기 때문에 각자에게 맞는 rule 에 따라서 잘 조절해보시기 바랍니다. 혹시 로그인 된 사용자마다 다르게 cache 하고 싶다면 __proxy_cache_key__ 디렉티브 부분을 참고하시면 될 것이고, 특정 path 마다 캐시 전략을 달리하고 싶다면 __location__ 부분을 추가해서 새로운 rule 을 적용하면 될 것입니다. 

## 참고사항 (server, location 우선순위)
config 파일에서 server 블럭과 location 블럭은 여러개가 될 수 있습니다. 그렇기 때문에 중복해서 사용했을 때 우선순위를 모른다면 생각한 대로 동작하지 않을 수 있습니다. 즉, 순서에 관계 없이 nginx 는 아래에 나와 있는 순서대로 라우팅을 시키게 됩니다. 물론 같은 순위라면 위부터 먼저 읽게 됩니다.

server 우선순위 (server_name 에 따라)
1. 완전 일치하는 이름
   - ex> kscory.com
2. 에스터리스크(*)로 시작하는 와일드 카드 명
   - ex> *.kscory.com
3. 에스터리스크(*)로 끝나는 와일드 카드 명
   - ex> kscory.*
4. 정규 표현식 명
   - ex> ~^demo\d*\\.kscory\\.com$

location 우선 순위
1. = 연산자 (문자열 완전 일치)
   - ex> `location = /demo/about {}`
2. ^~ 연산자 (문자열의 시작 부분과 일치)
   - ex> `location ^~ /demo/about {}`
3. ~ 연산자 (정규 표현식)
   - ex> `location ~ /demo/about[0-9] {}`
4. ~* 연산자 (대소문자 구분 없는 정규 표현식)
   - ex> `location ~* /demo/about[0-9] {}`
5. 연산자 없는 일반 선언
   - ex> `location /demo/about {}`

이 때 만약 location 블락을 중첩해서 사용한다면 원하는 방식대로 사용할 수도 있습니다. 

## 마무리

이번 포스팅은 좀 길어진 것 같습니다. 항상 쓰던것만 써왔는데 막상 정리하려 하니 뒤죽박죽으로 생각나더군요. ㅜㅜ 하지만 아직 nginx 를 이용한 몇 가지 포스팅이 남은 것 같습니다. __SSL 적용하기__, __Load Balancer__ 로 활용하기, kubernetes 의 __nginx ingress__ 등이 있을 것 같네요. 시간이 나는 대로 틈틈히 포스팅 해야겠습니다.

## 참고 자료

https://stackoverflow.com/questions/43951720/react-router-and-nginx

https://12bme.tistory.com/367

http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_ignore_headers

https://www.joinc.co.kr/w/man/12/nginx/static

https://jojoldu.tistory.com/60

https://www.chanhvuong.com/2887/node-js-express-with-nginx-reverse-proxy-and-cache/

https://stackoverflow.com/questions/29265996/nginx-proxy-pass-not-caching-content
