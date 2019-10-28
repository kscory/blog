---
layout: post
comments: true
title:  "[Design Pattern] State 패턴"
date:   2019-06-13
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/state
tags: design pattern
---

__State Pattern__ 이란 객체 내부의 상태가 바뀜에 따라 객체의 행동을 바꿀 수 있는 패턴을 의미합니다.

행동을 변경하는 메소드들로 이루어진 인터페이스를 정의하고, 인터페이스 구현에서 각 상태에 맞는 행동을 정의함으로써 분기문(ex> if, switch) 을 캡슐화, 분리화 하는 패턴입니다.


<img src="/assets/design/state/state-01.png" alt="state-01" style="width:60%;">

### 사용하는 이유

객체들은 종종 상태에 따라 행동을 변화시킬 필요가 있습니다. 이 때 가장 간단한 방법이 if/else, switch 문인데, 이런 구조는 상태 테이블을 변화시키거나 새로운 상태를 추가하기 어렵게 되며, 새로운 상태가 추가된다면 만은 메서드를 변경해 주어야 합니다. 즉, 유지보수하기 어려운 구조를 가지게 됩니다.

그런데 만약 State 패턴을 사용하게 된다면 분기문을 제거할 수 있습니다. State 패턴에서는 각 상태가 State 객체를 통해 표현되고, 각 State 객체는 특정 상태에 있는 Context 객체의 행위를 구현하게 됩니다.

간단한 사용 예로 페이지 이동에 대한 로직을 생각해보겠습니다.

먼저 분기문을 사용하지 않고 사용한 예시입니다.

```java
public class PageService {

    final static int INDEX_PAGE = 0;
    final static int PROFILE_PAGE = 2;
    final static int POST_PAGE = 1;
    final static int SIGNUP_PAGE = 3;

    Stack<Integer> backPageState = new Stack<>();

    public void goBack() {
        int backState = backPageState.pop();

        if (backState == INDEX_PAGE) {
            // INDEX_PAGE 로 돌아갈 때 필요한 로직을 실행한다.
        } else if (backState == PROFILE_PAGE) {
            // PROFILE_PAGE 로 돌아갈 때 필요한 로직을 실행한다.
        } else if (backState == POST_PAGE) {
            // POST_PAGE 로 돌아갈 때 필요한 로직을 실행한다.
        } else if (backState == SIGNUP_PAGE) {
            // SIGNUP_PAGE 로 돌아갈 때 필요한 로직을 실행한다.
        }
    }

    public void enterPage(int state) {
        if (state == INDEX_PAGE) {
            // back stack 에 페이지를 추가해준다.
            backPageState.push(INDEX_PAGE);
            
            // INDEX_PAGE 로 접근할 때 필요한 로직을 실행한다

        } else if (state == PROFILE_PAGE) {
            // back stack 에 페이지를 추가해준다.
            backPageState.push(INDEX_PAGE);

            // PROFILE_PAGE 로 접근할 때 필요한 로직을 실행한다

        } else if (state == POST_PAGE) {
            // back stack 에 페이지를 추가해준다.
            backPageState.push(INDEX_PAGE);

            // POST_PAGE 로 접근할 때 필요한 로직을 실행한다

        } else if (state == SIGNUP_PAGE) {
            // back stack 에 페이지를 추가해준다.
            backPageState.push(INDEX_PAGE);

            // SIGNUP_PAGE 로 접근할 때 필요한 로직을 실행한다
        }
    }
}
```

위와 같이 작성하면 중복코드가 많이 생길꺼 같고, 만약 더 제약이 생긴다면 유지보수도 힘들 것 같이 보입니다. 만약 이를 State 패턴을 이용한다고 하면 아래와 같이 나타낼 수 있을 것 같습니다.

그럼 이제 State 패턴을 한번 적용해서 리펙토링 해보겠습니다.

```java
// State
public interface PageState {
    void goBack();
    void enterPage();
}
```

```java
// Concrete State
public class IndexPageState implements PageState {
    @Override
    public void goBack() {
        // INDEX_PAGE 로 돌아갈 때 필요한 로직을 실행한다.
    }

    @Override
    public void enterPage() {
        // INDEX_PAGE 로 접근할 때 필요한 로직을 실행한다.
    }
}
```
나머지 Page State 액션도 위와 같이 만들어주고 이제 Context 에서 사용할 수 있습니다.

