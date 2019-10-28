---
layout: post
comments: true
title:  "[Design Pattern] Facade 패턴"
date:   2019-07-21
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/facade
tags: design pattern
---

__Facade Pattern__ 이란 하나의 인터페이스에서 복잡한 서브시스템을 통합하여 동작시킬 수 있도록 제공해주는 패턴입니다. 즉, 복잡한 일련의 작업들이 있을 때, 이를 사용자가 이해하기 쉽게끔 하나의 큰 인터페이스에서 하나의 객체인 것 처럼 다루게 됩니다.

아래의 클래스 다이어그램에서도 보듯이 __Subsystem Class__ 를 상위 __Facade__ 에서 혼합하여 client 가 요청할 수 있는 간단한 함수를 제공합니다.

<img src="/assets/design/facade/facade-01.png" alt="facade-01">

### 사용하는 이유

퍼사드 패턴은 새로운 서비스를 개발해야 해서, 서브 시스템들을 사용하고자 할 때, 이를 하나로 묶어주는 역할을 하는 클래스를 만드는 것입니다. 즉, 만약 새로운 클래스가 같은 layer 상으로 서브시스템과 엮게 된다면 이 시스템은 스파게티 코드같이 꼬이게 되고, 유지보수하기 어려워 질 것입니다.

[위키피디아](https://en.wikipedia.org/wiki/Facade_pattern) 의 예를 한번 들어보겠습니다.

```java
/* Complex parts */

class CPU {
    public void freeze() { ... }
    public void jump(long position) { ... }
    public void execute() { ... }
}

class HardDrive {
    public byte[] read(long lba, int size) { ... }
}

class Memory {
    public void load(long position, byte[] data) { ... }
}

/* Facade */

class ComputerFacade {
    private final CPU processor;
    private final Memory ram;
    private final HardDrive hd;

    public ComputerFacade() {
        this.processor = new CPU();
        this.ram = new Memory();
        this.hd = new HardDrive();
    }

    public void start() {
        processor.freeze();
        ram.load(BOOT_ADDRESS, hd.read(BOOT_SECTOR, SECTOR_SIZE));
        processor.jump(BOOT_ADDRESS);
        processor.execute();
    }
}

/* Client */

class You {
    public static void main(String[] args) {
        ComputerFacade computer = new ComputerFacade();
        computer.start();
    }
}
```

Computer 를 실행하려면 CPU, Memory, HardDrive 등이 필요하고 특정한 순서로 진행되어야 합니다. 그런데 이를 Facade 패턴을 이용하여 Client 입장에서는 단순히 "Computer 를 시작한다" 는 단순한 명령어로 끝낼 수 있게 됩니다.

만약, Facade 패턴을 사용하지 않으면 Client 는 CPU, Memory, HardDrive 등에 직접적으로 접근해서 사용해야 합니다. 만약 실제 상황에서도 컴퓨터를 킬 때, 전원버튼으로 컴퓨터를 시작하지 않고, CPU, Memory, HardDrive 기능을 하나씩 키고 연결해야 컴퓨터가 시작된다면 끔찍하지 않을까요??

다시 돌아와서 말하면, Facade 패턴을 이용하면 레거시 코드를 감추어주면서 전체 레거시 시스템을 하나의 큰 단위로 묶어서! 사용할 수 있도록 도와줍니다.

### 장점과 단점

그럼 Facade 패턴의 장단점을 알아보겠습니다.

장점
1. 서브 시스템 간의 결합도를 낮출 수 있습니다.
2. 클라이언트 입장에서 좀 더 간결하게 코드를 알아볼 수 있게 해줍니다.

단점
1. 레거시에 레거시, 레거시에 레거시 등으로 코드가 복잡해 질 수 있습니다.

### 실제 사용 예제

Facade 패턴의 경우 이미 많은 개발자가 사용하지만, 이게 Facade 패턴이었는지 몰랐던 경우가 많지 않았을까? 합니다.

간단하게 Spring 에서 Controller 예제를 하나 들어보겠습니다.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private MapValidationErrorService mapValidationErrorService;

    @Autowired
    private UserService userService;

    @Autowired
    private UserValidator userValidator;

    @PostMapping("/register")
    public ResponseEntity<?> registerUser(@Valid @RequestBody User user, BindingResult result) {

        // Validate passwords match
        userValidator.validate(user, result);

        ResponseEntity<?> errorMap = mapValidationErrorService.MapValidationService(result);
        if(errorMap != null) return errorMap;

        User newUser = userService.saveUser(user);

        return new ResponseEntity<User>(newUser, HttpStatus.CREATED);
    }
}
```
위 코드의 경우 user 를 등록시키는 간단한 코드입니다. 즉, 이는 Client에서 api 요청이 들어왔을 때 여러 다른 로직을 직접 실행시키지 않고 단순히 `registerUser` 라는 함수 를 실행시키게 됩니다.

사실 다들 그냥 사용하고 있었겠지만, 이렇게 User에 관한 큰 서브시스템들을 하나의 function 으로 묶어서 이를 실행하는 패턴이 Facade 패턴인 것입니다.

이렇게 해서 Facade 패턴에 대해서 알아봤습니다. 

이제 디자인 패턴 관련 글도 중간 이상 지난 것 같군요. 조금 더 힘내서 공부해야 겠습니다.