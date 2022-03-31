---
layout: post
comments: true
title:  "[책 리뷰] 팀장부터 CEO까지 알아야 할 기업 정보보안 가이드"
date:   2022-02-23
author: Cory
categories: Review
permalink : /daliy-life/review/hanbit-sre_with_java_microservices
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/AM-JKLWyEz21hYtfN2bI74WmL21NTaJ-rg01s8InR1FObWk_hTsWNOUHzZYbrdCOB6yw4n7bdoRGrzVWmB2QOJAiDKTH6YRm_Y6rXedEAvhk0JBM7amfdVxzAN9rbTzBD4T5wc1NNQoLYR6xWbuiWOv2w_Uw=w2296-h1724-no?authuser=0" alt="hanbit-sre_with_java_microservices-01">

> 이 리뷰는 한빛미디어 <나는 리뷰어다> 활동을 위해서 책을 제공받아 작성된 서평입니다.

개발하면서 가장 신경써야 할 부분이 어플리케이션의 모니터링부분이다. 이 책은  그런 모니터링에 대한 부분에 대해서 설명해 주고 있는 책인데, 특히나 모니터링 도구들을 사용하기 보다 어플리케이션에 직접적인 (화이트박스) 모니터링을 중점적으로 설명해준다.

책에서는 실제 코드를 제시하면서 설명하기는 하지만 핸즈온 실습처럼 하나의 어플리케이션을 구축해가면서 모니터링을 설명하는 것이 아니라 특정 모니터링에 대한 개념을 설명하고 이를 자바로 구축한다면 특정 방식으로 구축할 수 있다는 점을 보여준다. 그래서 개인적으로 자바를 주 언어로 사용하고 있지 않은 작성자같은 경우에도 비슷한 방식으로 모니터링 시스템을 구축할 수 있겠다 하는 생각이 들었었다.

처음에는 구글의 신뢰성엔지니어링과 비교하면서 저자의 경우에는 다른 방식이 더 나았다는 말을 하게 된다. 즉, 본인이 속해있는 회사의 비즈니스에 맞는 방식을 선택해야 한다는 것을 의미한다. 그러면서 모니터링의 중요성에 대해 설명해준다. 2장부터는 실제로 어플리케이션 메트릭에 대한 설명을 자세하게 해준다. 메트릭은 어떤 종류가 존재하고, 이 중에 어떤 메트릭을 수집해야 하는지 등에 대한 설명을 한다. 그러면서 자바로 어떻게 구현할 수 있는지에 대해 설명한다. 3장에서는 Observability 에 대해서 설명하면서 분산추적과 샘플링에 대한 설명을 한다. 4장에서는 이제 차트에 대해서 설명하면서 어떤 차트를 어떻게 사용하면 좋을지 이야기해준다. 5장은 CI/CD 에 대한 이야기로 가장 인상 깊었던 내용은 CI 의 경계를 지정하라는 부분이다. 개인적으로도 개발할 때 CI/CD 의 경계가 모호하다는 생각을 자주 하곤 했어서 그런 것 같다. 6장은 린트와 라이브러리 버전 등에 관한 이야기를 하고 있다. 7장에서는 배포시에 어떤 전략을 쓰는지 설명한다.

요즘 모니터링에 대해서 관심이 많아서 그런지 책 자체의 내용은 흥미로웠다. 단순히 도구(프로메테우스 등) 사용법만을 이야기하는 것이 아니라, 어떤 방식으로 어플리케이션을 모니터링해야하는지, 어떤 점이 중요한지 등을 알 수 있어서 좋았었던 것 같다. 자바로 개발하는데 모니터링에 대해서 아직 접하지 않은 개발자라면 이 책을 읽어보길 추천한다. 