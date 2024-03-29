---
layout: post
comments: true
title:  "[책 리뷰] 리액트 네이티브를 다루는 기술"
date:   2022-04-10
author: Cory
categories: Review
permalink : /daliy-life/review/gilbut-reactive_native_tech
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/AM-JKLWuIsFrOtavvSgcE81hKbH57PzE-OQ1qQamDNCk512cP04T6N_adX4ui2gTUGsSFnT8d5CNiMCofDtwuYevp5S1ZMeRohQ3z6nAnK4qn-ZRcL8pZ9GdEVrCDJe1x9qbggY5XLEUlvmsq2mxW8kV68eU=w198-h254-no?authuser=0" alt="gilbut-reactive_native_tech">

> 이 리뷰는 길벗 출반사 개발자 리뷰어 이벤트에 의해 책을 제공받아 작성된 서평입니다.

스타트업에서 앱을 만들때 요즘에는 리액트 네이티브를 활용해서 개발하는 경우가 많다. 현재 작성자의 회사도 마찬가지로 적은 인원이 높은 퍼포먼스를 내기 위해 리액트 네이티브를 활용하고 있다. 이는 그런 리액트 네이티브를 위한 입문서로 어울리는 책이다.

이전에 리액트를 다루는 기술을 먼저 본 개발자라면 책 목차나 내용 자체는 이질감이 크지 않을 것 같다. 아무래도 동일한 저자가 집필한 책이라서 그런지 짜임새가 비슷하게 초반에 할일 앱을 만드는 것부터 서버를 만들고, API 를 붙히는 것까지 비슷한 목차대로 흘러나간다. 그리고 특징이라면 특별히 막히는 부분 없이 이것저것 기초적인 부분을 테스트해 볼 수 있는 책인 것 같다.

다만 실습할때에는 버전을 맞춰서 작성을 해서 몰랐는데, 최근에 리액트 네이티브가 업데이트되면서 특정 부분들이 많이 바뀌었다고 들었다. 화면이나 API 부분은 문제 없겠지만 모듈을 만드는 부분은 바뀔 수 있는 부분이므로 책을 사는 분들은 개정판을 기다리는 것도 방법일 수 있다. 물론, 아직 가장 최신버전만 바뀌었으므로 작성자처럼 이전 버전으로 실습하고 다른 부분은 folow up 해도 괜찮은 전략이라고 생각한다.

책은 처음에 리액트 네이티브 환경세팅을 먼저 시작한다. 아무래도 웹이 아니라 모바일에서 작동하다보니 처음 시작할 때 가장 막히는 부분 중 하나일 것이다. 그 후에는 jsx 문법을 알려주고 바로 기본이 되는 할 일 App 을 만들어 나간다. 이는 앞에서 배운 jsx 문법을 복습하는 차원에서의 앱이라고 생각하면 될 것 같다. 그 후에는 모바일에서 필수인 리액트 네비게이션을 사용하는 예제를 설명하고 바로 두번째 앱을 개발하개 된다. 두 번째 앱은 다이어리 앱으로 여러 화면을 넘어가는 앱을 만들게 된다. 그러면서 할일 앱보다는 조금 더 복잡한 뷰와 상태관리 방법을 설명해준다.

그 다음 챕터는 사진공유앱으로 모바일 앱을 개발하면서 거의 초반에 접하게 되는 Firebase 를 백앤드로 하는 앱을 만든다. Firebase 를 이용해서 인증을 하고 FireStore 를 DB 로 활용해서 앱을 만든다. 그 후에는 이전에 말한 모듈을 작성하는데 실제 AOS와 IOS 가 사용하는 코틀린, 스위프트를 이용해서 모듈을 만든다. 그런데 아마도 이 부분이 많이 바뀐 부분이라고 본 것 같아서 다시 확인해볼 필요가 있을 것 같다.

그 후에는 타입스크립트를 활용하는 방법을 알려주고 다음 챕터에서는 리액트에서 빠질 수 없는 상태관리에 대한 이야기를 한다. 아무래도 많은 앱들이 리덕스로 이루어져 있는 만큼 리덕스에 대한 이야기가 주를 이루고, 마지막에 리코일에 대한 이야기로 챕터가 끝난다. 

이제 마지막 앱을 만들게 되는데, 실제 간단한 API 를 만들어서 앱하고 연동하는 실습을 진행한다. 물론 백앤드를 많이 다뤄본 사람이거나 이미 해본 사람이라면 문제 없겠지만 처음 접한다면 조금은 어려울 수도 있다. 하지만 특별히 어렵게 실습하는 부분은 없고, 작성자의 경우에는 리액트 네이티브만을 실습하고 싶어서 백앤드는 그냥 github 에서 다운받아서 사용했다. 그리고 마지막 16장에서는 앱을 실제 마켓에 올리는 방법을 알려주고 책은 끝이 난다.

책의 부피가 크고, 이번에 리뷰할 책을 조금 늦게 받아서 특정 코드를 직접 작성하기 보다 복사 붙히기 하면서 보기도 했었다. 하지만 이전에 나왔던 리액트를 다루는 기술이라는 책 만큼 이 책도 처음 접하는 사람이 보기에 좋은 책인 것 같다. 작성자처럼 리엑트 네이티브를 공부해보고 싶은 사람들에게 입문서로 추천하고 싶다.
