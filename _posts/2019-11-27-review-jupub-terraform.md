---
layout: post
comments: true
title:  "[책 리뷰] 테라폼 설치에서 운영까지"
date:   2019-11-27
author: Cory
categories: Review
permalink : /daliy-life/review/terraform-up-and-running
tags: review book
---

<img src="/assets/review/terraform-up-and-running/terraform-up-and-running-01.jpeg" alt="terraform-up-and-running-01">

최근에 테라폼이 인기를 끌고 있음에도 불구하고 테라폼에 대한 책이 이것 뿐이라서 반 걱정하는 마음에 책을 구매했었다. 그래도 믿고 보는 __up & running__ 의 번역서인데다가 __jpub__ 출판사에서 번역한 책은 깔끔하기 때문에 안심은 되었었다.

이 책을 읽고 난 후 결과적으로 말하자면 작성자처럼 처음 테라폼에 입문하고자 하는 사람이라면 묻지도 말고 __이 책부터 추천__ 해 주고 싶다고 느꼈다. 기본 문법에 대한 설명 뿐만 아니라 후반부에서는 테라폼을 사용할 때의 팁들을 정말 잘 설명해주고 있다. 다만, 이미 업무에서 다양하게 사용하고 있는 사람들에게는 쉬운 내용들만 적혀 있을 수 있다. (사실 __O'REILLY__ 의 __up & running__ 시리즈 자체가 입문서라고 봐도 무방하니 목적에 맞다고 할 수 있다.)

다만 최근에 이 책의 개정판(2판) 이 출시되었기 때문에 얼른 새로 번역해주면 좋겠다는 생각이 들었다. 그래도 2판이 출시되면서 1판 구매자에게도 좋은 점은 책은 책대로 읽고 예제는 원 저작자의 github repo 를 다운받아 사용한다면 에러도 없고, 최신 테라폼 문법까지 배울 수 있다는 것이다.(버전 12 로 올라가면서 문법이 바뀐 것이 많다.) 개정판을 보니 terragrunt 사용법 등에 대해서도 다루고 있어 개정판을 기다리는 것도 나쁘지 않은 선택인 것 같기는 하다.

위와 같은 점을 고려할 때 총 평은 __테라폼 최고의 입문서__ 라고 말하고 싶다.

<img src="/assets/review/terraform-up-and-running/terraform-up-and-running-02.jpeg" alt="terraform-up-and-running-02">

이 책은 기본적으로 AWS 를 기반으로 해서 만들어 졌다. 그래서 일단 테라폼의 기본 문법을 사용해서 AWS 인스턴스를 올리는 것부터 시작이 된다. 그 이후 아래 그림처럼 테라폼의 문법을 설명하면서 AWS 의 다른 요소들을 띄우기 시작한다. 그리고 점점 후반부로 가면 AWS 의 간단한 아키텍처들의 결합 요소들을 어떻게 테라폼으로 관리할 수 있는지 설명해준다. 마지막으로는 DevOps 팀에서 테라폼을 관리할 때의 팁들을 알려준다.

아무래도 코딩 책(IaaC 책)이다 보니 코드가 많이 적혀 있는 것은 사실이다. 개인적으로 실습 책은 이런 스타일의 책들을 선호하는 편이긴 하다.

또한 아키텍처와 같이 어려운 부분들에 대해서 설명을 할 때 테라폼을 어떻게 올려야 AWS 에서 장애 없이 올릴 수 있는지 설명해주고 좀 어려운 부분의 같은 경우 위 사진과 같이 그림과 함께 설명해준다.

<img src="/assets/review/terraform-up-and-running/terraform-up-and-running-03.jpeg" alt="terraform-up-and-running-03">

현재는 작성자 회사의 경우 __Cloudformation__ 과 __Terraform__ 어느 것을 이용할 지 선택길에 있다. 그래서 테라폼에 입문해보기 위해 사용해 보았는데 정말 문법도 간결하고 쓰기 편하다는 생각이 들었다. 

사실 테라폼이든 클라우드포메이션이든 어려운 것은 이것을 코드로 작성하는 것이 아니라 __AWS__ 그 자체이다. 혹시 테라폼을 배우고 싶은데 __AWS__, __GCP__ 등 현재 사용해야 하는 클라우드에 대해서 잘 모른다 하면 이 책을 사고싶은 마음은 접어두고 클라우드 공부부터 시작하는 것을 추천하고 싶다.

그렇기 때문에 결론적으로 이 책을

- 1 AWS 를 사용하고 있는 테라폼 입문자
- 2 Cloudformation 을 사용해본 테라폼 입문자
- 3 어쨌든 테라폼 입문자

등에게 추천하고 싶다.
