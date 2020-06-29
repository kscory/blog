---
layout: post
comments: true
title:  "[책 리뷰] 우아하게 앤서블"
date:   2020-06-28
author: Cory
categories: Review
permalink : /daliy-life/review/bjpublic-woowa-ansible
tags: review book
---

<img src="https://lh3.googleusercontent.com/pw/ACtC-3dILEThattvrDUS6YDxOkJ_RMYe_VHjHDoAJA9EABLZy7Nt7mEyBYqjLkNHv4H94A0sg_sfcNJvP6TkuTSpf4UbmXRtKGExMQEtFP4xxDP7ZSX5WPQbO3aKcN-0sdu9_4-hI-3uk1XUVYeXbmesdFNf=w1350-h1798-no?authuser=0" alt="woowa-ansible-01">

[책프협(책쓰는 프로그래머 협회)](https://www.facebook.com/groups/techbookwriting/) 에서 회원수 3100 명을 맞아 책 서평을 작성하는 재미있는 이벤트를 기획 했다. 서평 목록에 이전부터 읽어보고 싶어서 눈독들이고 있던 [우아하게 앤서블](http://www.yes24.com/Product/Goods/65306887) 을 보고 바로 신청해서 읽어볼 수 있는 기회를 얻게 되었다.

우선 저자의 경우 [IT 인프라 엔지니어 그룹](https://www.facebook.com/groups/InfraEngineer/) 를 정말 활동적으로 운영하시는 분이고, __인프런__ 에서 [앤서블 강좌](https://www.inflearn.com/instructors/82032/courses) 를 열고 강의하시는 분이시다. 예전에 이 강의를 들으면서 앤서블에 대한 개념을 잡았던 기억이 있어 이 책도 매우 기대하고 있었다.

이 책의 경우 저자가 직접 강의한 앤서블 강좌를 책으로 자세하게 옮겨 놓았다. 차이점은 책에서는 vault 등의 개념이 들어가 있지 않는 반명 네트워크 장비를 테스트할 수 있게 하는 VyOS, Cumulus 를 추가해주었다는 점이다. 그 외의 개념에 대해서는 비슷하게 진행하니 참고하면 좋을 것 같다. 만약 책을 읽은 후 강좌를 보고 싶다면 마지막 심화강의만 보면 될 듯 싶다.

개인적으로 생각했을 때, 앤서블에 입문하고 싶다면 이 책보다 더 나은 책은 없을 것 같다느 생각이 들었다. 작성자의 경우도 이 책 말고도 앤서블 책 두 권 정도가 더 있는데 처음 설치할때부터, 환경구성할때부터 애먹었던 경험이 있었다. 반면에 이 책의 경우 설치부터 시작해서 환경구성하는 법을 정말 자세하게 오류가 안나도록 도와준다. 특히, 실습을 진행할 때 버전이 다름에도 오류가 한번도 안났던 점은 인상깊었었다. 게다가 코드에 대해서 자세하게, 상세하게 설명해 주기 때문에 앤서블에 대해서 쉽게 이해할 수 있었다. 또한 환경을 대부분 vagrant 로 세팅하게 되는데 vagrant 에 대해서 다시 한번 공부 할 수 있는 계기가 된 것 같아서 뿌듯한 마음이 들기도 했다.

다만 조금 아쉬운 점은 도커세팅을 하는 법도 서비스로 넣어줬으면 어땠을까 하는 생각이 들었다. 또한 요즘 대세인 클라우드 환경에서 실습하는 환경이 없어서 조금 아쉬웠다. 하지만 이 책을 읽은 독자라면 스스로 이 정도는 할 수 있지 않을까 싶다.

결론적으로 이야기하면 앤서블에 대해서 아예 모르는, 개념이 없는 독자들도 이 책 한권이면 앤서블을 입문하는데에 어려움이 없을 것으로 생각한다. 만약 앤서블을 새로 공부해야만 하는 상황에 처한 엔지니어라면 일단 이 책부터 읽어볼 것을 권유하고 싶다. 작성자의 경우도 이제 다른 책을 다시 보면 조금 더 이해할 수 있지 않을까 싶다.

<img src="https://lh3.googleusercontent.com/pw/ACtC-3cccmh4FqVOmgdbLY4TAOJdvMnBLjyNYt63UZRSPUWfIaXS5pZ7HsCo_Xo1bNF9Osk2lGSRdqRthHW_a-N7Lzt3D_6vaVw7thZo-D-ONfgYfioI_JLhNABYniGfQNpLQiYbLf7iA2ZUrCgrgqMKE9XY=w2400-h1800-no?authuser=0" alt="woowa-ansible-02">

책에서는 우선 virtual box 에서 앤서블을 설치하고 nginx 를 구성하는 방법에 대해서 알려준다. 그러면서 간단하게 앤서블을 맞볼 수 있게 만들어 준다. 여기서 혹시나 작성자처럼 카페에서 실습하고 싶은 경우 미리미리 os 이미지를 다운받아 놓기를 권하고 싶다. (작성자의 경우 이미지 다운 때문에 다시 집으로 돌아갔다..)

그 다음장부터 베어그란트로 실습환경을 만들어서 앤서블을 테스트해 본다. 우선 CentOS 기반으로 앤서블을 사용해보고 Ubuntu 를 사용하고 Window 를 사용해본다. 여기서 대략 10 개의 노드를 사용해서 실습하는데 이 때쯤 왜 앤서블을 사용하는지 알 것 같기도 했다. 만약 회사의 서버가 10개가 아니라 수백대, 수천대 라면 이런 자동화 툴 없이는 운영하기 어려울 듯 싶었다. 이 장 또한 미리미리 vagrant box 를 전부 다운받아놓고 싫습해서 시간을 아끼도록 하자.

여러 OS 를 실습해봤다면 네트워크 장비도 자동화 시키는 실습을 진행한다. 개인적으로는 이 파트가 가장 흥미로웠다. 아무래도 작성자의 경우 인프라 엔지니어는 아니다보니 생소한 개념이기도 했고, 단순한 서버가 아니라 네트워크 장비도 앤서블로 자동화 시킬 수 있다는 점이 신기했다. 이전에 강의를 들었을 때는 NX-OS 로 실습하면서 따로 싫습환경을 꾸려주지 않았는데 책에서는 VyOS, Cumulus 를 통해서 직접 실습할 수 있도록 구성해준 점도 마음에 들었다.

그 이후에는 앤서블 플레이북에 대해서 좀 더 자세히 배우게 된다. handler, facts 등 인프라를 코드로 관리함에 있어 필수적인 요소들을 배운다. 여기서 이제 control flow 를 이용해서 같은 파일에서 OS 마다 다른 설정을 적용할 수 있는 방법에 대해 배우게 된다.

마지막장에서느 앤서블 role 과 앤서블 갤럭시에 대해서 배우게 된다. 만약 여기까지 배웠다면 아마 코드로 인프라를 관리하는 방법, DevOps 엔지니어 끼리 인프라를 관리하는 코드를 어떻게 공유할 수 있을지에 대해서 이해할 수 있지 않을까 싶다. 

<img src="https://lh3.googleusercontent.com/pw/ACtC-3e6d2k2wPa2wj0pf1nolJ-TWfLeD9OC6pmaPd52KZE5xzMRZ5OIOmjEccZWwCH1eOnHuuU5mcdPTqeBbQ4bpeJjKNl2UftChWy7ax3rHnmYYCsxIUxlH0-qDkP_PxEtkxnuS8cK0M6Xp5QKXgu9SSUC=w2400-h1800-no?authuser=0" alt="woowa-ansible-03">

작성자의 경우에는 아직 실무에 앤서블을 따로 적용하고 있지는 않다. 하지만 최근들어 [비트로](https://bitro.kr/) 라는 서비스를 런칭하면서 계속해서 새로운 환경을 만들어 내는데 고민이 있기는 했었다. 그러다 보니 여러 자동화 툴에대한 관심이 많이 생기고, 운영툴에 대한 관심도 많이 생겨서 이것저것 찾아보면서 공부하고 있는 중이다. 

그런데 우아하게 앤서블 책은 정말 작성자의 수준에 딱 맞게끔 잘 쓰여 있어서 개인적으로는 너무 마음에 드는 책이였다. 앤서블에 입문하고 싶은 사람, 혹은 인프라 자동화에 관심이 있어 한번쯤 보고싶은 사람 모두에게 이 책을 추천하고 싶다. 물론 저자의 인프런 강의를 보는 것도 괜찮지만 __네트워크__ 파트 때문에 현재로써는 책을 더 추천하고 강의의 경우 마지막 심화 강의만 따로 볼 것을 권하고 싶다. 

