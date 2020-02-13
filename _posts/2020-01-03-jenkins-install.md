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

그 다음으로 ssl config 를 설정합니다. 이 파일은 `/etc/nginx/snippets/ssl-params.conf` 에 위치합니다.

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

아래에서 주목해야 할 디렉티브는 __proxy_redirect__ 부분입니다. 이 설정은 jenkins 에서 응답이 redirect 로 일어났을 때 그대로 클라이언트에 전달하게 되면 localhost 로 클라가 접속하려 하게 됩니다. 따라서 jenkins 에서 redirect 가 일어났을 경우 우리가 원하는 url 로 변경해서 전달하도록 하는 설정입니다.

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
# "--httpListenAddress={jenkins 의 ip address}" 추가
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

<img src="https://lh3.googleusercontent.com/7DiO7HqSpmBX8QsUU3KA2V9MX60snQsW1mrFBkN2AjkKbcQ3bCer24Unpq3apnC0x0fOAuacOGT997vMIhjfHqRfo9odK1m6S-qNN2vpHTXpP1Vf7h1GsJ7fiH1DqWV1b1dqOET37dE14e8W3PGuLSKCH25kYcOzO1unk1X9tv8J69UjniGXzJJl8Nh2GlG7YabU9DZk9ENwQJeuH4irdV3EuHGRBWi9R4fWPIpBGAyGhfUPcLKVWwTnRWCFNevGqyEXCLODyPruobLlIOFedBtxsQq9ONhYANwMihWzPH0A5SSwd5Q2Io4q7gxIAAIfb-88XwRSvYWip_wdPGc43CF3VI6Bau7_duuqweD1sW24VWHJXHBZA6kuf1iR6oXwb_h5eMye1kRIHtn-tgtOnTMUffaJtDXDXIRGkjoDCylh4e3DmsBwojAw2Z8VlMSp5-vtVEVoyfYVBZtYNrNVqUYTqYyJTez611q1hcjRsuM6PRpunCl5hNdmPzvbqMZjHOWgda_7wjOVjdNttnnLUlJw6mLyIvGcFBhOcxU2FGbgY6gZ15S_DBye7HPCLLSrbgqPBuWo2p5QEDP5RaT8PjtSrytneeHgb9eDryKB-tTLMbpgYUUyZS-ayxmZtP1nCB7LN_MFzF8cPA2LdYz2RO30vwzG1YnTrYP8FmXAI02a-CMx84VMk4OxzLlb6barP_dE0OSN3ZHCBB8DUNvHCCiCy5INonuUruAbLiku4gSX03CA=w1440-h499-no" alt="jenkins-install-01">

그럼 초기 패스워드를 입력해서 완료하겠습니다. ui 에 나와있듯이 처음 패스워드는 `/var/lib/jenkins/secrets/initialAdminPassword` 에 위치해 있습니다. 아래 명령어의 결과를 붙여 넣도록 합니다.

