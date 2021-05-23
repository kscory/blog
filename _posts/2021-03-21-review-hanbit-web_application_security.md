---
layout: post
comments: true
title:  "[책 리뷰] 웹 애플리케이션 보안"
date:   2021-03-21
author: Cory
categories: Review
permalink : /daliy-life/review/hanbit-web_application_security
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/ACtC-3fmD7dIkasy7cIJqQa6IT5h8B1JEhVkg69jJldn298YrR2tcZ3Q0qey7H0nUE_dOX8tgVYFIvCAtikfn6GPbZjwGN01UMH3B8EKx_C3tXqNXOc-WCuqLSpgrbv-lXQ0hXGehvtWvM0EVryf1Fxs5eje=w2316-h1736-no?authuser=0" alt="web_application_security-01">

웹 애플리케이션을 개발하다보면 항상 신경쓰이는 부분이지만 잘 지켜지지 않는 부분이 바로 보안적인 부분이 아닐까 싶다. 물론 요즘에는 대부분의 라이브러리나 프레임워크 내부에서 자동적으로 적용해주는 부분들이 많지만 그것만 믿고 개발했다가는 문제점이 많이 발생할 수 있다. 이 책은 그러한 웹 애플리케이션을 개발할 때 주의해야 하는 보안에 대해 이야기하고 있다. 

저번에 읽었었던 책은 컨테이너 보안 책이였는데 그 구성요소는 다르게 느껴졌다. 전에 읽었던 책은 컨테이너 자체에 집중하면서 보안에 대한 이야기를 했다면, 이 책은 일반적인 웹 보안 이야기로 시작해서 일반적인 웹 보안이야기로 끝이 난다. 그러다보니 다른 웹 보안 책들과 비교해서 특별하다거나 그런 점을 느끼지는 못했지만 기본에 충실한 책이라는 생각이 들었다.

우선 책의 시작은 보안과 해킹에 대한 역사로 시작한다. 아무래도 처음에는 간단하게 아이스브레이킹하는 시간이자, 보안의 역사가 어떻게 되어있는지에 대한 이야기를 한다. 그리고 마지막에 현재는 다른 보안도 중요하지만 웹 보안이 가장 취약점이 많으면서 문제점이 많은 부분이라는 말로 끝이 난다.

그 후에는 보안에서 중요한 정찰, 공격, 방어에 대해서 알려준다. 정찰의 경우는 대부분의 회사에서 진행하는 모니터링, 취약점 분석 등을 이야기하고 있다. 공격과 방어는 쌍방으로 이루어져 있어 거의 한 세트라고 봐도 무방하다. 흔히들 알고 있는 XSS, CSRF, SQL 인젝션 등에 대해서 공격하는 방법, 방어하는 방법에 대해서 알려준다. 마지막까지 책을 읽고 나면 웹 보안에 대한 기본적인 부분들에 대해서 이해할 수 있게 된다.

사실 여기서 나온 보안에 대한 이야기는 정말 기본적인 이야기인 것 같다. 아마도 대부분의 개발자들이라면 한번쯤 들어봤고 한번쯤 검색해봤을 그런 이야기들에 대해서 적혀있다. 하지만 이 책을 읽으면서 좋았었던 점은 이런 웹 보안에 대해 다시 한번 생각해볼 수 있는 계기가 되었고 들어는 봤지만 무시하고 넘어갔던 부분들에 대해서도 다시한번 검토해보게 만들어 주었다는 점이다. 물론 책의 눈높이 자체가 이미 보안을 알고 있던 사람에게 초점을 맞춘 책은 아니였기 때문에 그런 사람들은 쉽다고 느낄 것이다. 

웹 애플리케이션을 개발하면서 어떤 보안에 신경써야 하는지 궁금한 분들이 한번쯤 읽어보면 도움이 될 듯한 책이며, 개인적으로도 마음에 들었던 책이였다.