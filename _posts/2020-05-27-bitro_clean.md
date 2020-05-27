---
layout: post
comments: true
title:  "[프로그래밍 방법론] 비트로 서비스 클린아키텍처 적용하기 (feat nestJs)"
date:   2020-05-27
author: Cory
categories: programming_paradigm
permalink : /dev/programming_paradigm/bitro_clean_architecture
tags: nestjs clean_architecture 
---

![bitro-logo](https://lh3.googleusercontent.com/pw/ACtC-3c17zEIPlUO7no2uAkir59CBKCfXSAVyhDDO2Ie2jABI7Pdk5gDAhhr7HAo5uBnTlZcG0nkQHEb0R8Sq0hBvMOFRvSYOckIuAc2Kr6NCL9AoEWUe1Pr50Mlcv57vQFSdKuVg1zPqkj0LPrhS_snsjcY=w700-h302-no?authuser=0)

요즘 클린아키텍처에 대해 열풍이 불고 있는데요. 제가 속해 있는 겜퍼에서도 [비트로 서비스](https://bitro.kr)를 클린아키텍처를 적용해서 개발하고 있습니다. 그래서 이번 포스팅에서는 비트로 서비스에 적용된 __클린아키텍처__ 에 대해 이야기하고자 합니다.

이 포스팅에서는 클린아키텍처 개념에 대한 이야기를 자세히 하지는 않으니 궁금하신 분들은 [클린아키텍처](https://blog.insightbook.co.kr/2019/08/08/%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98/) 도서를 참고하시면 좋을 것 같습니다.

## Hexogonal 아키텍처

![hexogonal](https://lh3.googleusercontent.com/pw/ACtC-3djhz2B5-8-thizTdeuvG-haSIafN0j6Y2hH4PFoyJSQis-EGKAR8hpQDP3LbiG60gtwGEpf5wyl92Se0E8jbXQqwmOFHlOP9UGsI51ZWbYrIEnrhPPczSklj0BPUBnvHMhujHBm-j_CJWTNuqXgcKa=w700-h621-no?authuser=0)
<div style="text-align: center;">Hexogonal 아키텍처</div><br>

갑자기 클린아키텍처를 이야기한다면서 __Hexogonal 아키텍처__ 가 나와 당황스러우신 분들이 있을 것 같은데요. 이 아키텍처에 대해서 이야기하다보면 클린 아키텍처와 닮은점이 많다는 것을 알게 됩니다. 사실 uncle bob 의 클린아키텍처 블로그 글을 확인하면 가장 처음에 hexogonal 아키텍처의 링크를 참고하게끔 하고 있습니다.

간단하게 이야기하자면 가운데 육각형 안에 비즈니스 로직이 있고 외부 종속적인 UI, API, Persistence 로직 등은 육각형 밖의 레이어에 위치하게 됩니다.

예를 들면 회원가입을 수행하는 로직은 내부 도메인으로 들어가게 되고, 회원가입을 요청하는 UI 로직은 외부에 두며, 요청한 회원을 DB 에 저장하는 로직 또한 외부에 존재시켜 레이어를 분리하게 됩니다.

또한 이 Hexogonal 아키텍처는 MSA 설계와도 매우 밀접하게 관련되어 있는데요.

![hexogonal2](https://lh3.googleusercontent.com/pw/ACtC-3d5kav2BUiS6XjfTAQvb4ZAluH8DapckSnIgAjnbwAAwWj_K0NnyQfaa-ehBwD6Lkyes5ZZgnWkNGfZHVFEHaQtgtUFAEcZMFVRm-GMCpKHigwFRUFhLaFOoG3elCsL_yX-RxtHcYMaMpC9mujbv51D=w700-h524-no?authuser=0)
<div style="text-align: center;">hexogonal architecture for msa</div><br>

위 그림에서 보듯이 각각의 서비스가 adapter 를 이용해서 상호작용하는 것을 확인할 수 있습니다.

## 클린 아키텍처

![clean-architecture](https://lh3.googleusercontent.com/pw/ACtC-3fl4P4d6QUxqk4iNMeEg9ZTgx6R7Md2ZgOP22jmyqM8l2c_OrHswqCbzSHKsycpuC2Dh-o14HJKlmvJMjJmtA7jIyF1_jEv7lcuvo_UIm0Vycem4KYcOx0aK6rIOzkZwHuFTHSf5Aar78Qh0yc4Z-9N=w700-h514-no?authuser=0)
<div style="text-align: center;">출처: https://reflectoring.io/spring-hexagonal</div><br>

이제는 너무나 유명해진 클린아키텍처 그림 입니다. 개인적으로 클린아키텍처로 설계할 때 중요한 점은 의존성 규칙은 외부에서 내부로 흘러야만 하고, 내부 로직은 프레임워크에 독립적이어야 한다는 점입니다.

그래서 저희는 의존성 규칙을 지키기 위해 DIP 를 적용했습니다. 그림에서도 나와 있지만 port 를 이용해서 의존성을 역전시킵니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3eekbrOPUlwVuK4leM7Xfo0YUck0C7mrhq7k3uvBWVPh8uvzq1APGh8-MzvgoxW3w5-WYyglpYyX0FWw_ev8mh7uO721ag_Xs7imAkLRbToJplS9_NnLsG76oTeXXHE-TmjsTXzBp6dqs3gfrRzNrnY=w700-h114-no?authuser=0)
<div style="text-align: center;">의존성 규칙이 위반된 형식</div>

![](https://lh3.googleusercontent.com/pw/ACtC-3cwajumn6_mTcJeq6A4cJjl1JidN8BAIOCc7amxyPkjFuPOuMTgtahlpWMs7IyRQ5pvkvqUle9D5yAehf-OtngucyWBLfLHqEnPXNbLSc57SNjr8WoTq0fOwaLPkW8gMod1yUv4cZx8ZW5K08NvFwRy=w700-h122-no?authuser=0)
<div style="text-align: center;">port 를 활용한 DIP</div><br>

물론 클린아키텍처에 의하면 UseCase 조차도 Interface 로 추상화 시켜야 한다고 하지만 저희 내부에서는 너무 심한 추상화는 오히려 개발할때 힘들 수 있으며 개발 속도도 늦어질 수 있다는 판단하에 이 부분에 대한 추상화 작업은 나중에 진행하기로 결정했습니다.

## 비트로 클린아키텍처

저희는 [Tom Hombergs](https://reflectoring.io/spring-hexagonal/) 의 블로그 글에서 hexogonal 아키텍처를 이용해 클린 아키텍처에 접근한 방법에 공감하여 비슷한 아키텍처로 구성하게 되었습니다.

![tom-hombergs-architecture](https://lh3.googleusercontent.com/pw/ACtC-3dmV_2_5By_zwuTta-md8P3Yf58b6HKpdDIXeTgPVyfApZI3U2AzkjWKvxgBKTCHU10gsX30LnBtlChf8aQrcGSe8ImJROoYGGkwxUogLL6bRck4y53czF2D1CjoMXmjwC3Mud4gfbStxhuzFEe6b1L=w700-h344-no?authuser=0)
<div style="text-align: center;">출처: https://reflectoring.io/spring-hexagonal</div>

위의 그림에서 보듯이 Entity 와 UseCase 를 Hexogonal 의 내부로 생각하고 Controller 와 ORM Repository 는 Port 를 이용해서 접근하게 됩니다. 여기서육각형 안에 있는 부분은 프레임워크에 독립적일 수 있도록 만들었습니다.

그리고 프레임워크에 종속적인 부분들은 Port 와 Adapter 를 이용해서 내부 도메인 로직과 소통하게 됩니다.

## 프로젝트 구조는?

비트로에서 실 서비스에 사용하고 있는 프로젝트 구조입니다.

![bitro-project](https://lh3.googleusercontent.com/pw/ACtC-3dbQHkEwwSj3XWL_CenWSz8C7_brVHQD9Knb_pwwibPZWPLI3-owotPKsZya81-UjmmXZzVSP86_WIDVXT_84jJ3udzMkzi4Eb5PhSYpxXMbmKAAJ6KkyN__X-CUlNuzJSjUGZXYlyuCYtsaQ8u6EUD=w678-h1076-no?authuser=0)

비트로 서비스는 기본적으로 Typescript 기반의 NestJS 를 사용하고 있으며 각각의 domain 별로 Module 로 묶어서 사용하고 있습니다. 이 모듈로 나뉜 부분이 하나의 육각형을 이루고 있다고 보시면 됩니다. 위의 그림의 경우 AssetModule 로 하나의 육각형 아키텍처를 이루고 있습니다.

`(※JS, TS 에서는 패키지란 말보다 모듈이란 말을 주로 쓰는데 앞에서 이야기한 AssetModule 과 혼동 될 수 있어 패키지 라는 명명을 하도록 하겠습니다.)`

이 프로젝트 구조에서는 육각형 내부에 존재하는 __domain 패키지__, 외부에서 흘러 들어오는 부분을 처리하는 __inbound 패키지__, 내부에서 외부로 흘러 나가는 __outbound 패키지__ 로 구성되어 있습니다.

하지만 이 중에서 __saga__ 와 __scheduller__ 의 경우 inbound 혹은 outbound 에 포함시키기에는 애매한 부분이 있어서 따로 패키지로 구성했습니다.

그럼 패키지별로 비트로에서는 어떤 방식으로 클린아키텍처를 적용했는지 알아보도록 하겠습니다.

## domain 패키지

domain 패키지 도메인 로직을 담고 있는 Entity , DIP 를 위한 Port , Entity 와 Port 를 연결해주는 UseCase 클래스들을 포함하고 있습니다.

### Entity

Entity 는 아래와 같은 도메인 로직을 실행합니다. 예를 들어 비트로 서비스에서는 비트코인 및 상품권 가격을 계산하고 검증하는 로직을 수행하고 있습니다.

```typescript
export class WithdrawalBTC {

    readonly btcPrice: number;
    readonly btcFee: number;
    readonly amount: number;
    readonly address: string;

    get btcAmount {
        return this.amount / this.btcPrice - this.btcFee;
    }

    public validateBTCExchange() {
        if (this.btcAmount < 0) {
            throw new InValidBTCAmountException();
        }
    }
}
```

### Port

port 의 경우 단순히 adapter 와 이어주기 위해서 추상화시킨 계층입니다. 예를 들어 위에서 출금이 필요하다면 외부 bitcoin-core 서비스에 비트코인 출금을 요청하는 프로세스가 필요할 것 같습니다.

```typescript
export interface SendAssetPort {
    sendBTC(amount: number, addr: string): Promise<undefined>;
}

// 이 상수의 경우 port 에 대한 naming 을 위해 사용됩니다.
export const SEND_ASSET_PORT = "SEND_ASSET_PORT"
```


### UseCase

UseCase 의 경우 위에서 작성한 Entity 와 Port 를 연결하는 역할을 담당하게 됩니다. 즉, entity 의 검증 로직을 사용하고 이 검증이 통과하면 port를 활용해서 비트코인을 출금하게 됩니다.

```typescript
export class WithdrawAssetUseCase {
    
    constructor(
        @Inject(SEND_ASSET_PORT) private readonly sendAssetPort: SendAssetPort,
    ){}

    async withdrawBTC(withdrawalBTC: WithdrawalBTC): Promise<undefined> {

        // 1. 출금을 할 수 있는지 검증
        withdrawalBTC.validateBTCExchange();
  
        // 2. 출금 요청
        await this.sendAssetPort
          .sendBTC(withdrawalBTC.btcAmount, withdrawalBTC.address);
    }
}
```

## inBound 패키지

inbound 패키지의 경우 Controller 의 집합인 api 패키지, listener 들의 집합체인 consumer 패키지로 구성되어 있습니다. 이 때 데이터를 dto 를 사용해서 처리하고 있습니다.

이 inbound 패키지에서는 UseCase 를 직접 사용해서 도메인 로직을 사용하게 됩니다.

### api

api 의 경우 Controller의 집합으로 Web Adapter 를 의미합니다. 즉 UseCase 를 Inject 받아서 처리하게 됩니다. 만약 외부에서 출금 요청이 들어온다면 위에서 작성한 WithdrawalUseCase 를 활용해 외부의 요청에 응답해줍니다.

```typescript
@Controller('assets')
export class AssetController {

    constructor(
        private readonly withdrawAssetUseCase: WithdrawAssetUseCase,
    ){}

    @Post('btc/withdrawal')
    async requestWithdrawal(
        @Param('asset') assetName: string,
        @Body() request: WithdrawalBTCRequest
    ) {

        // 1. dto 검증
        if (withdrawalBTCRequest.amount < 0) {
            throw new ParameterException("amount must be positive");
        }

        // 2. UseCase 를 활용해서 출금 요청
        await this.withdrawAssetUseCase
          .withdrawBTC(WithdrawalBTCRequest.toEntity(request));

        // 3. 요청에 대해 응답
        return new OKResponse();
    
    }
}
```

여기서 위의 dto 검증의 경우 class-validator 를 이용하면 더 보기 좋게 줄일 수 있지만 말하고자 하는 범위를 넘어가므로 제외하겠습니다.

### consumer

여기서 withdraw 의 경우 비동기로 이루어질 수도 있습니다. 만약 출금 로직을 consumer 로 바꾼다면 받아들이는 방법이 달라질 뿐 UseCase 를 활용하는 사실은 변함 없습니다.

즉, api (web) 을 통해서 들어오던 queue 에서 들어오던 상관 없이 같은 로직을 활용할 수 있습니다.

예시는 RabbitMQ 를 사용한다고 가정해보겠습니다.

```typescript
@Controller()
export class AssetConsumer {
    constructor(
        private readonly withdrawAssetUseCase: WithdrawAssetUseCase,
    ){}

    @MessagePattern('withdraw')
    async consumeWithdrawal(
        @Payload() event: WithdrawalEvent,
        @Ctx() context: RmqContext,
    ) {

        // 1. connection 정보 가져오기
        const channel = context.getChannelRef();
        const message = context.getMesssage();

        // 2. UseCase 를 활용해서 출금 요청
        await this.withdrawAssetUseCase
          .withdrawBTC(WithdrawalEvent.toEntity(event));

        // 3. 완료시 ack 응답
        channel.ack(message);
   }
}
```

### dto

여기서 dto 의 경우 event 와 request 모델들이 주로 정의됩니다. 그리고 이 패키지 내에서 Mapping 함수를 사용하여 entity 모델로 바꿔주는 역할을 진행하게 됩니다.

다만 nestjs 에서는 json 객체를 reflect 를 이용해서 dto 클래스로 변환시켜 줍니다. 따라서 json 에는 함수가 없기 때문에 일반 함수로 작성한다면 “function is not defiend” 에러가 발생하게 됩니다. 따라서 저희는 Mapping 함수를 static 함수로 정의했습니다.

```typescript
export class WithdrawalBTCRequest {

    // 변수 정의
    static toEntity(request: WithdrawalBTCRequest) {
        return new WithdrawalBTC(/* 변수 */)
    }
}

export class WithdrawalEvent {

    // 변수 정의
    static toEntity(event: WithdrawalEvent) {
        return new WithdrawalBTC(/* 변수 */)
    }
}
```

## outBound 패키지

outbound 패키지의 경우 외부 api 를 호출하는 api 패키지, event 를 만들어 내는 producer, 그리고, DB 에 접근해서 정보를 저장하는 persistence 로 구성되어 있습니다.

여기서 DDD 에서는 Repository 를 하나만 쓰라고 권장하고 있습니다. 하지만 현실적으로 그렇게 개발하기는 시간이 부족할 것 같아 저희는 퍼사드 패턴을 이용해 여러 repository 를 하나의 adapter 로 정의했습니다. 즉, domain 패키지에서 정의한 port 는 adapter 가 상속을 받고 repository 를 이용해서 db 로직을 수행하게 됩니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3fJggsV6jpBA7xqYfpV__nBPfp9zEL2TmJfQcJAzoxS7ZCVaQyq7GnZwvRiZO7wRlEKDvUmmsTAAPV3U2FcDEVcyXwCGSZXea0zOA_PK7jTotOoQgCdEG3KHhyZJTRn-MSaII9znxmN53tILJ14JkWh=w700-h252-no?authuser=0)
<div style="text-align: center;">Persistence Adapter 퍼사드</div><br>

Api 와 Producer 또한 퍼사드 패턴을 활용하고 있습니다. 특히 api 의 경우 같은 프로젝트 내의 다른 모듈(ex> MemeberModule)에게 요청을 해야 할 때 따로 Api 를 만들 필요 없이 효과적으로 Adapter 만 이용해서 가져올 수 있는 장점을 가지고 있습니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3eE_vWT6Hio13-pbSOeaGBqsnPmOvfiFentWmXdv0IPdV6tPbXJuAmUfVqIiz6a_UOkrwBpRDd9aeVV1x8mDsjSnHfgrloyQLwSgf0iRHxXm3EOy0CzwfV67wEla3O3Asbc0Z1Yztz0l7Cy-o8gh7mE=w700-h253-no?authuser=0)
<div style="text-align: center;">Api Adapter 퍼사드</div><br>

### api

api 패키지의 경우 말 그대로 외부의 api 를 호출하는 역할을 합니다.

만약 비트코인을 전송하는 SendAssetPort 의 경우 아래와 같이 adapter 에서 상속을 받고 bitcoinApi 에서 실제 api 를 call 하는 로직을 수행합니다.

```typescript
@Injectable()
export class ApiAdapter implements SendAssetPort {

    constructor(
        private readonly bitcoinApi: BitcoinApi,
    ){}

    async sendBTC(amount: number, addr: string): Promise<undefined> {

        // 1. request dto 변환
        const request = new SendBitcoinRequest(amount, address);
    
        // 2. 비트코인을 전송
        await this.bitcoinApi.send(request);
    }
}

@Injectable()
export class BitcoinApi {

    async send(request: SendBitcoinRequest): Promise<undefined> {  

        try {
            await axios.post(`${bitcoin-url}`, request);
        } catch (error) {
            throw new BitcoinApiFailedException()
        }
    }
}
```

### producer

producer 의 경우 nestjs 의 client Proxy 를 이용해서 전송하고 있습니다. 만약 비트코인을 보내는 로직을 producer 로직으로 바꿀 경우 아래와 같은 형식으로 사용할 수 있습니다.

```typescript
@Injectable()
export class ProducerAdapter implements SendAssetPort {

    constructor(
        private readonly bitcoinProducer: BitcoinProducer,
    ){}

    async sendBTC(amount: number, addr: string): Promise<undefined> {

        // 1. event dto 변환
        const event = new SendBitcoinEvent(amount, address);
    
        // 2. 비트코인을 전송
        await this.bitcoinProducer.send(event);
    }
}

@Injectable()
export class BitcoinProducer {
    
    constructor(
        @Inject('bitcoin-mq') private readonly client: ClientProxy,
    ){}

    async send(event: SendBitcoinEvent): Promise<undefined> {

        try {
            await this.client.send('btc-send', event).toPromise();
        } catch (error) {
            throw new BitcoinProducerFailedException()
        }
    }
}
```

여기서 보면 알겠지만 만약 저희가 API 형식에서 EVENT 형식으로 바꾸고 싶다면 단순히 port 상속을 바꿔주면 됩니다. 즉, domain 로직은 변하지 않고 외부의 Adapter 만 바꾸면 됩니다.

또한 ApiAdapter 와 ProducerAdapter 를 ExternalServiceAdapter 와 같은 식으로 하나의 Adapter 로 활용한다면 상속도 바꿀 필요 없이 의존성 주입할 클래스를 “BitcoinApi” 에서 “BitcoinProducer” 로 변경해서 사용해주면 됩니다.

### persistence

persistence 패키지의 경우 주로 db 영속화를 담당하게 됩니다. 이 adapter 에서는 repository 를 이용해서 정보를 저장하게 됩니다.

그런데 여기서 코드에는 persistence model 로 전환하는 예시를 들고 있지만 내부적으로는 entity 를 database model 로 사용하고 있습니다.

```typescript
@Injectable()
export class PersistenceAdapter implements CreateWithdrawalPort {

    constructor(
        private readonly withdrawalRepository: WithdrawalRepository,
    ){}

    async saveBTC(withdrawalBTC: WithdrawalBTC): Promise<WithdrawalBTC> {

        // 1. persistence model 변환
        const withdrawal = Withdrawal.fromEntity(withdrawalBTC);
    
        // 2. 저장
        await this.withdrawalRepository.saveWithdrawal(withdrawal);
        // 3. 결과 리턴
        return withdrawalBTC;
    }
}

@EntityRepository(Withdrawal)
export class WithdrawalRepository extends Repository<Withdrawal> {

    async saveWithdrawal(withdrawal: Withdrawal): Promise<withdrawal> {
        const saved = await this.save(withdrawal)
        return saved;
    }
}
```

## 마무리하며

비트로 서비스는 위와 같은 방법으로 클린아키텍처를 적용해서 개발하고 있습니다. 사실 대부분의 클린 아키텍처 레퍼런스가 Spring Framework 기반으로 되어 있어서 적용할 때 조금 어려움을 겪었던 것도 사실입니다. 하지만 어느정도 개발 방법을 알고 나니 아키텍처도 깔끔하고 새로운 기능도 빠르게 추가할 수 있었습니다.

여기에 더 추가하고 싶은 의견이 있다면 댓글 달아주시면 감사하겠습니다
