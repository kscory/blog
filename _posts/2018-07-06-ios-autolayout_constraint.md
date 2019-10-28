---
layout: post
comments: true
title:  "AutoLayout - Constraint 속성"
date:   2018-07-06 12:41:10
author: Cory
categories: IOS
permalink : /dev/ios/autolayout-constraint
tags: swift ios
---
ios 에서는 기본 view 로 Autolayout 을 story board 형태로 제공하고 있습니다. 그 때문에 나름 view 를 그리기 쉽게 되었지만 생각해야 할 속성들이 늘어나게 되었습니다. 물론, 스토리보드만으로는 한계가 있지만 여기서 기본적으로 제공하는 AutoLayout 속성을 잘 활용하게 되면 많은 아름다운 UI 를 코드 없이도 쉽게 그릴 수 있을 것으로 생각합니다. 또한 나중에 하드코딩으로 view 에 속성을 넣어주어야 할 때에 참고할 수 있을 것입니다.

## Constraints
consonants 란 두 개의 view 사이의 간격이라고 생각하면 될 것 같습니다.

### view 가 한개인 경우
view 가 한개인 경우는 단순합니다. 아래 그림처럼 스토리보드의 Constraints 에 관한 아이콘을 선택한 후 속성을 설정해 주면 됩니다. 설정을 해주면 오른쪽 그림 처럼 상하좌우로 설정한 constraint 가 적용됩니다.

<img src="/assets/ios/autolayout/constraint1.png" title="constraints1">

여기서 봐야할 것은 Utility Inspector 에서 각각의 constraint 속성은 이름에 맞게 __Top, Bottom, Leading(왼쪽), Trailing(오른쪽)__ 으로 되어 있고 여기서 편집이 가능하다는 것입니다. (그런데, 속성 네임이 Left 가 아닌 이유는 글을 쓸 때 오른쪽에서부터 시작하는 국가들이 존재하기 때문에 헤깔리지 말라고 정한 것 같습니다.) 그러면 이번에 각각 간격을 20 으로 수정해보면 아래 그림과 같이 20이라는 간격에 맞게 view 가 꽉 차는 것을 볼 수 있습니다.

<img src="/assets/ios/autolayout/constraint2.png" title="constraints2">

### view 가 두개 이상인 경우
위와 같이 버튼이나, label 같이 크기가 정해진 widget 이 아닌 두개 이상의 view 를 생성하여 constraint 를 지정할 경우 아래와 같은 오류가 발생합니다. 이는 view 의 크기는 정해져 있지 않아 어떤 크기를 기준으로 해야 하는지 알 수 없어 생기는 오류입니다.

<img src="/assets/ios/autolayout/constraint3.png" title="constraints3">

이 경우에는 크기를 지정해주거나 두 view 의 크기를 parent 전체 크기 비율로 설정해 줄 수 있습니다. <br>
크기를 지정하기 위해서는 위에서 constraints 를 지정하는 데에 width 의 크기를 지정해 줄 수 있고, 만약 크기의 를 동일하게 설정하려면 두 view 를 동시에 클릭한 후 같은 아이콘에서 Equal Width 를 선택해 주면 됩니다. (혹은 하나의 view 에서 다른 view 로 마우스오른쪽으로 드래그하거나, control+드래그 후 설정하시면 됩니다.)

<img src="/assets/ios/autolayout/constraint8.png" title="constraints8">

아래는 equal width 를 설정하였을 때의 그림입니다.

<img src="/assets/ios/autolayout/constraint4.png" title="constraints4">

그런데 여기서는 속성에 주의해서 코딩해야 합니다. 만약 equal width 를 설정하는 방법을 잘못 하게 되면 아래와 같은 현상이 벌어질 수 있습니다.

<img src="/assets/ios/autolayout/constraint5.png" title="constraints5">

아마 위와 같은 현상을 원하지는 않을 것입니다. 저라면 정확하게 가로를 세등분으로 나뉘어져 있는 것을 원할 것 같습니다. 이를 위해서 constraint 속성을  Utility Inspector 에서 더블클릭하여 직접 변경해 주어야 합니다. 살짝 들여다 보면 _First item_, _Relation_, _Second item_, _Constant_, _Multiplier_ 가 존재합니다. 이것이 의미하는 바는, constraint 와 width/height 에 따라 달라지는데 아래와 같이 생각할 수 있습니다.
- Constraint 관계 : First Item 의 특정 방향(leading, trailing 등) 은 Second item 의 특정방향과 constant 만큼 Relation 관계로 떨어져 있다.
- Width/Height : First Item 의 width/height 를 기준으로 Second item 의 width/height 를 동일하게 하겠다. (혹은 Multiplier 의 비율로 설정하겠다.)

아래는 width 를 각각 1:2:3 으로 first item 을 모두 회색의 view 로 설정했을 때의 모습입니다.

<img src="/assets/ios/autolayout/constraint6.png" title="constraints6">

추가로 아래 그림에서 핑크색 view 와 보라색 view 에는 top 에 대한 constraint 를 주지 않고 gray 만 height 를 주고 equal height 만 주어 gray 의 height 로 따라가게끔 할 수도 있습니다.

### constant 수정을 마우스로 & 단축키로?
만약 수정을 하고 싶은 경우 마우스로 설정한 다음 `update constraints` 를 클릭해주면 됩니다. 이 기능은 constants 아이콘 옆에 존재합니다.<br>
참고로 update frame 도 존재하는데 이는 마우스로 잘못 적용하였거나, 버튼이나 레이블을 잘못 옮겼을 경우에 원래 상태로 변경하겠다는 것을 의미합니다.(command+option+= 으로 설정 가능 혹은 아이콘)<br>
마지막으로 모든 constant 를 초기화 하고 싶다면 clear constants 를 선택하면 됩니다.

### 가로방향?
가로방향일 때도 constants 로 설정을 하면 아래 그림과 같이 비율에 맞게 나오게 됩니다. 특히, ios 버전이 달라져도 비율에 맞게 같은 크기의 view 를 설정할 수 있게 됩니다.

<img src="/assets/ios/autolayout/constraint7.png" title="constraints7">
