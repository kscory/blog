---
layout: post
comments: true
title:  "[책 리뷰] 파이썬으로 살펴보는 아키텍처 패턴"
date:   2021-06-18
author: Cory
categories: Review
permalink : /daliy-life/review/hanbit-architecture_patterns_with_python
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/ACtC-3cRHXmTQmoRhVL3GEkUMmLwD_2W8B4eqGmj7PcG9BDCYrKfw4RQbdzkGOQFvzF68K-E9T6rSVSw2mar-dWEcwX5P4u7fDnUAl2k6_mrYNhXMKcq65wJdIEk2Y7yxvkoN-egceU-ddmZSqs2aPS1VgI8=w2316-h1736-no?authuser=0" alt="architecture_patterns_with_python-01">

> 이 리뷰는 한빛미디어 <나는 리뷰어다> 활동을 위해서 책을 제공받아 작성된 서평입니다.

이것저것 어플리케이션을 개발하다 보면 클린아키텍처, DDD, Onion 아키텍처 등등 많은 개발 아키텍처 패턴에 대해 관심을 가지게 된다. 하지만 개인적으로 읽었었던 글 중에서는 MS docs 에 있는 [.NET 마이크로 서비스: 컨테이너화된 .NET 애플리케이션을 위한 아키텍처](https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/) 가 가장 쉽게 와닿았었던 것 같다. 이 책 또한 .Net 문서와 비슷한 아미텍처 패턴에 대해서 알려주고 있는 책이다. 문서와 다른 점은 일단 이 책은 마이크로서비스에 관한 이야기는 아니고 파이썬으로 되어 있다는 점이 아닐까 싶다.

머리속에는 DDD, TDD, EDA, CQRS, UOW, 등등 패턴과 아키텍처가 섞여서 이해하게 되고, 이걸 적용할 때는 어떤 방식으로 적용해야 하나 항상 고민했었는데 책을 읽고난 후에는 이렇게 머리에 난잡하게 있던 개념들이 정리되는 느낌을 받았다. 결로적으로는 실제로 어떻게 적용하는지가 중요한데 이 책에서는 직접적으로 예시를 들면서, 리펙토링을 해가며 개념들을 하나씩 적용해 나가는 부분이 마음에 들었다.

이 책은 목차만 보아도 무엇을 이야기하는지 정확하게 적혀 있다. Part1 에서는 1장의 도메인 모델링, 2장의 Repository 패턴, 4장의 Application Service 계층, 5장에서 테스트는 어떻게 하는 것이 좋을지, 6장의 UOW 패턴 등 어디선가 들어봤고 적용하고 있는 부분들에 대해서 알려주고 있다. 마지막에는 Aggregate 과 Bounded Context 에 대해서 간단하게 알려주면서 끝이 난다.

그 뒤 Part2 에서는 EDA 와 관련해서 알려주는데 8, 9장은 domain event 와 관련된 이야기, 10장은 command event, 11장은 Integration Event, 12장은 CQRS 관련해서 이야기한다. 그리고 이렇게 이벤트 핸들러들이 늘어났을 경우 의존성 주입을 하지 않으면 불편한 점들이 야기되는데 이를 위한 DI 에 대해서 알려주고 있다.

특히 부록도 마음에 들었었는데 부록B 에서는 초반에 개발할때 고민하게 되는 프로젝트 구조를 어떻게 짜면 좋을지에 대한 이야기로 시작, 부록C 는 추상화를 할 경우 infrastructure 를 어떻게 쉽게 갈아끼울 수 있는지에 대한 이야기, 부록D 는 장고와 같은 full framework 에서 어떤 방식으로 domain 모델을 프레임워크에 종속적이지 않게 만들 것인지에 대해 이야기해준다. 그리고 부록E 는 처음접해봤었는데 작성자의 경우에도 궁금했던 부분들에 대한 이야기였다. 검증을 어디서 해야하고 이는 어떤 layer 에 들어갈까 하는 부분이다.

이 책은 어려운 개념이 많이 들어있기 보다는 실무에서 어떻게 사용하면 좋을지에 대한 실무에 가까운 책인 것 같다. 하지만 개인적으로 생각해 보았을 때 DDD, EDA, TDD 등을 완전히 처음 접해본다 하면 개념을 좀 더 찾아보고 읽기를 권유하고 싶다. 개념에 대해서 간단하게 이해할 수 있을 정도로만 설명하고 넘어가고 코드 위주로 설명하기 때문에 개념을 이해하지 못한다면 왜 이렇게 사용하는지 이해가 잘 안될수도 있을 것 같았기 때문이다.

결론적으로 전체적으로 어플리케이션 아키텍처 패턴을 정리하고 싶은 분들은 이 책을 구매해서 읽어보는 것을 추천하고 싶다.
