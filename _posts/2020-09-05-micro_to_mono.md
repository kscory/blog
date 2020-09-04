---
layout: post
comments: true
title:  "[프로그래밍 방법론] 마이크로 서비스에서 모놀로틱으로 1"
date:   2020-09-05
author: Cory
categories: programming_paradigm
permalink : /dev/programming_paradigm/bitro_micro_to_mono
tags: nestjs paradigm
---

![bitro-logo](https://lh3.googleusercontent.com/pw/ACtC-3c17zEIPlUO7no2uAkir59CBKCfXSAVyhDDO2Ie2jABI7Pdk5gDAhhr7HAo5uBnTlZcG0nkQHEb0R8Sq0hBvMOFRvSYOckIuAc2Kr6NCL9AoEWUe1Pr50Mlcv57vQFSdKuVg1zPqkj0LPrhS_snsjcY=w700-h302-no?authuser=0)

안녕하세요? 저는 요즘 비트로 서비스 2.0 을 위해서 열심히 개발하고 있습니다. 이전에 [비트로 클린아키텍처](https://kscory.com/dev/programming_paradigm/bitro_clean_architecture) 글을 쓰면서 비트로 서비스는 마이크로 서비스 방식으로 개발하고 있다는 점을 말씀드린 적 있었습니다. 

그런데 계속해서 유지보수하고 운영해가면서 문제점들이 너무 많이 발생해 최근에는 모놀로틱 서비스로 다시 이전하는 작업을 진행중에 있는데요 아직 완성되지는 않았지만 왜 비트로 서비스는 모놀로틱 형태로 바꾸기로 결정했는지 어떤 방식으로 모놀로틱 서비스로 바꾸고 있는지에 대해서 설명하고자 포스팅을 작성했습니다.

## 들어가기 전에

들어가기 전에 저 간단한 비트로 서비스에서는 어떻게 마이크로 서비스로써 굴러가고 있었는지에 대해서 간략하게만 설명하겠습니다. 

![](https://lh3.googleusercontent.com/pw/ACtC-3eNmpCWYqWCH5Z9xcqizkBbxlmJQXS8FnALzV5SCmgkZpVxevo57S2tG_MnBqdjIjaaxQgY8OHQLvvrwv2mx3bEHTZPD7QfI4HHkeAbHai7Ryvrjexv85-fGoJixLlo_E5ZT41Hum2w-GdGuCH3oLIF=w1640-h1022-no)

우선 비트로 서비스가 돌아가는 필수적인 구성요소만 그려보았습니다. 각각의 domain 에서는 user, giftcard 등의 서비스적인 요소를 포함하여 bitcoin 을 다루는 서버, 출금 등이 일어날때 알려주는 알람 등을 구성하고 있습니다. 또한 각각의 domain 안에서는 그 domain 에 맞는 service 들이 module 형식으로 이루어져 있습니다. 그리고 각각의 서비스에서 비동기적으로 이루어져도 괜찮은 부분은 queue 를 통해, 즉각적인 응답이 필요하다면 http api 를 이용하고 있습니다. 자세한 아키텍처는 [이전 블로그 포스팅](https://kscory.com/dev/programming_paradigm/bitro_clean_architecture) 을 참고 바랍니다.

사실 여기 있는 서비스 말고도 저희 겜퍼는 블록체인 기술을 가지고 있는 팀으로 적절한 데이터는 겜퍼 내 블록체인에 데이터를 담고 있습니다. 즉, 블록체인이라는 서버도 운영중에 있다는 뜻입니다. 그 외에도 운영 요구사항이 필요하다면 마이크로 서비스 형식으로 만들어 나가고 있었습니다.

## 뭐가 문제야?

문제점은 저희 개발자 인원 자체가 적다는 것입니다. 즉, 소수의 개발자가 이 많은 분산된 서비스를 관리하는 일이란 쉽지 않은 일이였습니다. 일단 위에서 그려드린 서비스만 하더라도 Api gateway 부터 6개의 서로 다른 서버를 운영하고 있다는 점을 알 수 있습니다.

또한 저희들은 아직 많은 이용자가 사용하는 서비스는 아니다보니 __쿠버네티스__ 나 다른 마이크로서비스를 도와주는 도구를 따로 쓰지 않고 __elastic beanstalk__ 를 아용해 __ECS__ 를 띄우도록 서버를 구성하고 있습니다. 그러다보니 AWS 안에서도 비효율적으로 리소스를 사용하게 되는 현상이 일어나게 되고, 이를 해결하기 위해서 EKS 를 도입하자니 적은 개발자 리소스를 이를 배우는데 사용하기는 무리가 있었습니다.

또한 각각의 서버를 따로 띄워서 6대를 쓰는 것(각각 서버를 1대씩 띄웠다는 가정하에)보다는 하나의 모놀로틱 서버를 6대로 띄워서 scale out 하는게 현재 상황에 더 맞을 수 있겠다는 판단을 하게 되었습니다.

__그래서!!__ 이번에 회사에서 비트로 2.0 을 기획하게 되면서 이렇게 흩어져 있던 마이크로 서비스를 아래처럼 하나의 모놀로틱 서버로 모아버리기로 결정했습니다. 

![](https://lh3.googleusercontent.com/pw/ACtC-3c1L9P56Cg5EYTEJDQPT-x-1GXdxEQFyeDp16NJxup3sjdJq6xpgVaGxwU2yajOLNx9JGKDh-WylzaE1x9kPBsLy1YkIJX2VlO3lfTQzVkkVZEhwCPkUr-OFVJpAMQQBFvtbkNCAZhXdJ4hzYBIDANw=w1042-h696-no?authuser=0)

딱 두대의 서버구성만을 하게 됩니다. 비트코인을 다루는 서버는 비트코인 코어와 붙어야 하기 때문에 보안상 따로 띄우기로 했습니다. 즉, 사실상 모든 서비스가 __bitro 2.0__ 서버로 들어가게 되었습니다. API gateway 도 따로 두지 않고 이미지 서버도 nginx 웹서버에서 최대한 처리해서 전달하기로 결정했습니다.

## 구현은 어떤 방식으로?

구현과 관련된 부분에서는 하나의 발표자료가 영향을 많이 미쳤습니다. 스프링 과련되었던 세션이였던 것 같은데 [잘 키운 모노리스 하나 열 마이크로서비스 안 부럽다](https://www.slideshare.net/arawnkr/ss-195979955) 라는 발표자료 입니다.

즉, 마이크로 서비스로 서버를 분리시키지 말고, 모듈형으로 개발해서 서비스 단위로 하나의 서버에서 개발을 할 수 있다는 점입니다. 그리고 서로 다른 모듈의 호출이 필요할때는 root 모듈에서 추상화를 시켜서 사용하는 방식이였습니다.

하지만 저희 겜퍼는 기본적으로 __타입스크립트__ 를 기반으로 __Nest js__ 프레임워크를 주로 사용하고 있습니다. 그래서 스프링을 활용한 방식과는 조금 다르게 개발 해야만 했습니다.

그럼 다음 장부터는 어떻게 모놀로틱 형식으로 개발하는지 설명드리도록 하겠습니다.

## Nest js 의 모듈 방식을 적극 활용

일단 Nest js 는 기본적으로 모듈 형식의 프레임워크 입니다. 그러다 보니 오히려 모듈을 나누는 데 있어서는 직관적으로 알 수 있었습니다. 예를 들면 아래와 같은 방식입니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3doUjt0ImlUPsgcEzka2hTrzpAwseUZoyNdsXB82LjyFDw0yfGnLtrRnX4W55F09KevN7KTdiVyv-GDsmoueMyomkpIf1-A29f08D76pFBX8tIf7k8MbED3s6hhI0U5TQH9aBAnV_RgWaHP0rL3Tl5a=w970-h526-no?authuser=0)
<div style="text-align: center;">출처: https://docs.nestjs.com/modules</div><br>

가장 위에 App Module 이 존재하고 Order Module 은 각각의 Feature Module 을 가지게 되는 구조입니다. 

이에 저희는 이전에 있던 비트로 마이크로서비스들을 저기의 Application Module 바로 아랫단으로 두고 각각의 Module 에 각각의 서비스를 다시 Module 로 두는 식으로 개발하게 되었습니다. 즉, 이전에 Application Module 바로 아래 있던 부분이 이제는 두번째 layer 로 내려갔다고 생각하시면 될 것 같습니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3ec24ntdR7emN0CgazaUfn8clryeBrwEPWIWi2zTCfgzz2DpIxPZCznAVlFBWuvDL_w21wCB-iV0pIxDyzcCLoRFKHDFOW5UdBTkDG9RV7QSpk9hF6EN7lnldFo9u5DFCamcm597hHqeqhrFQvMoMac=w1680-h454-no?authuser=0)

기본적으로 위와 같은 방식으로 흩어져 있던 마이크로 서비스를 모놀로틱 서비스로 합치고 있습니다. 

하지만 여기서 문제는 이 모듈 형식은 `child module` 이 `parent module` 에 종속되기 때문에 child module 이 parent module 을 사용할 수 없었습니다. 즉, 저희는 User 에서 Giftcard 서비스의 로직을 사용하고 싶은데 그냥 module 을 이용해서 사용할 수 없는 상황이였습니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3e_kTqp_gRqW8-OSq9hsBYoVgmPxqHjF-tmEwdB_IsUovPPTZ8II52u66q6QLK_t32kYA3jJ2qBxqA5Sf_Y4TXxMaQFTRanmEQg3oU2rtO458rLi3JWiujrKowiO2QP8lYICXFHVusHYcflx604jk1V=w1230-h476-no?authuser=0)

하지만 Nest js 에서는 Shared Module 을 제공하며 Circular depenency 를 회피하기 위한 Module 형식도 제공하고 있습니다. ([Circular dependency](https://docs.nestjs.com/fundamentals/circular-dependency#circular-dependency)) 이를 활용하게 되면 아래 와 같이 두 가지 방식을 생각해 볼 수 있습니다.

1. 각각의 Module 들이 필요한 모듈은 서로 참조한다.
2. 공통된 shared module 을 빼고 이 모듈과만 소통한다.

저희는 첫번째 방식은 내부 프로젝트의 모듈형식이 너무 꼬일 것 같아 각각의 모듈에서 외부에서 사용할 것들을 interface 로 추상화 한후 service Module 이라는 이름으로 서로 다른 domain 의 모듈에서 호출 할 수 있는 모듈을 따로 생성했습니다. (구현 방식은 다음 포스팅에서 진행하겠습니다.)

![](https://lh3.googleusercontent.com/pw/ACtC-3fKhlglzh1zAnjg-tbC_5CJEn5pVGWXTEOlfK9ZedQ7MEFgH0WjY6MMpNBxpcMDIsbOmahVvM6j_dU-8KW1WHPzIIPMBywYslcsVNWpCLFhIPIqlw6GlDNTPjl3Pl7vyGi3iwdIl7LR7tkGD8BeldxG=w1422-h464-no?authuser=0)

여기서 Application 모듈에서 못한 이유는 App Module 은 많은 시스템적인 부분을 담당하고 있어 공통되게 순환참조 시키기는 무리가 있다고 판단했기 때문입니다. 

위와 같은 방식을 통해서 User 모듈에서 밖으로 열어줘야 하는 로직은 Service Module 에서 구현하고 GiftCard 에서 User 모듈의 로직을 사용해야 하면 Service 모듈에서 구현된 클래스를 주입 받아서 사용하게 됩니다.

## 마무리 하며

이번 포스팅은 Why? 에 대한 이야기와 간단한 이론적인 이야기만을 포스팅하게 되었습니다. 다음 포스팅에서는 이제 이렇게 모듈 형식으로 구현하는 방식을 예시를 들어서 하나씩 구현해보도록 하겠습니다.

다음 포스팅은 앞에서 이야기한 비트로의 Clean Architecture 형태로 구현할 예정이니 혹시 이전 포스팅을 못보신분들은 참고 부탁드립니다