```java
// Context
public class PageService {
    PageState indexState = new IndexPageState();
    PageState postState = new PostPageState();
    PageState profileState = new ProfilePageState();
    PageState SignUpState = new SignUpPageState();

    Stack<PageState> backPageState = new Stack<>();

    public void goBack() {
        PageState state = backPageState.pop(); // state 를 변경
        state.goBack();
    }

    public void enterPage(PageState pageState) {
        backPageState.push(pageState);
        state.enterPage();
    }
}

```

위와같이 만들게 되면, 만약 다른 페이지가 생기더라도 단순히 State 만 만들어서 거기서 로직을 진행하면 됩니다. 또한 Enum 과 함께 쓰면 더 훌륭한 패턴이 될거 같다는 생각이 듭니다.

그런데 여기서 Strategy 패턴과의 차이점? 뭔지 의문이 들고, 다른데서도 많이 정리한 걸 볼 수 있습니다. 단순하게 이야기하자면 두 패턴이 사용되는 목적이 State 패턴은 __분기문을 줄이는 데__ Strategy 패턴은 __상속을 줄이는 데__ 있으며, 코드상에서는 State 패턴에서는 __Context 내 에서 State 를 정의하고 바꿔가며__  사용하고 있지만 Strategy 패턴은 __외부에서 Strategy 를 주입__ 받아 사용한다는 데에 있습니다.

### 장점과 단점

그럼 state 패턴의 장단점을 알아보겠습니다.

장점
1. State 메커니즘은 상태에 대한 모든 행동양식이 한 곳에 있기 때문엥 유지보수하기  편합니다.
2. 메서드 상의 긴 분기문들을 제거하 할 수 있습니다.

단점
1. 클래스 갯수가 많아져서 오히려 유지보수가 힘들어질 가능성이 존재합니다.
2. 상태에 따라 변하는 메서드의 숫자가 적다면 오히려 불필요한 복잡성을 추가할 수 있습니다.

### 실제 사용 예제
생각해보면 백앤드에서는 State 를 DB 에 저장하고 관리하는 경우가 많기 때문에 이를 Enum type 을 사용해서 DB 와 함께 적용할 수 있을 것으로 생각됩니다. 즉, 예시로 들면 만약 게시글의 상태 따라 유저에게 어떻게 보여줘야 할지 적용하는 경우 아래와 같이 만들 수 있을 것입니다. 

```java
// State
public interface BoardStateOperation {
    BoardState checkIsGetBoard();
    BoardState requestNextStep();
}
public enum BoardState implements BoardStateOperation {
    INIT(new InitRso()),
    NOT_OPEN(new NotOpenRso()),
    OPEN(new OpenRso());
    
    private final BoardStateOperation operations;
    
    BoardState(BoardStateOperation operations) {
        this.operations = operations;
    }
    
    @Override
    public BoardState checkIsGetBoard() {
        return operations.getBoard();
    }
 
    @Override
    public BoardState requestNextStep() {
        return operations.requestNextStep();
    }
}
public class InitRso implements BoardStateOperation {
 
    @Override
    public BoardState checkIsGetBoard() {
        // 작성자가 아니면
        throw Exception("접근할 수 없는 유저");
        // 작성자라면
        return BoardState.INIT;
    }
    
    @Override
    public BoardState requestNextStep() {
        // ~~~ 진행
        return BoardState.NOT_OPEN;
    }
 
}


// Entity
@Data
@Entity
@Table
public class Board implements Serializable {

    @Id
    @Column
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long seq;

    @Column
    private String title;
    
    // ...

    @Column
    @Enumerated(EnumType.STRING) // enum 타입으로 저장(String 으로 저장됨)
    private BoardState boardState;

    // ...

    public void checkIsGetBoard() {
        setBoardState(boardState.getBoard(this));
    }
    
    public void requestNextStep() {
        setBoardState(boardState.requestNextStep(this));
    }
}

// Service
@Service
public class BoardService() {

    @Autowired
    BoardRepository boardRepository;

    public Board getBoard(Long id) {
        Board board = boardRepository.findById(id);
        board.checkIsGetBoard();

        return board;
    }
}
```

조금 복잡해 보일지 모르지만 BoardState 에 따라 Operation 을 정의하고 이를 Entity 에서 가지고 있다가 State 에 따라 다른 로직을 실행하게 됩니다. 결국 Service 레이어에서 이를 체크해서 사용할 수 있게 됩니다.

이렇게 해서 State 패턴에 대해서 알아봤습니다. 

요즘 공부한 디자인 패턴을 생각하면서 코딩하니 더 재밌게 코딩하고 있는 저를 발견하곤 합니다.
