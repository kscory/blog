---
layout: post
comments: true
title:  "[책 리뷰] 리팩터링 2판"
date:   2020-04-27
author: Cory
categories: Review
permalink : /daliy-life/review/hanbit-refactoring
tags: review book
---

> 이 책의 리뷰는 한빛미디어 '나는 리뷰어다'로 부터 책을 지원받아 작성된 글입니다.

<img src="https://lh3.googleusercontent.com/CyVXfZxSjfupObAtFE-TuhUne0Y7eGfkLpgxjhUfTdTRzgazJ1ADNAPhTDpSrm5qTH8IshplFIyfGI_V7EdOLMbXYwDkwRWu8hTgoh0RTJFcsVxGAK8aGajEQs1MCvEFp_OQpIbdzg6-gl6YJ75ihRAr0FxM2i21WbLlnThJTT-wJ_aTFwgKHgje8eVTF_ia7xRqN9pkf3jiTkk8OW1qLetoFsb2ZxaQiPrDIOa56aUUPLeundwDieRVHb1ra-gunTcWjQDdXP8Wtocv0xBhqLSR1lDvgNOm9F3pvPJoUurRWtw3HGzYe0Qns6agCKO5cjRoynXOXGR9bcOCHvM4oPhM6ovU5zNa9MnG7hZrXOGcNSxF5pqjq-fXtNJaQq6Sn-6C2OHG7AuxFK_nU_AkXrJSZZJQY8BiBrdL61cgt6LSgDYX8tT25FLP3DQ_gVSHYcdkgWs2embtooEfST_gE1XMR6gFsgm5CsI091Z9SGC5IhA8o5HQrq6pthzasdOHB_rNhRVueDfsYubhmo3eaeTs_ram8qZHw4urbD5li3m92NShUf-sc4GsC7hgSl8FmR2egwA1TWJzxrBFNQEbfwTvL0ePIWBEa5zYltEZ2NWIuSqlk7X89omhPp925h-1Bb7o8s9D1xu8y5cwGT5-OVmifBS4FudtWucWIeOZWzjE_uaSHCmo0_tRPVbuceblf-Qp7nzLgN7VMCbkpS4mGvor4fSy2_QWvfLiYoCAAvCo_xNve8OMj80=w720-h960-no" alt="refactoring-01">

원래 리팩터링 1판을 읽고 싶어서 회사에 책을 요청한 적이 있었다. 그런데 책이 절판되어 더 이상 읽을 수 없었는데 드디어 2판이 출간되면서 얼른 이 책을 읽어보게 되었다.

저자인 __마틴 파울러__ 의 경우 클린코드, 클린아키텍처 등 프로그래밍 지식에 대한 여러 책을 출간했고 그 책들 하나하나가 정말 주옥같은 책들이기 때문에 이 책도 많은 기대를 했었다. 역시 그 기대에 맞게 책 내용이 정말 마음에 들었다. 

개인적으로 가장 인상깊었던 부분은 리펙터링에 대한 정의에 대해서 설명해줄 때였다. "코드 베이스를 정리하거나 구조를 바꾼느 모든 작업은 재구성이라 의미하며, 리팩터링은 재구성 중 특수한 한 형태다" 라는 말과 함께 리펙터링을 함에 있어서는 테스트코드가 깨지지 않게 하라는 것이다. 이 부분을 보았을때 내가 하던 작업은 사실 __리펙터링이 아니라 재구성__ 작업이었다는 사실을 깨닫게 되었다.

이 책은 1판과는 다르게 __자바스크립트__ 를 이용해서 리팩터링을 진행한다고 한다. __자바__ 개발자에게는 조금 아쉬운 부분일 수도 있겠지만 개인적으로 자바스크립트를 더 많이 쓰는 작성자의 경우 더 마음에 들었던 부분이었다. 물론, 어려운 자바스크립트 문법을 사용하지는 않으니 책을 읽는 데 있어 문제는 없을 것으로 생각한다.

즉, 책에서는 재구성이 아니라 리펙터링 하는 방법에 대해서 알려주고 있다. 하나의 함수를 바꾸더라도 그냥 바꾸는 것이 아니라 단계별로 어떻게 해야 프로그램이 안깨지는지를 설명하면서 바꿔준다. 다시 말하자면, 리펙터링 중간에 프로그램을 실행시켜도 에러가 발생하지 않고 프로그램이 실행되는 방법에 대해서 알려준다.

물론, 이런 방식으로 계속 리팩터리을 하게 되면 이전에 했던 단순히 재구성을 하는 방법보다 시간이 더 오래 걸릴 수도 있다. 예를 들어, 함수 이름을 변경하는데 하나하나 프로세스를 밟아야 한다는 것은 조금 번거로울 수 있는 작업일 수 있기 때문이다. 그렇지만 리팩터링 절차를 거치지 않고 재구성을 하게 된다면 나중에 에러가 발생할 확률이 더 늘어날 테니 어떻게 보면 나중을 위해서는 필수적인 절차가 아닐까 싶기도 하다.

