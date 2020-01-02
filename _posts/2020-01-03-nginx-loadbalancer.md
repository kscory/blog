---
layout: post
comments: true
title:  "[Nginx] Load Balancer 설정하기"
date:   2020-01-03
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
git clone -b demo-nginx-loadbalancer-app --single-branch https://github.com/Lee-KyungSeok/blog-sample-projects.git

# 첫번째 demo app 실행
screen -S demoapp-1
cd blog-sample-projects/demo-app-express-1
npm install
PORT=7000 npm start
# 실행 후 "control + a + d" 로 빠져 나오기

# 두번째 demo app 실행
screen -S demoapp-2
cd blog-sample-projects/demo-app-express-2
npm install
PORT=8000 npm start
# 실행 후 "control + a + d" 로 빠져 나오기

# 두번째 demo app 실행
screen -S demoapp-3
cd blog-sample-projects/demo-app-express-3
npm install
PORT=9000 npm start
# 실행 후 "control + a + d" 로 빠져 나오기

# 프로세스 확인
ps aux | grep demo
```

이 앱은 express generator 로 간단하게 만들어진 앱으로 아래 사진과 같이 단순히 First App, Second App, Third App 이라는 html 페이지를 보여줍니다.

<img src="https://lh3.googleusercontent.com/JGndeF_7s9q2rD5h5GYhPx3cZpxmtSyx7WHDyUzLZY9zm28A1BS6mqRxU7cJpD4mFA3G1jzoZoQ5hDdNsvOEUaf7o9F_aH48xwmBZdsCq79nCF-BBMK_V4if166Wbly9tFWSlENrq0KFUqyE4_PjwXEhz1bgd13htfUenYVQiUp5kRQH5RceewmSgKdxWDTNec4ZmcFOXHyGpujYYCn-4JFSEW-s1tiyUCf5ACBfFISSbLdNvgY7iqP-60XcV6rH79IBXymerv3VC3S2jDKwYB4R5VJmzj-VVYvki4_BkjF7cyyNFQwMgrWYkD1gRT3-hRTcJJULVsYwuKUs14aZFKcbDTPg8FnP7vXuJWdFAMUzH0_jVNGHIHLONcU1uKa9KDlQtHousfGiHLRDnkyFclpLj7MG6pUDTCGUx964oEYiHx5usXqPodQzIUjQt7NEK9qTo5duDs7uDpb9XMH4MKfBkqXXTjcM9Yw_Srwte0FHEWbsLEC7jWAMEGdKBcBrDOebWQeB-LbmsSGEeKuWWF6Osrzj0HH1Rnoht4O96aTgOzwnO8HliXzXrZ44HTeWSdgi1QFLLc8uYVz9U8lTDYtt3o0mNSLjjMxTdNuniBlH8E8kyJWPF-_sdmHa9Arccxahp0JX3ZxM5oGCzXpVgQuXWmdi_h-EEmwg-wztI7_HMd9CLhdS1tQ=w1680-h456-no" alt="nginx-loadbalancer-01">

이제 이 앱을 이용해서 first app, second app, third app 으로 로드 벨런싱 되는 과정을 설명하겠습니다.

## nginx 설정하기

이전에 설정했던 것과 마찬가지로 `/etc/nginx/sites-available/proxy.conf` 파일에 적용하도록 하겠습니다.

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

그런데 뭔가 이상하지 않나요?? 왜 __Second App__ 은 건너뛰고 __Third App__ 으로 로드 벨런싱이 되었을까요?? 사실 이상한게 아니라 아무런 설정을 하지 않으면 __1. 라운드로빈 방식__ 으로 진행되는데 저희 express app 은 __2. 정적파일__ 을 가지고 있기 때문입니다. 즉, 로드 벨런싱이 잘 되었다는 말이죠. 한번 아래 사진을 확인해보겠습니다.

<img src="https://lh3.googleusercontent.com/fxcCmYpjojxbnHC439wxM0O87-svONpm7OymiQfNcR4lEY8v9Ih6szdhhMBEMZeViC24Cut_GW2Fnl-CtZLH0-C_JeEPdComqKoXlwxkv1Ui7aHvzfSBQRzbe8KsoxAPcntP_AkkXpHC5AxriAdcH0R9yN2d9zfwlHIgkqR0H8eNKPAv06ooenKTPrZnF1e2vim2VPt-c2xbQhamHiTJFL1-phiSyIlx2TuyQJFk4zK74BQmbKR9BaCCcyMupAbHv27pLuaRYKFFMGOOklZV0AuJSa7oiV_QYEv8y0-KmHs2fWXoB3IbM23wGYTONJeJmTKiODOq1asVQC7qw7Fiu0VCOCpk21H4QrR16buJqxJdjlKukUqKQXyjlYtDDFjWaiPAIOFCA-JzJNBQJD_YIi_6WKW16ZUWXgbLXpmXN_Rp_6EbzExzbf9ir3FZK2UfXTWv0DiUP1eUbcxQeX_Z2oAxDQevPS549CpT5Myc6wqMYizv1mddnGCcteaCAVv_rIJrlYjwyqiAM0ntRKvBdXrpM_NFX_wQbpMlmvvbJXEZ5aiR4CSKY57YKqBlkyBFwFQEoKCmAb3y0WeBv9vYfw7jy5pw11TXvHXKkVj1qSki0D-4QjACQXVwPU1gbDGwQg3T0wjVYiA2EiJ4og6KIAEqfidq7CJv_IUZIXYLp-zOJCq-OXKyKWs=w1216-h310-no" alt="nginx-loadbalancer-03">

그림에서 설명했듯이 __처음 get__ 요청에 대한 응답은 __demo app1__ 에서, 그다음 요청인 __get styles.css__ 요청은 __demo app2__ 으로 전달되며, 로드밸런싱이 잘 되는 것을 알 수 있습니다.

그럼 다음 챕터에서 nginx loadbalancing config 를 수정하여 이런 점들을 고쳐보도록 하겠습니다.

## nginx LoadBalancing config

## 마무리


## 참고 자료

