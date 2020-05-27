---
layout: post
comments: true
title:  "[프로그래밍 방법론] 비트로 서비스 클린아키텍처 적용하기 (feat nestJs)"
date:   2020-05-27
author: Cory
categories: programming_paradigm
permalink : /dev/programming_paradigm/bitro_clean_architecture
tags: nestjs clean_architecture 
---

![bitro-logo](https://lh3.googleusercontent.com/pw/ACtC-3c17zEIPlUO7no2uAkir59CBKCfXSAVyhDDO2Ie2jABI7Pdk5gDAhhr7HAo5uBnTlZcG0nkQHEb0R8Sq0hBvMOFRvSYOckIuAc2Kr6NCL9AoEWUe1Pr50Mlcv57vQFSdKuVg1zPqkj0LPrhS_snsjcY=w700-h302-no?authuser=0)

요즘 클린아키텍처에 대해 열풍이 불고 있는데요. 제가 속해 있는 겜퍼에서도 비트로 서비스를 클린아키텍처를 적용해서 개발하고 있습니다. 그래서 이번 포스팅에서는 비트로 서비스에 적용된 클린아키텍처에 대해 이야기하고자 합니다.

이 포스팅에서는 클린아키텍처 개념에 대한 이야기를 자세히 하지는 않으니 궁금하신 분들은 [클린아키텍처](https://blog.insightbook.co.kr/2019/08/08/%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98/) 도서를 참고하시면 좋을 것 같습니다.

## Hexogonal 아키텍처

![hexogonal](https://lh3.googleusercontent.com/pw/ACtC-3djhz2B5-8-thizTdeuvG-haSIafN0j6Y2hH4PFoyJSQis-EGKAR8hpQDP3LbiG60gtwGEpf5wyl92Se0E8jbXQqwmOFHlOP9UGsI51ZWbYrIEnrhPPczSklj0BPUBnvHMhujHBm-j_CJWTNuqXgcKa=w700-h621-no?authuser=0)

갑자기 클린아키텍처를 이야기한다면서 Hexogonal 아키텍처가 나와 당황스러우신 분들이 있을 것 같은데요. 이 아키텍처에 대해서 이야기하다보면 클린 아키텍처와 닮은점이 많다는 것을 알게 됩니다. 사실 uncle bob 의 클린아키텍처 블로그 글을 확인하면 가장 처음에 hexogonal 아키텍처의 링크를 참고하게끔 하고 있습니다.

간단하게 이야기하자면 가운데 육각형 안에 비즈니스 로직이 있고 외부 종속적인 UI, API, Persistence 로직 등은 육각형 밖의 레이어에 위치하게 됩니다.

예를 들면 회원가입을 수행하는 로직은 내부 도메인으로 들어가게 되고, 회원가입을 요청하는 UI 로직은 외부에 두며, 요청한 회원을 DB 에 저장하는 로직 또한 외부에 존재시켜 레이어를 분리하게 됩니다.

또한 이 Hexogonal 아키텍처는 MSA 설계와도 매우 밀접하게 관련되어 있는데요.

![hexogonal2](https://lh3.googleusercontent.com/pw/ACtC-3d5kav2BUiS6XjfTAQvb4ZAluH8DapckSnIgAjnbwAAwWj_K0NnyQfaa-ehBwD6Lkyes5ZZgnWkNGfZHVFEHaQtgtUFAEcZMFVRm-GMCpKHigwFRUFhLaFOoG3elCsL_yX-RxtHcYMaMpC9mujbv51D=w700-h524-no?authuser=0)

위 그림에서 보듯이 각각의 서비스가 adapter 를 이용해서 상호작용하는 것을 확인할 수 있습니다.

## 클린 아키텍처

![clean-architecture](https://lh3.googleusercontent.com/pw/ACtC-3fl4P4d6QUxqk4iNMeEg9ZTgx6R7Md2ZgOP22jmyqM8l2c_OrHswqCbzSHKsycpuC2Dh-o14HJKlmvJMjJmtA7jIyF1_jEv7lcuvo_UIm0Vycem4KYcOx0aK6rIOzkZwHuFTHSf5Aar78Qh0yc4Z-9N=w700-h514-no?authuser=0)

이제는 너무나 유명해진 클린아키텍처 그림 입니다. 개인적으로 클린아키텍처로 설계할 때 중요한 점은 의존성 규칙은 외부에서 내부로 흘러야만 하고, 내부 로직은 프레임워크에 독립적이어야 한다는 점입니다.

그래서 저희는 의존성 규칙을 지키기 위해 DIP 를 적용했습니다. 그림에서도 나와 있지만 port 를 이용해서 의존성을 역전시킵니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3eekbrOPUlwVuK4leM7Xfo0YUck0C7mrhq7k3uvBWVPh8uvzq1APGh8-MzvgoxW3w5-WYyglpYyX0FWw_ev8mh7uO721ag_Xs7imAkLRbToJplS9_NnLsG76oTeXXHE-TmjsTXzBp6dqs3gfrRzNrnY=w700-h114-no?authuser=0)
<div style="text-align: center;">의존성 규칙이 위반된 형식</div>

![](https://lh3.googleusercontent.com/pw/ACtC-3cwajumn6_mTcJeq6A4cJjl1JidN8BAIOCc7amxyPkjFuPOuMTgtahlpWMs7IyRQ5pvkvqUle9D5yAehf-OtngucyWBLfLHqEnPXNbLSc57SNjr8WoTq0fOwaLPkW8gMod1yUv4cZx8ZW5K08NvFwRy=w700-h122-no?authuser=0)
<div style="text-align: center;">port 를 활용한 DIP</div>

물론 클린아키텍처에 의하면 UseCase 조차도 Interface 로 추상화 시켜야 한다고 하지만 저희 내부에서는 너무 심한 추상화는 오히려 개발할때 힘들 수 있으며 개발 속도도 늦어질 수 있다는 판단하에 이 부분에 대한 추상화 작업은 나중에 진행하기로 결정했습니다.

## 비트로 클린아키텍처

저희는 Tom Hombergs 의 블로그 글에서 hexogonal 아키텍처를 이용해 클린 아키텍처에 접근한 방법에 공감하여 비슷한 아키텍처로 구성하게 되었습니다.

![tom-hombergs-architecture](https://lh3.googleusercontent.com/pw/ACtC-3dmV_2_5By_zwuTta-md8P3Yf58b6HKpdDIXeTgPVyfApZI3U2AzkjWKvxgBKTCHU10gsX30LnBtlChf8aQrcGSe8ImJROoYGGkwxUogLL6bRck4y53czF2D1CjoMXmjwC3Mud4gfbStxhuzFEe6b1L=w700-h344-no?authuser=0)
<div style="text-align: center;">출처: https://reflectoring.io/spring-hexagonal</div>

위의 그림에서 보듯이 Entity 와 UseCase 를 Hexogonal 의 내부로 생각하고 Controller 와 ORM Repository 는 Port 를 이용해서 접근하게 됩니다. 여기서육각형 안에 있는 부분은 프레임워크에 독립적일 수 있도록 만들었습니다.

그리고 프레임워크에 종속적인 부분들은 Port 와 Adapter 를 이용해서 내부 도메인 로직과 소통하게 됩니다.

## 프로젝트 구조는?

비트로에서 실 서비스에 사용하고 있는 프로젝트 구조입니다.

