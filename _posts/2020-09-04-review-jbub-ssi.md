---
layout: post
comments: true
title:  "[책 리뷰] 자기주권 신원증명 구조 분석서"
date:   2020-09-04
author: Cory
categories: Review
permalink : /daliy-life/review/jbub_ssi
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/ACtC-3ccxONOSky2gFMYWNg1WnohliMMuJ68SFnlKrTvC262azU89-ziZ1ajjty4xstzfpTNE2QQypyz5cH810Uu1868cIk3mNdiXAdXwDkH7Nb_te2F7kYfrUTQnJ0F6Yoe9gH3oGgvxQ2A6YSTx0vEjjoP=w1350-h1798-no?authuser=0" alt="ssi-01">

블록체인 생태계에서 현재 가장 활발하게 진행되고 있는 두 가지 분야가 있다면 하나가 DID, 다른 하나는 DeFi 일 것이다. 그런데 이 두가지 분야는 개념 익히기도 어렵기 때문에 진입장벽이 높은 기술이라고 할 수 잇을 것 같다. 이 책은 그 중 DID(SSI) 를 상세하게 다루고 있다.

우선 이 책에서는 SSI에 대해서 정말 자세히 쉽게 설명해주고 있다. 하지만 절대 쉬운 책이 아니라는 점을 말씀드리고 싶다. 일단 블록체인이라는 개념 자체가 어려운 개념인데다가 SSI 는 거기에 한술 더 나아간 어려운 개념들이 포함되어 있기 때문이다. 그래도 개인적으로는 지금까지 보았던 다른 블로그 글보다 쉽게 쓰여있다고 생각한다.

저자도 이전에 __하이퍼레저 패브릭으로 배우는 블록체인__ 이라는 책을 쓰셨었는데 그 책 또한 하이퍼레저에 대한 내용에 대해서 정말 상세히 알려주었었던 것으로 기억한다. 이에 못지 않게 __자기주권 신원증명 구조 분석서__ 책도 내용을 하나하나 자세히 잘 설명해준다. 또한 책을 읽을 때 문맥때문에 안읽히거나 하는 경우는 거의 없을 것으로 생각한다.

하지만 이 책을 구매하고자 하는 분이라면 "내가 블록체인을 한번 쯤 본 적 있는가?", "개발을 해본 경험이 있는가?" 는 한번 생각해봐야 할 듯 싶다. 왜냐하면 다시한번 강조하지만 이 책이 SSI 에 대해서는 정말 쉽게 이해할 수 있도록 쓰여 있다고 하더라도 SSI 라는 주제 자체가 어렵고, 특히나 처음 접해보는 분야라면 개념 자체도 생소해서 이해하기 힘들수도 있겠다는 생각이 들기 때문이다. 또한 중간중간 개발자들이 사용하는 용어들이 나오는데 물론, 이 책에서 설명해주긴 하지만 지식 없이 읽기는 무리가 있지 않나 싶다.

또한 이 책에서 __하이퍼레저 인디__ 에 대해서 알려주고는 있지만 실습을 깊게 하거나 예제를 많이 들면서 진행하는 방식이 아니기 때문에 단순히 코드를 통한 __개발__ 하는 방법을 알고 싶은 분들은 다른 참고서를 찾아보길 바란다. (물론 아직까지 DID 에 관한 개발 서적은 없다시피 하다.) 작성자의 경우에는 개발도 개발이지만 SSI 에 대한 개념을 제대로 잡아보고 싶어서 이 책을 읽었기 때문에 매우 만족하고 읽은 책이였다.

<img src="https://lh3.googleusercontent.com/pw/ACtC-3dfe6CCDlumkbPuZQWWYBJHjr6BwaMTrGPnASlHk3DbM4GH5vyg2oDv_GlmyH-QsSdMOBuKDvBxrLIKXMLi9OHufPARccz0-dszqP4Aje8iZPJqhTHstZjuvePOCBFsv7_Y2auOYMD5xSH-A5URnDHa=w1680-h1260-no?authuser=0" alt="ssi-02">

책은 초반에 SSI 대한 간단한 개념과 왜 필요한지에 대해서 설명해주면서 시작한다. 여기서 강조하는 점은 SSI 기술 자체가 블록체인을 써야만 한다는 점이 아니라 블록체인을 활용하면 좀 더 편하게 SSI 를 구축할 수 있다는 점을 시사하고 있다.

그 후에 이제 SSI 의 구성요소 중 하나면서 오히려 더 친숙한 DID 에 대해서 설명해준다. 그 후에는 VC, VP 개념에 대해서 설명하고 이러한 SSI 프로젝트를 어떻게 사용되는지 예시를 들어준다. 여기 있는 세 개의 챕터가 정말 중요한데, 이 챕터를 이해하지 못하고 넘어간다면 다음장에서 이어지는 PeerDID, Hyperledger Indy 를 이해하는데 어려움이 많을 것이라 생각한다.

5장에서는 Peer DID, 즉 어떻게 보면 Private 한 DID 에 대해서 설명해준다. 이전 챕터까지는 public 한 환경에서 DID 를 구성했다면 Peer DID 에서는 어떻게 환경을 구성해야 하는지, 그리고 그 요소들은 무엇이 있는지 설명해준다.

그리고 드디어 마지막장에서는 SSI 플랫폼 중에서 가장 유명한 하이퍼레저 인디에 대해서 설명해준다. 만약 앞에서 설명한 개념들을 잘 이해하고 왔다면 Indy 플랫폼 설계를 설명하는 6장은 앞보다는 쉽게 읽힐 것이다. 다만, 이 부분에서는 약간의 코딩이 들어가니 개발자가 아니라면 6.2 장은 넘겨도 될 것 같다.

<img src="https://lh3.googleusercontent.com/pw/ACtC-3cKeIJ8B9FaB59WgDtJK33GLZ5KyNoirY-n7MMcjW1iFfiwS64PaFvhtSrvMTqYwE5FJHJyk4oGC9jNhmVwzqn176FB83fdvNAHLSCp9qrrKYmDE-1PMYM4X9Tifgd3NMg4Np6K8O-hRdQS99Y_PfgI=w1680-h1260-no?authuser=0" alt="ssi-03">

아무래도 작성자의 경우 블록체인 회사에서 근무하고 있기 때문에 다른 사람들보다 더 재미있게 빠져서 읽었을지도 모른다. 하지만 미래의 인증체계는 어떻게 바뀔지 모르기 때문에 블록체인 종사자가 아니더라도 SSI 에 대한 프로토콜(형식)은 알아야 하지 않을까 싶다.

이 책을 읽기 전까지는 DID 와 SSI 에 대한 개념이 머릿속에 난잡하게 엮여 있었는데 이 책을 읽고 나서 머리속에 있던 개념들이 정리 된 느낌이다. 또한 작성자의 경우도 잘 몰랐던 부분들도 더 상세하게 알려주고 있고 초반부터 SSI 에 "Why Blockchain" 이라는 부분도 제시해주기 때문에 더 깔끔하게 읽을 수 있도록 도와줬던 것 같다,

만약 블록체인이라는 산업에 근무하시는 분이라면 당연히 한번쯤 들어봤을 SSI(DID) 에 대해서 개념을 정리하고 더 자세히 알고 싶다면 꼭 이 책을 읽어 볼 것을 권하고 싶다. 또한 훝날 미래의 인증 시스템이 될 확률이 높은 신기술을 먼저 접해보고 싶은 분도 이 책 한권이면 전체적으로 개념을 정리할 수 있을 것으로 생각한다.