<img src="https://lh3.googleusercontent.com/9-VtYFR2dZkpKtZYQLfKmaY52cEdlZWj60mGDoDCnBu10JNamclxa1azTMJfmPAYPJVrIAIyCpeUjLYpD8abFUNOcwk1dFTlxbiqZXyrLXihYJ5C9PNhj-ldEcqJt-EnlP8ZAH23mwPctcrHdEtX-cnVS678gvbyRxKcRmGLIzesqgKV4x4kpqhBF218x8Y6PJOXGPXdha02ZQvdm7_Vypcfjsu3G6F5ZBNwJm_FQNlM9a0VTpl0ESM4wl2JGGrhW5yhE63w9zMqD9p85UXMeaXtAc9QR-8AOHoj4-j18tTiaBKVDneZgcFraxJL3sbysru-RNEh47ZUQVn7TiZgqbl8bjBIJWEYwDM8nt5w-DEnvCm2EuijFbo4Y_x9rP0Rr_DcU7xnKFv169cIqJ4ZmVDB4k5IM85aph6ujRs4ATgcp6hL16k_5L_JM8jMEZEux6l4BWtXmEC8SNX5x6Hd65UBfztTOolftXN2uKX4J77nL_I3iv7SZ-M8EDXpzXBD0znKmNb2xnoOR28Bav2LWN2oe6o3D89SQIEruqBauR61vEbt1eKUtY5miNplyR1ZdBQZpZf3RP-vf0Z8TBqhobGOrQThuGenW5AdjnYG8C_xQLnFlSrwwTMS50SDrPUVhMJ9IrtQ2Y0w-VL0bpWsW0JxKOtwZwlEMICO1zesKYnUB0P7Ph_flUuWCTmveYBAaYIWGfY5QQYidE3GGU8pBXhIbN5hx9RKdLwWWNCkeQjWcnTWTArF2tU=w960-h720-no" alt="refactoring-02">

책은 위 사진에서 보듯이 리팩터링 기법에 대해서 설명해준다. 그러면서 각각의 배경과 절차에 대해서 알려주고 간단한 기법은 예시 없이 넘어가기도 한다. 하지만 조금 어렵거나 복잡한 기법들은 아래 사진처럼 여러 예시들을 들어가며 단계 단계별로 리팩터링 하는 방식에 대해서 설명해준다.

1장에서는 전체적으로 어떻게 리팩터링을 해야 하는지 알려준다. 그 후 2장에서는 리팩터링이 무엇인지에 대한 개념에 대해서 설명한다. 3장에서는 왜 리팩터링을 해야 하는지 이유에 대해서 알려주며 4장에서는 리팩터링하는데 필수적인 테스트 코드를 만드는 방법에 대해서 설명해준다. 5장은 리팩터링 기법에 대해서 설명하기 전에 어떻게 앞으로 읽어야 하는지 알려준다.

그 후 6장 부터 리팩터링 기법에 대한 설명이 시작된다. 처음 기본적인 리팩터링 기법에 대한 설명으로 시작해서 캡슐화, 기능 이동, API 리팩터링 방법 등 정말 많은 리팩터링 기법에 대해서 설명하고 있다.

물론 이론 책의 특성상 따라 치면서 보기는 애매한 부분들이 많다. 그보다는 실제로 자신이 리팩터링 할 때의 레퍼런스로 참고해서 보면 어떻까 싶다.

<img src="https://lh3.googleusercontent.com/wBpSFPqG8YNzdblYw88dCZeH4klehZClvCIypfkoRwmBiKHkIoUguffvNTwgnTPkjAKLvaY4wTagyrK8Nr-PPKPB8o351bl1hAHquvfyZlUnj7eT9nH7y1Qvanj8i6IDwnExpIb1OR_vaM1rdOM3uihPizHBk9O6pxAZtPjFixLW8-VKtdvXHcZnnzpAdwMDxCTwuZga2i4HHUtg3biq0eKQCQfyRgfInjCBhvq_PtN8GdCV6NOL6LgCAk0XSW9rll3x6P8bU5GUHxsPiAo891ZCZReTfbjGjnLGnoTImBd-ycjK8DLjT5czqN1gLOPiXm9zbGvtA_dcKv0KzNershCyYETahjKAiM-zhl2gqAnCA88T7UEJZprljtWEHe3_oeBj7zAuCtjLr1iJEg68txZgg70czYajX-rREyPVX1lBc0v1sFdVe740k9ZmLORDScPPGDcRKjoowINKzafpN_sEM3p-63U6AaSUPC5oHgwBhiPDuPyWQNHv1oCbvhWD9F4U7nvsIZQ9-XgT6UEzrgFTy72ZRjUo7qixTm-593pRFwj0Pgii2ha0ghY6xMVWuJ-fNVRs3D01O7w_bJJVG1z8GcDRlcev5AhOtDKhxYcUPm4Ug3EKZ3lX50s-GHTKdMOJdiUOGmjhvD3rJe-i5N0S-wLIY7XXGtwlJ6jX0XNWmv0HiFYZLKgyM_eqHv1lKBykv8xAWo1eK4ZujSZP3D4fitB13QFFxqmw5XHWjcidfITmU74LxQw=w960-h720-no" alt="refactoring-03">

작성자의 경우 최근에 서비스를 오픈했기 때문에 이제 개발했던 코드의 악취를 리팩터링을 해서 개선해야 하는 시기가 왔다. 그래서 그런지 이 책이 더 와닿았던 건 사실이다. 물론 그렇지 않은 개발자들도 이 책을 보게 되면 앞으로 어떻게 리팩터링 해야 겠구나 하는 생각이 들 것 같다.

알고 안쓰는 것과 모르고 안쓰는 것은 다르기 때문에 개인적으로는 이 책을 전국에 있는 모든 개발자가 한번쯤 읽어야 하는 필독서 중 하나가 아닐까 싶다.

