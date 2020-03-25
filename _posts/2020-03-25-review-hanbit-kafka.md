---
layout: post
comments: true
title:  "[책 리뷰] 실전 아파치 카프카"
date:   2020-03-25
author: Cory
categories: Review
permalink : /daliy-life/review/kafka_practice
tags: review book
---

> 이 책의 리뷰는 한빛미디어 '나는 리뷰어다'로 부터 책을 지원받아 작성된 글입니다.

<img src="https://lh3.googleusercontent.com/8ow3E7B9iY7pbs8uZy5bmdi09KPs4rakbfn8hNNhKSS1g9gBIJcnIr_efLVM5_IGnxzOHEESNaS2yS8cBY57v6xVnBag2JkO6HHLEv2Cp36gqlNWjRrqfvfa8sUsj3Y9GBlX4OTOUXf8XhcPW8v018tg2p_HTUR2IqZDFnEF4QiH6LhHtxBQ0IXdjklSwtjJvY2E7jR1UCU1s1XJXoGDEX1pi4xjkUKv2peoBNADQ9mIt3bz1Hw56HyogIOd4YIFEohMg1NQiD9WXoqXru6igp4baHEMt-4quk5FmjmJFs4i1Yw5ii1WbyF3sEgmJT8azUMsrwygLkvbIKmhI4RZeRLU43ZKy3YUElFhEGK8ZfoNFqITOAwnZX8_GNdeX-W06vyCzwQnZXO9BfbMOP-n02096ERY82_WqBeHkvr3WM5W4x0DpZXrpylvS-NUS8hF72tuvHsp21oxZv_S831lQTjez5oF7xPyDSzGAcNfsHxdEE5T8qTFghLuHyO9T_0EgNWAjThMo565zyWNowhOPlNKv31vGtTT6HnscgRcENiigV1FaQkW6BAYMzbMI2iOAeFzeXyh4T3OsRoQLBjOzBeEENguAdrnCws7sOLoshOd-7cS0_BBcek-FI79lnnFkoRrps3EgtugoD7xQN-ZR9Mc087wI-W1O9fIc2XNLq4Wu3k1FVUjfbmn6ALDLIOdNEJK62EAktVuQ7sj5j-IL0HpNVjiP8vV-dVCUi4R9MwMbxOX-8vypI0=w1350-h1800-no" alt="kafka_practice-01">

