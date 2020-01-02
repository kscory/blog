---
layout: post
comments: true
title:  "[Nginx] Load Balancer 설정하기"
date:   2020-01-02
author: Cory
categories: Web
permalink : /dev/nginx/loadbalancer
tags: webserver nginx loadbalancer
---

사실 요즘 __Loadbalancer__ 기능을 __HAProxy__ 혹은 __Envoy Proxy__ 를 사용해서 구축하는 경우가 더 많다고는 합니다. 가장 큰 이유가 __health check__ 기능을 이용하려면 오픈소스만으로는 안되고 [nginx plus](https://www.nginx.com/) 을 구매해서 사용해야 하기 때문일 것입니다. 그래도 nginx 는 로드벨런서 기능을 쉬우면서도 강력하기 때문에 알아둘 필요성은 있습니다. 

`이번 포스팅은 __ubuntu 18.04__ 기반으로 진행할 예정이며 sample app 으로 express app 두개를 사용하도록 하겠습니다.`

`이 포스팅은 nginx 설치와 proxy 설정에 대해서는 설명하지 않을 예정이니 궁금하신 분들은 이전 포스팅들을 참고해주시면 감사하겠습니다.`


## 데모 앱 띄우기

nginx 를 들어가기 전에 demo app 을 다운받아 각각 다른 프로세스에서 실행시키도록 하겠습니다.

```bash
# 데모 앱 클론
$ git clone -b demo-nginx-loadbalancer-app --single-branch https://github.com/Lee-KyungSeok/blog-sample-projects.git

# 첫번째 demo app 실행
$ screen -S demoapp-1
$ cd blog-sample-projects/demo-app-express-1
$ npm install
$ PORT=7000 npm start
# 실행 후 "control + a + d" 로 빠져 나오기

# 두번째 demo app 실행
$ screen -S demoapp-2
$ cd blog-sample-projects/demo-app-express-2
$ npm install
$ PORT=8000 npm start
# 실행 후 "control + a + d" 로 빠져 나오기

# 두번째 demo app 실행
$ screen -S demoapp-3
$ cd blog-sample-projects/demo-app-express-3
$ npm install
$ PORT=9000 npm start
# 실행 후 "control + a + d" 로 빠져 나오기

# 프로세스 확인
$ ps aux | grep demo
```

이 앱은 express generator 로 간단하게 만들어진 앱으로 아래 사진과 같이 단순히 First App, Second App, Third App 이라는 html 페이지를 보여줍니다.

<img src="https://lh3.googleusercontent.com/JGndeF_7s9q2rD5h5GYhPx3cZpxmtSyx7WHDyUzLZY9zm28A1BS6mqRxU7cJpD4mFA3G1jzoZoQ5hDdNsvOEUaf7o9F_aH48xwmBZdsCq79nCF-BBMK_V4if166Wbly9tFWSlENrq0KFUqyE4_PjwXEhz1bgd13htfUenYVQiUp5kRQH5RceewmSgKdxWDTNec4ZmcFOXHyGpujYYCn-4JFSEW-s1tiyUCf5ACBfFISSbLdNvgY7iqP-60XcV6rH79IBXymerv3VC3S2jDKwYB4R5VJmzj-VVYvki4_BkjF7cyyNFQwMgrWYkD1gRT3-hRTcJJULVsYwuKUs14aZFKcbDTPg8FnP7vXuJWdFAMUzH0_jVNGHIHLONcU1uKa9KDlQtHousfGiHLRDnkyFclpLj7MG6pUDTCGUx964oEYiHx5usXqPodQzIUjQt7NEK9qTo5duDs7uDpb9XMH4MKfBkqXXTjcM9Yw_Srwte0FHEWbsLEC7jWAMEGdKBcBrDOebWQeB-LbmsSGEeKuWWF6Osrzj0HH1Rnoht4O96aTgOzwnO8HliXzXrZ44HTeWSdgi1QFLLc8uYVz9U8lTDYtt3o0mNSLjjMxTdNuniBlH8E8kyJWPF-_sdmHa9Arccxahp0JX3ZxM5oGCzXpVgQuXWmdi_h-EEmwg-wztI7_HMd9CLhdS1tQ=w1680-h456-no" alt="nginx-loadbalancer-01">

이제 이 앱을 이용해서 first app, second app, third app 으로 로드 벨런싱 되는 과정을 설명할 예정입니다.

## nginx 설정

nginx 에서 로드벨런싱을 적용하는 법은 간단합니다. __upstream 블락__ 을 만들어서 어던 서버로 로드벨런싱 할지 정해주면 됩니다. 즉 저희는 __express-app__ 이라는 upstream 을 만들고 각각의 앱에 연결한 다음 __proxy_pass__ 를 이용해 __80__ 포트로 들어왔을 때 이 upstream 으로 전달하도록 하겠습니다.

이전과 마찬가지로 `/etc/nginx/sites-available/proxy.conf` 파일을 열어 내용을 작성하겠습니다.

```bash
server {
  listen  80;
  server_name {nginx server ip 혹은 domain 설정};

  access_log /var/log/nginx/proxy/access.log;
  error_log /var/log/nginx/proxy/error.log;

  location / {
    include /etc/nginx/proxy_params;
    proxy_pass http://express-app;
  }
}

upstream express-app {
  server 127.0.0.1:7000;
  server 127.0.0.1:8000;
  server 127.0.0.1:9000;
}
```

아마 두번 실행하시면 아래 사진처럼 First App 과 Third App 으로 로드벨런싱이 되는 걸 확인할 수 있습니다. 한번 새로고침으로 여러번 눌러보시기 바랍니다.

<img src="https://lh3.googleusercontent.com/iqRe4uWj5t0045phB35ge0EXFh-xuOmwkpyxGlQmUW_psCMfO1gaEODgQ8xfTOhXkkveQ-Xx6S1F8fEvx2rQK5wedbrwoWVZznZJbTXzqqzbbXPXjfBB4pYgh8wxusFUJB7NEd-690kt0N7nCVSCmfOgwZI3PO5P_OjoAslV0HgDDSxfGxjkYwOQB9oPCyQXEoHItFYj1dcPokBJ9d7mUapJ-VTKma7yYIWr8hmh1hw4niPZ5Ek70tCBoJFbb7Gi_fZzaKxMMq6KIE9SaWel4jiY3CZUxZqNYXyvM9UYJWD1MjXG7oU1QrbGvoj5W0qMTaTobHgtaExqC_yXZWEAJ8j_gykht4X6UVvKK9shEHQrNdv1aAZttp70ADmemEnzJzTfs2FLkJe_floa_mYzrv6GigebcWmogZPGjaanOGo7U5obAAM14vbUWmB4wRHUZ6_tASGWqpGKK8vb1PCuMlu4_zsiqqj7WDWMq6kP9vxBBJDHsm4524DR2gXZm2LcLNxgnjR6HAPi3mdkYvVO22KeQZulK7gZo68twUB9wQPc9TaHCvUAuI-FzVliKRdmS9g_4p1SrQLISG0EtBGwZTefdCI7H3YanK7Wp0Hu2b6NcHI-P_e0jGQAluluhVBad_6A9orH5qRoG0PCBHBazYbdqOfzL50cQExpNot9c8U9KqAfHY3v1UU=w1680-h512-no" alt="nginx-loadbalancer-02">

그런데 뭔가 이상하지 않나요?? 왜 __Second App__ 은 건너뛰고 __Third App__ 으로 로드 벨런싱이 되었을까요?? 

사실 이상한게 아니라 아무런 설정을 하지 않으면 __1. 라운드로빈 방식__ 으로 진행되는데 저희 express app 은 __2. 정적파일__ 을 가지고 있기 때문입니다. 즉, 로드 벨런싱이 잘 되었다는 말이죠. 한번 아래 사진을 확인해보겠습니다.

<img src="https://lh3.googleusercontent.com/fxcCmYpjojxbnHC439wxM0O87-svONpm7OymiQfNcR4lEY8v9Ih6szdhhMBEMZeViC24Cut_GW2Fnl-CtZLH0-C_JeEPdComqKoXlwxkv1Ui7aHvzfSBQRzbe8KsoxAPcntP_AkkXpHC5AxriAdcH0R9yN2d9zfwlHIgkqR0H8eNKPAv06ooenKTPrZnF1e2vim2VPt-c2xbQhamHiTJFL1-phiSyIlx2TuyQJFk4zK74BQmbKR9BaCCcyMupAbHv27pLuaRYKFFMGOOklZV0AuJSa7oiV_QYEv8y0-KmHs2fWXoB3IbM23wGYTONJeJmTKiODOq1asVQC7qw7Fiu0VCOCpk21H4QrR16buJqxJdjlKukUqKQXyjlYtDDFjWaiPAIOFCA-JzJNBQJD_YIi_6WKW16ZUWXgbLXpmXN_Rp_6EbzExzbf9ir3FZK2UfXTWv0DiUP1eUbcxQeX_Z2oAxDQevPS549CpT5Myc6wqMYizv1mddnGCcteaCAVv_rIJrlYjwyqiAM0ntRKvBdXrpM_NFX_wQbpMlmvvbJXEZ5aiR4CSKY57YKqBlkyBFwFQEoKCmAb3y0WeBv9vYfw7jy5pw11TXvHXKkVj1qSki0D-4QjACQXVwPU1gbDGwQg3T0wjVYiA2EiJ4og6KIAEqfidq7CJv_IUZIXYLp-zOJCq-OXKyKWs=w1216-h310-no" alt="nginx-loadbalancer-03">

그림에서 설명했듯이 __처음 get__ 요청에 대한 응답은 __demo app1__ 에서, 그다음 요청인 __get styles.css__ 요청은 __demo app2__ 으로 전달되며, 로드밸런싱이 잘 되는 것을 알 수 있습니다.

## nginx LoadBalancing config

이번에는 nginx load balancing config 에 대한 설명을 드리고 로드벨런싱을 조금 더 정교하게 수정해 나가겠습니다.

이전 챕터에서 아무것도 설정하지 않으면 __라운드 로빈 방식__ 이라고 말씀드렸었습니다. nginx 에서는 라운드로빈 방식 외에 추가적으로 두 가지 방식을 더 지원합니다.

1. round-robin: 라운드 로빈방식으로 서버를 할당
2. least-connected:  커넥션이 가장 적은 서버를 할당
3. ip-hash:  클라이언트 IP를 해쉬한 값을 기반으로 특정 서버를 할당

저희는 __least-connected__ 방식을 사용하겠습니다.

```bash
server {
  # 생략...
}

upstream express-app {
  # least-connected 설정
  least_conn;

  # IP hash 기반으로 하고 싶은 경우 아래로 설정
  # ip_hash; 

  server 127.0.0.1:7000;
  server 127.0.0.1:8000;
  server 127.0.0.1:9000;
}
```

여기서 server 디렉티브에는 특정 옵션들이 사용될 수 있습니다. [생활코딩](https://opentutorials.org/module/384/4328) 에서 정리한 표를 가져왔습니다.

- __weight=n__: 업스트림 서버의 비중을 나타낸다. 이 값을 2로 설정하면 그렇지 않은 서버에 비해 두배 더 자주 선택된다.
- __max_fails=n__: n으로 지정한 횟수만큼 실패가 일어나면 서버가 죽은 것으로 간주한다.
- __fail_timeout=n__: max_fails가 지정된 상태에서 이 값이 설정만큼 서버가 응답하지 않으면 죽은 것으로 간주한다.
- __down__: 해당 서버를 사용하지 않게 지정한다. ip_hash; 지시어가 설정된 상태에서만 유효하다.
- __backup__: 모든 서버가 동작하지 않을 때 backup으로 표시된 서버가 사용되고 그 전까지는 사용되지 않는다.

저희는 __demo-app-1 에 가중치 2__ 를 두고 __5번의 실패 확인__, __실패 후 30초 응답시간 설정__, 마지막으로 __demo-app-3는 모든 앱이 죽은 경우에만 사용__ 하도록 하겠습니다.

그럼 설정값을 다시 바꾸겠습니다.

```bash
server {
  # 생략...
}

upstream express-app {
  least_conn;

  # demo-app-1 에 가중치 2 부여
  # 5번의 실패 확인, 실패 후 30초 응답시간 설정
  server 127.0.0.1:7000 weight=2 max_fails=3 fail_timeout=30s;
  server 127.0.0.1:8000 max_fails=3 fail_timeout=30s;
  # demo-app-3는 모든 앱이 죽은 경우에만 사용
  server 127.0.0.1:9000 backup;
}
```

마지막으로 저희는 proxy 방식을 사용했는데 이 때 에러처리할 항목을 추가해 주도록 하겠습니다. 이제 최종 config 파일의 항목은 아래와 같을 것입니다.

```bash
server {
  listen  80;
  server_name {nginx server ip 혹은 domain 설정};

  access_log /var/log/nginx/proxy/access.log;
  error_log /var/log/nginx/proxy/error.log;

  location / {
    include /etc/nginx/proxy_params;
    proxy_pass http://express-app;
    # 에러처리 항목 추가
    proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
  }
}

upstream express-app {
  least_conn;

  server 127.0.0.1:7000 weight=2 max_fails=3 fail_timeout=30s;
  server 127.0.0.1:8000 max_fails=3 fail_timeout=30s;
  server 127.0.0.1:9000 backup;
}
```

완성되었으면 nginx 를 재실행시켜 준 뒤 접속해 보도록 하겠습니다.

몇번을 반복해도 __first app__ 과 __second app__ 만 돌아가면서 보여줄 것으로 예상됩니다.

그럼 이번에 __backup__ 을 확인하기 위해 실행되고 있는 두 개의 앱(demo1, demo2)을 중지시키겠습니다. 포스팅을 따라 오셨다면 두 개의 앱은 서로 다른 프로세스로 띄웠습을 알 수 있습니다. 즉, 이 두개의 프로세스를 죽이면 앱 실행을 중지시킬 수 있습니다.

```bash
# demo-app-1 프로세스 죽이기
$ kill -9 `ps -ef | grep demoapp-1 | awk '{print $2}'`

# demo-app-2 프로세스 죽이기
$ kill -9 `ps -ef | grep demoapp-2 | awk '{print $2}'`
```

이제 다시 접속해주세요!!

<img src="https://lh3.googleusercontent.com/483tvQ1AKJ6SFKRLNJ6elzR-A_FvQbBNtHNLkCa8G2sp2HFLz7o70PjJAa3nf0pOYscIVkcgWq8i_nC3GLElNA33JHDuaPwXx1e2EEfXs3qfdqxkc5CgcR745YdpPGzoZnvLOwK7O84yyKxOMVhs8uiM-QNvTKwFo7zYNVIfNunbP6zJgFnvxjqO9vBN_S-Iy4uxwW732kEHp-lVTWs136130yG1DDfXyvLmxm9VwmkNpICs2nXLGjAZ-O5N5zyh4e5SMsSjpxMpvhAxXFiqB2uVo1gPTCPSXSpghB1arZMlEyQ6zed9-gTOBnVE87scq-nq3yTJ1DoHiY74aMRWwVOgEi-MA0QbdvdvKd226B0L0pzwfhJ5e4nJc8xxPAcOydAVhZvbLAML8v2GKnU5fddckwEGumYJi5xl3ftvz57-AaxhriifJ6BfIpyVeZYD3ar7-kqADHl71SOpDJEA0U6vdqTeNAUXH8fbEV5N7pKgfxNTeimhHeZZsFvGExdJFfxROAZ_2i5qJj7i7LlMdM6chcZP6OLNIOfJf0fIEAWZrQ24tkROFRCyAmV3JISksM-vYo4HW0ryoY1q0kBixs6x8zFNwkSeTk0eHJJbKu98RacaHerCkuBJXAQhxfgZ_hrvQ2YkeDsi3RebErNRo8KAXROey1EITDqub8xmnKZaubIZrdFHMwU=w1680-h550-no" alt="nginx-loadbalancer-04">

아무리 접속해도 위 스크린샷 처럼 Third App 만 나올 것입니다.

마지막으로 connection 이 연결이 끊겼는지 확인하겠습니다. nginx 설정 할 때 에러는 `/var/log/nginx/proxy/error.log` 에 저장하도록 했으니 아래 명령어를 이용해면 어떤 에러가 발생했는지 확인 할 수 있습니다.

```bash
$ cat /var/log/nginx/proxy/error.log
```

아래 사진처럼 connection, upstream 에러가 발생하시면 저희가 생각한 대로 작동하고 있었다는 것을 알 수 있습니다.

<img src="https://lh3.googleusercontent.com/u3xUkBqGOFLzFAeJG-Hb6LmOevxYmAw6Kkg37KHz-1MtCvEPqLmT1EY0RxGf2lUUht1r9QvJNyuwEbem9orFTL4z-E4Ze-QHhZWB8f-CQ99vh5CdYeAfJIJocmLnWrCusXi7PpDAxTtkfq7jWW3nV6e_1nfUwtUIoMTCmObDT5zbzmMki5YiCnaobpMQV6Xd_ZsL43Ahoz-wtA5xcqIXsocebqIoDTqIN0cBga1tdVRo6jTCKCUnDVJ68J-TlHWcAlK8hGlY5DHhU5uXFkfEl3NEYME9hJJuDOCV_dI3NLfeLV7lw0frMYGlKze_sHNWX6zlPfxW3Si534-fLNCXXJwZxTTyEqdNpMXes0PhyO6u6E1JPFH3J2iIBMNJ9oitHdmpBiRdDPdLW07AW1_2tEMKHoBXVSECmuZYvtTmCjqnp9vkwuwgt5mD3Jz-G4ZWZ-KDo82QzFwemt2F8c9zjM8qG8TxnLxX_oJ76C89BgL1exyN_EnxS_X1naCTG3Yp5xKU4oKEGI3a09nJsD_jzSo-wg6yC_J7DRuVfTtvtNzeVWEKCrC9Rf54lhAJeqIU9RUK_5twEUjuZ9H-E3mMeE7HDmGfZKDJ7O_9xm3UG-P-vgZXe5LG_9jaWUlSGiJh1Xu-FRVjbOwx8KgYThuTAQPWJzDAiTTvWiyk1zyK7-ie6iH0LQK2VfQ=w1680-h362-no" alt="nginx-loadbalancer-05">

## 마무리

사실 어떻게 보면 이번 포스팅은 proxy 서버구축의 연장선상에 있다고 해도 무방하다고 생각합니다. 그래서 그런지 이번 포스팅은 조금 빨리 끝낸 느낌이 있군요. 

이 강력하면서도 쉬운 로드 벨런싱 기능 덕분에 __kubernetes__ 의 공식 __ingress__ 도 nginx 로 되어 있지 않나 합니다. 만약 이 설정방법을 모른다면 kubernetes 를 이용할 때 더 헤머게 되겠죠? 그런 이유에서라도 한번쯤은 이렇게 기본적인 부분들을 보는 것도 도움이 되지 않나 싶습니다.

## 참고 자료

http://nginx.org/en/docs/http/load_balancing.html

https://www.lesstif.com/pages/viewpage.action?pageId=35357063

https://opentutorials.org/module/384/4328

https://seokjun.kim/haproxy-and-nginx-load-balancing/