```bash
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

그러면 아래와 같은 사진이 나올 것입니다. 

<img src="https://lh3.googleusercontent.com/en6ErfvRQvvSsY9Tjdo0rzqr0sYTvS7U2ijDJCjcz8kzpnVl8xi9X_SxcANGSR2PFN5YaTogsCg2PlDIikYiMfqcIAVHkx1BkJ1u2Lo-o72it8kcznMtI0b4fl4ZclFVZfL61qdV_ASg1DJafQBZIN8zCLLT2HuJ4I5ATPeHq6SPxLOYPkSkFA3gWGM3NshrAg7Gbi1u4d3YE16ri9bhGUC64lPD0Rk01lJAx9cd1rEvH1oQ_mCNFOzSAp1H016dWoRxAiiRrC5cS7AvFWMVIbOd3SSMm182pDxu1ykAE90jyYhg6ek2tmnx-Tf0k58GjcxHh4lDcaaWG5ZRAo77yEelQ4e2cs9u7yow5RTQuVbvEe_-XB8efCgZFnuaxVdVgWAgMabUF-zZJ_FqcZbn4_yuVf0XofDVVI5_TFKuPunQzMCgh647o6mG3BjXt6ElasiOXEt6owgomsY6o4RjqCeYiJ5cG2-RQbPYb9Sk8Bd1CtxQuPSLpnqkrew1PfQYCtDTYyljMX7nKIObpo6yunBCvHjb7vHdZicjyVEEPculmon8UpgcI8ckMKkPfI29olwUy_fEjP5WK1QpSJN7Kkpa1_rxSWP_33w79ceAa5WsDxS1NJqgdn2wDYu-6yLhJNYMS9gQHtARTaHMuqV6dYFbUE8oHoJ4rONaCYHxk74qRO5jBcJ_XgT4F7dBZXFhALyO0j0MsnDdLQr7p7kikZlROeRwfsD-GzcABSZExh9qL24v=w1440-h600-no" alt="jenkins-install-02">

__install suggeted plugins__ 를 클릭해서 기본 플러그인들을 다운받겠습니다.

<img src="https://lh3.googleusercontent.com/Ra4OcGG46Jhik6EK2SA0ZSpbg2DmSJHDtPcIhGrb5326VOML-2ZmBx1KpSY0GfV2pt5QEAriQlBtS8Y6xMuC23UBAtk2GNI7UouwdoGICW6HgEBomu_UBzYzoLNws887VgVrp9Prb17Nafi0LcirCtFtwnVcd_F200t-YSq_4dsg61z0pLWVD7-zbx1CZMD7J9gWodKs_9HQ1uFDyF2LIWoaRJ4UPqeMw1qEqy6xeN9EEGvKgTbkBVMkHvjeZPX_Ieb6Y2FHQzU8HA0xgVwx6ag_gNdt95jGgar6gFYAfCAIGD8xEt3A_978H7J0yhM4GEPBwsh_FkUxtBJ5tp6v4H_eZ68TVn_V0x5khcOxMjMfaTwpRG7x6pvLYWyoH_sBBhUcpKxcZM6k9s1wtp7-42tsI8CyO3xr3QBsN-XSb6tYbJKPXkK4djJYpONHRXHBsCXlm9_AgNtkgIZhybmSmi_kSEFAQAkTO4nbq35OHRefteIZFD63FXC03EC5s9J4pshf6WQLjb5ar0dKg5SQGXxhbmh_fgw4h8AZT0IVR9aTSw3hldogdWdhVz7RabDOpLK8ChqTKemOfht6aTGbM1gUvP4g1ltHWbfutxbVcpiGqFlD9hL3q7-EPNaKBlRgvCB0_ysmQz9WG-BslMe-OnMrbhkfLG8dR3Bqv0J26zo2Nk6I1cRuOISJSqqcKGVoS1-ngRAgIBcWvbWL7cAEu3HocZyjmWsmImo2C-0XkaAv6F_-=w951-h720-no" alt="jenkins-install-03">

기본 플러그인들이 설치되면 이제 초기 계정을 만들라는 화면이 나옵니다. 관리자 계정을 추가하고 넘어가도록 하겠습니다.

<img src="https://lh3.googleusercontent.com/zOIU5vxKeNu5bnemqKB_L18QyW26P-OT9hfjPeEtgZu9rKkJDFoFMK-0eGvk4kkkm5EupbUOeRr-w88WlNzMW44dkVcFwLaPhSi_CnrefppOomM8sKJAd5_r3ifbAEjPERVZIFz-NJxKeILY97XrGfo4yTJvdrhm25b6H0og3uqNBJua4_Qn-UqDCwy4KuiUAOPN5bg-33A7GpTmLLge9T8GbyqNlha_apcF3DJ1pttmlwzKA8az68P064RoVE2S5Y0-Wm25QZhKByAMD9mElO5zXUJ5U0alg9sTo7cUPpg9lcPXsVtkhXZ8MRYdgMVTVQxG21zI0z7LApt9qDU0MeR7ahSfPhH15McOnBq8EzYCydGzmqRbvXqpGiadyKnheaNfgLEXllAUsaocMkzruLJyrYj0Eu6w1pxoz3jR_TyXOpg6AlglbJILkAoLFuSk2EDWPDJPW3QF7rWWZDU1KqTEHG5DFS6HNPiQ2q5OOIcOFSQ_4DHbXXN1BEiyENMlj9R12_f0BV09MD9L7tIfZg8Y_tkd8fYbicqV4-gphPriItrWoN-aVlJkCsqagNPsC_3U2TPGMoArqkMTyqZfNfYmzuVbFu0dAAyS1vno5ervJLPvJ6rH-8TSoc1cbmuew9aKJzSWe_dwUu8874yH_u2qjpy7wwEPAqXKtWDRFz7MP2LF19vUMMajRVM16vwhkwJZPSb-oOp8xhwnw_QZvgJCPWXxyVPaW_OWCSNFf16TuRZl=w948-h720-no" alt="jenkins-install-04">

그 후 domain 설정을 합니다. 저는 __https://jenkins.kscory.com/__ 으로 설정했습니다.

<img src="https://lh3.googleusercontent.com/sVMfyK350SG186F92C8i8EJSqH243ZIGg6CtJvkTc0--k2vFzkwX9B-O-5qaQNBFzz1fZW4dTzm3Z3iQro9OU4uYsKYvrqT8rEkcM7s78R0gNsN24nqkOm3dWMK2daqVr9jWbRZebBKPdDYBDJgch9zIcLbNGM-3PAvlppDi6zDlP8C84wOPZ75H3vwQZ062jpLXbxlSOn8ZcDUbjGxRmZHbBvBzFP1c9N_wXwS1ZHXhIanS0MVTlLPOkDlGL9rdcix85KErF7vLEvPWbxDJmDSMXYcPsVtVRcHQLiRKjY8aToJeLNAKCxQvy0EutRGOeI91d3Uum_6xYRSwDxoQ04MY0ZCRxx6tMTUHMmFF9I44tiLIbZkl2npmQqQc208u3cMWXVHATuxCjlOZcfL99mO5MY7MWb840vmjHLQfgvI_nPXMQ6kww0t4PdpFS621oi4tQsfXNp5pmJoRLI0w5pBbuy1oPdeGe9oN7sKAv2BJckDbOglpD90g2bLZCE5euANbIyvJwtDskqgaLo1hQu6ySkVHxv2UqI3uVC2k-xRvK8osrzC--TzJrDyAABNn0E6BqxmmEk6A3ADxxF1daVMyq2JB5KSgwLXcUIEYrW-F--LOJHt0KS2KIdH10G7vS2BnfmD_cqPRiy81N-tUlmxxpPwPo7sUtCFQbUJy53wxNqKmDi9kXkayvfPFNqVqrAWKDLnbGxXKnK4v5OZENT_RCNVfQiNC-rq_p3-BJGrkInMk=w954-h720-no" alt="jenkins-install-05">

이제 아래와 같이 젠킨스 메인 화면이 나왔다면 설치에 성공한 것입니다.

<img src="https://lh3.googleusercontent.com/SByStvg_CSV-XBufULPplEsTgnhmCmdlg3L9nDw6NqNWS1CB5vQCGrlDIsY5DZ9eNEr9k7WrThw_SP9duONV_Q4dlq37t9hN5jfdi1VRxEMW9aRimiZpdMEtsjhINSzumIxcAX4cTlNPHps5m0FQH6Kfjx-stkuX9iZNYuUBPM5d1aoRgW9yXXguzcHcsuOzIq-HjR7H-rMdGUyqUCAOtwHHSewhVApXmOBGeaswchsajkrVDYNaWz1F5pEOutx6iUKwahzMIxt3BMc-MaqWewyqVNs-oV7ZMXHBwGUvc3xouWaOfUPD-blu_DaXJWDv8xzuCz-MenHwyF6fJxbcJzkRV1bj6fjPka_YPhV1yVO7sw3Tp_6yEwsaU9hSYO-o4Ii1RLqDlRaCt6el-T7hTNTSw7-SyGbENMyaoA0HFmrHlrtY3Z4jeHh_ElrsJXmLH5x_zOqCVZxm9IZwjSnAXcR7H0J3rjhXdvhGqwsU7P4MtOEbhzxRszmrn_xDwbrBDYMCL9KLerJ7c3O5t0za696y8hdE2lRmEEYuYDaTOAQWMIQ7F6BgaE52a29IaLQr0YL2Y3yu7MGVxubugNKE6f_TtrdQjrhzgOGdsgI2rFIKCKnexx--GB2Cm5QoOdE4_CQIW1lbrVZC5KmZZtLOzW1mcLsm306RXjLPczD_2jPsjL7W_e5I6STZw3ys5ASC5r5izMld-4SyhJmYM-UlmCUkuCFQlkOsblHHyIHWk_hFH7m_=w1087-h720-no" alt="jenkins-install-06">

## 마무리

젠킨스 설치하는 방법에 대한 포스팅이 끝났습니다. 이제 툴을 설치했으니 잘 사용하는 방법만 남았겠네요. ^^

## 참고 자료

https://kkensu.tistory.com/58

https://wiki.jenkins.io/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy

https://www.digitalocean.com/community/tutorials/how-to-configure-jenkins-with-ssl-using-an-nginx-reverse-proxy-on-ubuntu-18-04

