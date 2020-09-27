---
layout: post
comments: true
title:  "[책 리뷰] 실전 자바 소프트웨어 개발"
date:   2020-09-27
author: Cory
categories: Review
permalink : /daliy-life/review/hanbit_optimizing-java
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/ACtC-3ef4BSM40MzV3sVxGTUpYW80a561qgr71E8s52LP-l8Bbl1FdEAkOvhBRiyhcu5D5hXPdk05kORn9FdYm2UPImH9YuMkPNWmavkkSAUu8PF32z7ByaFnYuE3Ze4G_unwtmbj7OhAGp9kWgY9mEEFujZ=w1350-h1798-no?authuser=0" alt="optimizing_java-01">

단순히 자바 문법이 아니라 조금 더 어려운 주제를 공부하고 싶어서 이 책을 읽게 되었었다. 물론 작성자의 경우에 자바를 현재 메인으로 사용하지 않지만 그래도 어떤식으로 성능 최적화를 할 수 있는지 개인적으로 궁금했다.

책은 솔직하게 말해서 술술 읽히는 그런 내용은 아니다. 처음부터 JVM 으로 시작해서 하드웨어, 운영체제, GC 등 하드한 내용들을 주로 다루고 있다. 그래서인지 개인적으로 JAVA 를 많이 써보지 않은 입장에서는 다소 어려웠던 책이였다. 

하지만 어려운 책임에도 불구하고 번역이 정말 깔끔하게 잘 되어있다. 어려운 내용들에 대해서도 용어도 먼저 설명해주고 있어 그래도 이해할 수 있도록 도와주곤 한다. 또한 다행히 컬러로 되어 있어 책을 읽는데 조금 더 집중할 수 있도록 해주었었던 것 같다.

결론적으로 자바를 문법적인 것만을 공부하다가 조금 어려운 내용을 공부하고 싶은 분들에게 추천하고 싶다. 저번에 읽은 __실전 자바 소프트웨어 개발__ 과는 달리 오히려 이 책은 어느정도 공부를 해 본 사람들이 봐야 이해할 수 있지 않을까 싶다. 작성자 또한 현재는 솔직히 많은 부분이 이해가 된다고 할 수는 없지만 나중에 더 익숙해 졌을때 꼭 다시한번 봐보고 싶은 책이였다. 

번외적으로 여기 나오는 내용은 취업 면접에도 도움이 되지 않을까 생각도 하기는 했다.

<img src="https://lh3.googleusercontent.com/pw/ACtC-3cEnCB2wWh9Ho2S8rUdIeVWM7PHtwPSrll3DBk92LFfvm-20viPZr3hG-74Vy-vnYDJkZXMIwrIfmswC7s5WU7HqIgrUcq3s0gzGXrhGesm_b6LBrzBuGgSYJxkZU-LFzyco5tPi6y1ReIcjUDwDJ1H=w1680-h1262-no?authuser=0" alt="optimizing_java-02">

책에서는 처음에 자바 성능과 최적화가 뭔지에 대해서 정말 간략하게 설명해준다. 당연히 이 책이 전체적으로 설명하는 내용이므로 무엇을 미리 알고 가야하는지에 대해서 알려준다고 생각하면 될 듯 싶다. 2장도 마찬가지로 자바에서 성능의 큰 부분을 차지하고 있는 JVM 내용인 많큼 이 부분도 꼭 알아야 할 부분인 듯 싶다. 3장은 어떻게 보면 2장의 연장선에 있다고 생각하면 좋을 듯 싶다. JVM 으로 해서 간단한 GC 내용을 설명해준다.

그 다음장에서는 이제 성능 테스트 기법을 알려주면서 성능 튜닝을 시작한다. 5장은 4장의 연장선상이라 생각해도 좋을 듯 싶다. 6장에서부터는 GC 에 대한 이야기로 시작해서 8장까지 이야기가 이어지고 있다. 그 뒷장에서도 계속해서 자바의 단순한 문법이 아니라 성능에 있어서 중요한 부분들에 대해서 다루고 있다.

개인적으로는 책을 읽으면서 이렇게까지 성능 튜닝을 해야하나? 혹시 성능튜닝하다가 오히려 프로그램을 망치지는 않을까? 하는 생각이 들기도 했다. 하지만 언젠가는 반드시 해야 할 작업이라 생각하고, 미리미리 준비해 두는 것도 좋을 것이라고 생각한다. 

"만약 자바가 느리다" 라는 말을 혹시나 많이 들어본 사람들은 이 책을 읽으면서 성능 튜닝을 어떻게 할 수 있는지 한번 확인해 보면 좋을 것 같다. 작성자의 경우에는 이 책을 읽으면서 그래도 자바에 대한 많은 궁금증이 해소되었었던 것 같다.