한번쯤 카프카를 공부해보고 싶었는데 마침 리뷰할 수 있는 책 중에 [실전 아파치 카프카](http://www.hanbit.co.kr/media/books/book_view.html?p_code=B8503179529) 가 있어서 지원해서 읽어보게 되었다.

작성자의 경우 예전에 __하이퍼레져 Fabric__ 의 구성요소 중 하나가 Kafka 로 되어 있어서 그 때 잠깐 Kafka 를 건드려 본 것 빼놓고는 정말 초보적인 지식밖에 없었다. 그렇기 때문에 이 리뷰글은 __입문자__ 관점에서 적었다고 생각하면 좋을 것 같다.

일단 이 책에서 가장 마음에 들었던 부분은 아래 사진에도 나와 있듯이 실습을 함에 있어서 기본적은 모든 환경설정을 알려준다는 것이다. 일반적으로 개발 환경 실습을 할때는 "자바8 버전을 설치하세요", "메이븐 최신을 설치하세요", 등에 대해서 이야기하는데, 이 책에서는 하나하나 자세히 알려주기 때문에 실습을 함에 있어서 딱히 막힘없이 진행하 수 있었던 것 같다.

특히 카프카를 설치하 때 일단 실습을 쉽게 하기 위해서 __confluent__ 플랫폼을 선택한 점도 눈에 띄는 것 같다. 물론, 뭔가를 개발함에 있어서 설치가 가장 어려운 부분에 속하지만, 작성자의 관점에서는 일단 카프카 자체가 어떻게 돌아가는지 궁금하기 때문에 환경 구성에 대해서는 자세히 설명하면서도 쉽게 설치해서 실습할 수 있도록 유도한 부분에 대해서는 긍정적으로 생각한다.

또한 카프카의 주요 사용 방법에 대해서 설명하고 이를 하나씩 실습을 통해서 설명해 주는 것도 좋았다. 그렇게 실습하면서 카프카를 언제 사용해야 하는지, 왜 카프카를 사용해야 하는지 조금은 납득할 수 있었던 것 같다.

다만 입문자가 아닌 사람의 관점에서는 너무 쉬운 기초적인 부분만 알려주는 것 아닌가? 하는 생각이 들 것 같다. 또한 이 책 자체가 __입문__ 하는 사람의 위주로 쓰여서 그런지, 카프카 뿐 아니라 __spark, MQTT 프로토콜__ 등의 기초적인 부분들을 알려준다. 어떻게 생각하면 이런 부분은 조금 __카프카 자체를 알고 싶어 하는 사람__ 에게는 쓸데 없는 설명이 아니었을까 하는 생각도 들었다.

이러한 부분을 생각해 봤을 때 총 평은 __예제와 함께 실습하는 것을 좋아하는 사람을 위한 카프카 입문서__ 이라고 평하고 싶다.

<img src="https://lh3.googleusercontent.com/Z8RlF_hjVKVwumngtvUSNwKpKtdtk1SNzratELzyXW0hoLcf7xz1TOWt-wJ8mW5yVTJvYLCw-el7auFNzJz6i27bNZLWc0OtJ2TewiWPLCupAeSNrBt8taeO3ufAJfIq7vOJ4Ho-T_HC0xCLeQ6iJ5ocXy2IFuMiHMYzti1PJ1ZFa7UvgEimA1lWjNAwaROnxOyQsmltYz7mAIhiUg9eSkDoQ40P6Zy947k3vIUa1tlJeSibtbLUftKNqYK3fk6QsNoseNasjoc8FhV7-i7JEipj1Qsp-o8Z8u0rCkUA5wacb3ANqtRz7B7qZGUt65M0YF1xQFd57C0TrOHYdH72RiWdp-8GWCw90VUaIfS7wGz_zo__EMSCxlXD0rH8Oa4jmUoUwl8A6GTMJzyDlwbCOpHAYa34oJGZyfYSHjwpfPPB6oBv8Ed88oXBGDt_uHGQtDBpioPhX9mlAvrcqYllR9t3gbe9F7NEGwpMvkX4jQotHQnVuG1cxtJf9KW5zh80dgyUUeOfPtT4Som2J8kKWefky8kjEZVYJ8Q7yyKzzLH87lkezTOnZoWflqLGL-eNG3GyN9ekHvgiz-3kMtbyeIJd5zMZcdk8W3w9EpIiCCcKAnN6i5dkynIEOhXxXC6WYNwPtxV4yq_qyK1tvAClv2bwnhD547bFOQMybz7QuA52wHH2zJLD_mMtYR7WZ1E1GBsWwO8K4Vv54sNfGXM4OfT51jHLjE_j6n5EfXdE_6NhM-3t5_DsoY8=w2400-h1800-no" alt="kafka_practice-02">

책의 1장은 모든 다른 여타 책과 동일하게 카프카의 역사, 카프카가 쓰이는 이유 및 유스케이이스에 대해서 설명해준다. 그 후에 카프카의 구성에 대해서 이야기 해주고, 곧바로 기본적인 카프카 설치하고 간단히 producer, consumer 를 만들어서 실습해본다.

그 후 2장에서는 1장에서 설명한 유스케이스들에 대해서 실습하는 시간을 가지게 된다. "데이터 허브로 사용하는 방법", "스트리밍 처리하는 방법(로그 처리)", "사물인터넷 처리하기" 의 크게 세가지로 구분해서 알려준다. 

그 후 마지막장에서는 카프카를 운영할 때 주의할 점에 대해서 몇 가지 설명해주고 있으며, 부록에서는 앞에서 이야기하지 못한 몇가지 심화 개념에 대해서 설명해준다.

실습은 아래 사진과 같이 먼저 유스케이스에 대해서 설명한 후 이를 위한 환경 구축, 그 후에 카프카를 연결해서 개발해 나가는 식으로 진행이 된다. 총 실습과정은 대략 4~5 가지의 경우를 예시로 들고 있다.

<img src="https://lh3.googleusercontent.com/zWVkseJGFajlw6Oz7ZwwMhA-yZ0ucgeSUMi1ZANwubYNIZNYSJ7xZI3jIXy09uDUkEaXDz28gFAYIpTTKuIlf0YjC9Aton1Vz30ll5yO4SfB9kQUpbmCzBDZTjRPKGJKIeKvbRlMuuR2gg1Z5Hftixb48d_HjxTGl8jo--ucSbkE0Avzn13UEV_GVW6_d5a2lQeB1P7jwKZ4ia5l9MPX_B3Ii_l8D-i95lQuLHko5xScnycebHcFw7QytvjHvEOQ6O-KNjXtjgzi0bCzQDbCsJywpkdfZn9OqHDvXxrKIQ2XUVQMbZG74-d7OO4A845LkqTzdgkxKHbLFtrppAYxR9RBHtZ9NOmzrGt8NOCOAL9iL4etCMKmKOYQyW8Yk6gzaZ8jHOEfzlQWxF3x9tzR7bUeWCphVObpH37PkSFLuCGaWrXO6AsV96HUoyHzfYF7wrkoVhEXMC-olyAkqy3nXcZczWwJvdh46oJvuL6W-DqHUSFEMeG-JXGtjiEwgHbDE5uFPv85GJb9xtW67-y-G7a5ETLGWW9BZp72DnVNjE4RsJ4Sig3qvUrSDRd6pvaLK2pwsra_beGXdHodeXqOyS_Ved5tQY4OuzPTMwjaXw-HSjYfKwtQ8eApIfPkOblX5iy1cN2bmy2SEpo2gBsRC2t48jBBDP6HofNf8t-e9nbbOU-DmFAvndIrWXxIXw=w559-h419-no" alt="kafka_practice-03">

작성자의 경우 사실, 현재 카프카를 쓰는 것은 오버엔지니어링이라고 생각해서 따로 공부한 적이 없었다. 하지만 진짜로 그럴까 하는 궁금증이 있었는데 이 책을 읽으면서 더 명확해진 것 같다. (실제로 오버엔지니어링임이 틀림없다.)

카프카를 사용하기 위해선느 일단 브로커가 5대 이상 필요하다는 것 자체가 트래픽이 없는 회사(현재 작성자는 창업 시작단계)에서는 돈만 많이 들고, 개발자 자체도 몇명 없는데 카프카까지 관리하려면 정말 힘들 것이다.

하지만 이런 판단을 하기 위해서는 어느 정도는 공부를 해보고 리서치를 해 본 후에 결정해야 한다고 생각한다. 그런 관점에서 보았을 때, 이 책을 한번 실습해보고 읽어보는 것이 카프카를 쓸지 안 쓸지 결정할 때 도움이 될 것으로 보인다.

또한 개인적으로는 책이 쉽게 쓰여 있어 입문자 입장에서는 재밌게 실습해볼 수 있었던 것 같다. (물론 어느정도의 리눅스 지식은 있어야 한다.) 다시 한번 카프카에 입문하고 싶다면 이 책을 정말 추천하고 싶다.
