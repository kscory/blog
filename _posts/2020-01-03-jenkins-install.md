---
layout: post
comments: true
title:  "[Jenkins] Jenkins 설치 & 리버스 프록시 설정"
date:   2020-01-03
author: Cory
categories: ci/cd
permalink : /dev/jenkins/install
tags: jenkins ci/cd
---

jenkins 를 설치하는데 계속 검색하기도 귀찮고 해서 간단하게 남겨볼까 합니다.

최신버전의 jenkins 는 jdk11 을 지원하지만, 아직 불안정하다는 공식 문서에 의해 jdk8 기반으로 진행할 예정입니다.

실습을 시작하기 전 nginx 설치와 인증서 발급과 디피헬만 그룹 생성은 완료해 주시기 바랍니다. ([nginx 설치하기](https://kscory.com/dev/nginx/install), [인증서 발급받기](https://kscory.com/dev/nginx/https) 참고) 또한 필자의 domain 은 __jenkins.kscory.com__ 로 진행합니다.


## java 설치 및 환경변수 설정

설치는 [공식문서](http://openjdk.java.net/install/) 기반으로 진행합니다.

```bash
# 패키지 업데이트
$ sudo apt-get update

# jdk 파일 다운
$ sudo apt-get install openjdk-8-jdk

# 경로 확인
update-java-alternatives -l # jdk1.8 path 확인

# 환경변수 설정
$ echo 'JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-amd64"' | sudo tee -a /etc/environment

# path 적용
$ source /etc/environment

# java 버전 확인
java -version
```

아래와 같은 결과가 나오면 됩니다.

```
openjdk version "1.8.0_232"
OpenJDK Runtime Environment (build 1.8.0_232-8u232-b09-0ubuntu1~18.04.1-b09)
OpenJDK 64-Bit Server VM (build 25.232-b09, mixed mode)
```

## jenkins 설치

젠킨스 설치 또한 [공식문서](https://pkg.jenkins.io/debian-stable/) 를 기반으로 진행합니다.

```bash
# 시스템에 저장소 키 추가 (OK 나오는지 체크)
$ wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

# 데비안 패키지 저장소 주소 추가
$ echo "deb https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list

# 패키지 업데이트
$ sudo apt-get update

# 젠킨스 설치
$ sudo apt-get install jenkins
```

## nginx proxy 설정

nginx 설정은 [Jenkins wiki](https://wiki.jenkins.io/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy) 를 바탕으로 진행됩니다.

먼저 proxy param 을 수정합니다. 이 파일은 `/etc/nginx/proxy_params` 에 위치합니다.

```bash
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;

# Required for new HTTP-based CLI
proxy_http_version 1.1;
proxy_request_buffering off;
proxy_buffering off; # Required for HTTP-based CLI to work over SSL
```

설정 했으면 ssl config 를 설정합니다. 이 파일은 `/etc/nginx/snippets/ssl-params.conf` 에 위치합니다.

```bash
ssl_protocols TLSv1.3;# Requires nginx >= 1.13.0 else use TLSv1.2
ssl_prefer_server_ciphers on;
ssl_dhparam /etc/ssl/certs/dhparam.pem;
ssl_ciphers EECDH+AESGCM:EDH+AESGCM;
ssl_ecdh_curve secp384r1; # Requires nginx >= 1.1.0
ssl_session_timeout  10m;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off; # Requires nginx >= 1.5.9
ssl_stapling on; # Requires nginx >= 1.3.7
ssl_stapling_verify on; # Requires nginx => 1.3.7
resolver 8.8.8.8 8.8.4.4 valid=300s; # resolver $DNS-IP-1 $DNS-IP-2 valid=300s;
resolver_timeout 5s; 
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
```

마지막으로 proxy를 위한 nginx 설정을 합니다. 파일은 `/etc/nginx/sites-available/proxy.conf` 에 위치합니다. 

아래에서 주목해야 할 디렉티브는 __proxy_redirect__ 부분입니다. 이 설정은 jenkins 에서 응답이 redirect 로 일어났을 때 그대로 클라이언트에 전달하게 되면 localhost 를 클라는 접속하게 됩니다. 따라서 jenkins 에서 redirect 가 일어났을 경우 우리가 원하는 url 로 변경해서 전달하도록 하는 설정입니다.

```bash
server {
  listen  80;
  server_name jenkins.kscory.com;

  access_log /var/log/nginx/proxy/access.log;
  error_log /var/log/nginx/proxy/error.log;

  location ~ /\.well-known/acme-challenge/ {
    allow all;
    root /var/www/letsencrypt;
  }

  location / {
    return 301 https://$server_name$request_uri;
  } 
}

server {
  listen  443 ssl http2;
  server_name jenkins.kscory.com;

  access_log /var/log/nginx/proxy/access.log;
  error_log /var/log/nginx/proxy/error.log;

  ssl_certificate /etc/letsencrypt/live/jenkins.kscory.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/jenkins.kscory.com/privkey.pem;

  include snippets/ssl-params.conf;

  location / {
    include /etc/nginx/proxy_params;
    proxy_pass http://localhost:8080;
    proxy_read_timeout 90;
    proxy_redirect http://localhost:8080 jenkins.kscory.com;

    # workaround for https://issues.jenkins-ci.org/browse/JENKINS-45651
    add_header 'X-SSH-Endpoint' 'jenkins.domain.tld:50022' always;
  }
}
```

nginx 설정에 오류가 없다면 다시 실행시켜 줍니다.

```bash
# 오류 확인
sudo service nginx configtest

# 다시 실행
sudo service nginx restart
```

## jenkins 서버 설정

젠킨스를 nginx 와 연동하려면 젠킨스 서버가 모든 인터페이스(0.0.0.0) 이 아니라 __localhost__ 혹은 젠킨스의 IP 주소나 도메인을 통해서만 동작하게 설정해서 기존의 __8080__ 포트로 접근하는 것을 방지하도록 하겠습니다.

`/etc/default/jenkins` 설정파일을 열어 __JENKINS_ARGS__ 부분을 아래처럼 수정합니다.

```bash
# --httpListenAddress={jenkins 의 ip address}"
JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT --httpListenAddress=127.0.0.1"
```

그 후 젠킨스를 다시 실행하겠습니다.

```bash
# 젠킨스 재실행
$ sudo service jenkins restart

# 동작 확인
$ sudo service jenkins status
```

## jenkins 테스트

그럼 이제 proxy 로 적용한 jenkins 에 접근해보겠습니다. 아래 사진과 같이 접속이 되었다면 설정이 완료된 것입니다.

<img src="https://lh3.googleusercontent.com/tgmdGYZZSl5o-_wv-JcdneSIv1GJkcCra6aNmYgO1Z3X7-jc16WZyy5GcQ-FDbFUbdYwpxYaYvK1M6HS6UjoJ1YakjFBOj19jN7Z401R2MYoCBSxxdDjWzV1U46t29wL7kfFDXzl3SZYR6ITPKTsiBv2YYkHR030K5qjMc8QtMpnausMFQZyM6icagWjLl6_mE8sAaKREJWVSgcTduI6kufYPAtgWMNOre3Py2J9XHTW9oz4x_8B6974trovCMW0EPlPLiS6ns56ZvhmFd2lDgkusjC-gEQaS477b3xXFsN7bbzawrUagZ4DBtyb9TE0pNpV3r0-Y5HbeXTJM6ajcqpJDKrotJiFYepKSn3-5KjvErghIT1lPh3jrYbQz7V4ywRUx0EU6q6_5Masy2d5Dr5Y3ooxBMOva9l74cz55VeXue0LT66QfBfk__yCdjzXs09OERVBY0zRiE_EQ2suthHSFdWVSdS5Q3929ZsEAkSgYnPPQfUSNSdpG3_Kf-peQ8-24Q053yXeTZDyDiqXxjVWArXrNTT5HrfEfyu1oq5uJID_l8Zaqka84bsgzhJpX44zOYXCJxmsG9_Epb5lVkNHKT238WGx21cNZ3KkG49tBi7K5CKov1kPk63ycL8pU0GJDasWVBocLEjo__2ReYzEQKkTL03NDz2ug8I9jLy8oek81iig8Y0=w792-h275-no" alt="jenkins-install-01">

그럼 초기 패스워드를 입력해서 완료하겠습니다. ui 에 나와있듯이 처음 패스워드는 `/var/lib/jenkins/secrets/initialAdminPassword` 에 위치해 있습니다. 아래 명령어의 결과를 붙여 넣도록 합니다.

```bash
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

그러면 아래와 같은 사진이 나올 것입니다. 

<img src="https://lh3.googleusercontent.com/l7GlMYFVuCGlyOVDWv2fQay3CSXfTeUziIbp8bmhRb7ovN3qOLfz1ez9a_HFMuIqsYoufgpK9COsHAJrOAxx7caDYmkTzZ3L-4pBlvS7IjwvfjT0OWdhHlZ0_fv2kGXeP11NVy_MXdupfSpG3jmG-4cUcXke654LG7NlyeLw9huP_hyTS-lCzGJ2lpGCC9mifzpRSYDoz2BoX-UE_Krxs1nv_9ddjoHerDs1Joc4qAd6Vw1mi0qezFi3xXcWX5o-nbZY9hd4o-DENDNcmNuwLYExbapXTO_8x1mAFUNp0DpnPU_RWps4XuC6U298sGLL9cvi_6kZXCKXYIjYdr9cN4OKxiDKH8j536NBwCFcYHUIAVzq2RiwylaCjSXB4_8NFK1IlF_15rcchGwYBmGAX60oNMUZrtKx3iWgBqCEO9q0b_5P5W1h7WIOkkiBaCwAi91wB4JrWxBdt1ia92XZlvYRUzaBXWdI4Qa_dhcAln2yy3WnggmOLW8KTnmyn9soLfCFM2DZD-cJlzZlkLRkizKNhocYyNl-u0gyBSu5A-WlaZ4H3hP4aM9tiIb1poKZhdR1v3S0e2S_vZaCTyQCVBQqa92WrBqslUbS91gyDXlDZ3OGZKytwtj8Hv0qME-WpFTYxe1Vyq_NJVOmXeY2EFxaeMvAl8SSP3xarafUY6TjxHJ-AhjJL8g=w792-h330-no" alt="jenkins-install-02">

__install suggeted plugins__ 를 클릭해서 기본 플러그인들을 다운받겠습니다.

<img src="https://lh3.googleusercontent.com/-yG3Jd4ojzOSCt9NuLQU-jCBDdbRnlTY1nM8R9WZTQ1KrNgQwv3yWAmiTSdjanCSVkT4tdI5IQ00tzWDdc3ucf9-mZDQEFfBrQfe3YTe-of1pYK-IdqUr45jEb-Lml9DTMDOObTomCqzpyQ8rxyLFzPckJZd-wvRGnR_E7XWal37aw2C-Nwo5klnD71hBHmcUugJYhqczcZX2RAVmkzc0J9HhZqN_4DKkkWlYQewLlx-L4J5u5A9FN4L-39tiPrjrMRMf_hQjp79IXosWebCZG4s0IJO0ra2WSxuBD7_ZOvL_Jl96YtB7h5_bBZ6l0GBTaraRj3zBM6lv6qg2u-tma8_f2mIZ5LQI69jVlLYvflrtRiRKFxbDUOxSVmJ-G_hY1vDdZmjJ-0nBo9pAN65pXR32QibY-rUtVw4lzw2g9pzcZf8Pz0q9dT6ykj4HaCyz95MPYU41jnjKuv6iFdqZ27VMuAgqEpbhZnMncU-TSNEyJA5wyvCEAEo-SRj0eoKYP6e0xFMqYoJ8XcDguEAUARjpZ4VfvDt5E1LNt4ugVlqjCAa4dXI-Uqi0qaoWHWurqEC32m9LpgAyrznQrx_2ncsOAkjFk7by7FWctuOj3J8F_yVWdGzyVlyp9v19NmcUVL03iRXnmDGbOj-6UKima2Bj8IvaNvK9xmKfbJZG-C1WhxNW8B0pOE=w1680-h1272-no" alt="jenkins-install-03">

기본 플러그인들이 설치되면 이제 초기 계정을 만들라는 화면이 나옵니다. 관리자 계정을 추가하고 넘어가도록 하겠습니다.

<img src="https://lh3.googleusercontent.com/bh5s-_vpZV17uZMnk3IovdB9jqRLI2B-Dz4TL3SKmmrYM4O_bXUtvlLMYhQ50yUxY8mKwblE438HAmB7VeDPHXLTmsTb_0S0aNvq_ZgJfmZpVefx2WZLzgUvUBQr7vJdvQ-oAspUTWeu6Yj2EwiGc40ObyoKIDEGQ83XX589KMLkwVDu7pa4HJvrwLb0NdMGmliMSLCQutNefN-Cia_9OTvxqYzhjEzyudr12ZcR-40Z96QKX1agCckHEP6sBiwjSOl-lOiGDiZ8eLLK7idNeha-epWlctMV0Swy8fJeZAIeUeK47cTu4ZbD97BE0Qez4B3dQxvqJqe19I_Ct6dptlk7NHICZXptEZygJtUusNm-gZC8x3npNYw6DKyBTwMRzl_-N4ZRN7K2yAsyKeeTaXj2hjHOAryxoCGI16e0jHV80ZMDhBRrV8tfwPARVqHds8b76_ODIOfoF3Bd_d74byryzOnpHX0StOhGRYAxU1DYnQ71QdOYwzcgvGJwL338C520NaSmkqsX1cbWVanqnGzfZ3BBwvafSuyzhzvAZ4SCz2alegH8wtV1reMtUzSu34AuwB6j9SsZF7loPzWIOq5cn_FhzhYb0zgQHRypZcP5-6ZtMuJyU3T0v_lebqb_xjv0dqLxV9gqTwHS3xuwjbqYUSKkGYYl7szyKrVPMnjrAFL9Hi9beaw=w1680-h1276-no" alt="jenkins-install-04">

그 후 domain 설정을 합니다. 저는 __https://jenkins.kscory.com/__ 으로 설정했습니다.

<img src="https://lh3.googleusercontent.com/YAmuF4qbVUwydu_FiFbCTErga6sT6t_akr7lRM3nx9EpOhtl_TuOBRjjScKHVgpFIGXKVz4UTFWxgGL2vV8ulz92yJymdUEQTp-TLBDjIE6KTNDnZ3tZeFLQOe4ALSGR1GrvZQsnXWylX4reMxqVGX7d8E9jEDNJQ8JjHkTLM2n-bIFJmWj-mK8jNZL6IWFwg2XrrvKsA00uEeEYJMNPcuxYFmG7kZBE4mIAzkES0KqmffuPbpbslDEl7Rh_eEOypF_IQCweLqbUeIDK13T8xlIAvZAw-kwYmdIyG601Fefp_Km6gpN78SI5MIzFtBluVqS-unpPwEonwDgvs_dMXqGzUjItkgEh34L5uKVMCJrsgNnN-USaQIVKF2_Nfa-j5RPctoi0fxWTXAH401UaDGnNpl4DI5cqmbqlDaWaONZ_mK9xpLQHGqpmeC1NQheeRPfb7iRVgRom8tZ-WRRycLSgZFV0gaTwhPvMs1HnIelC1OMlnHwWlMRbwf0q-OMn5tWSFsCYB7uQP80Myfm9KGHgJhwCO_8E55cSZDlMoJc9AR1Nlw7P2KgivogtVMfrMpwskHo5jx4WuHSCWd2voNssz32N4szGuQWiBMUhMoIwkf_HxaigFBFVeoyENEKsjdllhjpj5wdMfPD0_qmOL0sGsNLtdMka5KwBssB3zWp5twfIs3AimSE=w1680-h1270-no" alt="jenkins-install-05">

이제 아래와 같이 젠킨스 메인 화면이 나왔다면 설치에 성공한 것입니다.

<img src="https://lh3.googleusercontent.com/EXxF8QNIciERkeyF56iMLOUSqudMc9AHkB7jnnoUC6JTj1cKHhgr2eeQ8dsNLjvNBI2oFNVn71yTdA_X2pYYHGgooUonhJPT190MYvmkydI0gzp_C6o8TRwS30djh4L6wnMPllRu5gb1Xx88oyDhs2aWTpVe_uU1eMb_MIgzIkknF4l2bGuKF8JQmSxuf__KSmI9yWDVJxEY9QglQhpSQgIMUBdhYscLSupdvzvjHk5s5rIcpDlTQmaY2ZgErUiMi_w4FuT24icu51wXTWTyjoc_929IDrjVSgptdMA_HrRet3K0IYji8J0QeLs3GEQ0BXFu8Z5LKTFtepohK-vmr2zliIjqyl2GsY_YwvXbkn4zatwdwTcAnoaCqe88AtxqY-Gg8-ifvPbUCNFATXUsNMIUVuRrgN7mrBz1KfJg69vXIvQnKxI2hoAoMUWrkpB-FiPd7MbH7jlrdikr8nIvoHyHAd8oPR5eBBqgJk2VertYHP-qnQyGRTR8CHwrGEhZhsg6L45Ybtzwavz2JU7zydEV_6-oniDU5JzX10FgXgoRs5oHW2yYryIkW47451VhkdQUrHa9s829QtIG4b2n3MAd1C-5NG_QCe8wijjGNsWkTN_cERW1Lvsq8sDJAMvtDNYIVVX2K7Sl6UuCTnjIbOVnac8TMdVYNvDRtwvQNBe2_r6nW1dT_l8=w1680-h1114-no" alt="jenkins-install-06">

## 마무리

젠킨스 설치하는 방법에 대한 포스팅이 끝났습니다. 이제 툴을 설치했으니 잘 사용하는 방법만 남았겠네요. ^^

## 참고 자료

https://kkensu.tistory.com/58

https://wiki.jenkins.io/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy

https://www.digitalocean.com/community/tutorials/how-to-configure-jenkins-with-ssl-using-an-nginx-reverse-proxy-on-ubuntu-18-04

