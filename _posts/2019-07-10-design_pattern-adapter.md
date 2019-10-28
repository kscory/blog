---
layout: post
comments: true
title:  "[Design Pattern] Adapter 패턴"
date:   2019-07-10
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/adapter
tags: design pattern
---

__Adapter Pattern__ 이란 클래스의 인터페이스를 사용자가 기대하는 다른 인터페이스로 변환하는 패턴으로, 호환성이 없는 인터페이스 때문에 함께 동작할 수 없는 클래스들이 함께 작동하도록 해줍니다.  (by [wikipedia](https://ko.wikipedia.org/wiki/%EC%96%B4%EB%8C%91%ED%84%B0_%ED%8C%A8%ED%84%B4))

즉, 이미 구현되어 있는 __Adaptee__ 객체가 있고, 현재 실행 되길 원하는 __Target__ 객체가 존재할 때, __Adapter__ 는 Target 인터페이스를 구현하여 Adaptee 가 이해할 수 있는 방법으로 Adaptee 에게 요청을 보냅니다.

다만 여기서 __클래스 Adapter__ 와 __객체 Adapter__ 두가지가 존재하게 되는데, 클래스 Adapter 는 상속을 사용하고, 객체 Adapter 는 합성을 사용하게 됩니다.

<img src="/assets/design/adapter/adapter-01.jpg" alt="adapter-01">

### 사용하는 이유

먼저, 기존 라이브러리가 더 이상 실제 서비스와 맞지 않아 다시 만들어야 하는데, 이 라이브러리를 다른 부서에서도 사용하고 있다고 생각해 봅시다. 그럼 이 라이브러리를 고치게 되면, 다른 부서에 영향을 끼치게 될 것입니다. 그럴 경우 Adapter 패턴을 이용하면 기존 라이브러리를 건들지 않으면서 새로운 기능을 추가시킬 수 있을 것입니다.

또한 만약 정책이 자주 바뀌는 경우, 계속해서 라이브러리를 바꿔야 할까요? 즉, 기존의 것을 최대한 건들지 않고 구현할 수 있어야 합니다.

실제 구현되어 있는 예를 한가지 보겠습니다. __Spring Security__ 에서 많이 사용되는 `WebSecurityConfigurerAdapter` 에 대해서 간단히 설명 후 왜 쓰는지에 대해 설명하도록 하겠습니다.

이 클래스는 Spring 에서 웹 보안을 설정하는데, 즉, WebSecurityConfigurerAdapter 내에 `ContentNegotiationStrategy`, `AuthenticationConfiguration`, 등의 __Adaptee__ 가 설정되어 있고, `WebSecurityConfigurer` 라는 __Target interface__ 를 상속받고 있습니다. 이를 Client 에서 사용하게 됩니다. 

만약 실제로 쓰고 싶다면 아래와 같이 설정하고 configure 로직을 원하는 대로 수정하면 됩니다.

코드를 보면서 직접 알아보도록 하겠습니다.

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
         http
            .authorizeRequests()
                .antMatchers("/signup").permitAll()
                .anyRequest() // 설정한 요청 이외의 request 요청을 표현
                    .authenticated().and() // 해당 요청을 인증된 사용자만 사용 가능
            .headers()
                .frameOptions()
                .sameOrigin().and()
            .cors().and()
            .csrf().disable()
            .exceptionHandling()
                .authenticationEntryPoint(unauthorizedHandler).and()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
    }
}
```

가 있을 때 client 에서는 configure 메서드를 바라보면서 계속 새로운 로직을 쓰게 됩니다.

만약 내가 여기다가 JWT 를 추가하고 싶다? 한다면 client 혹은 다른 구현체를 바꾸는 것이 아닌, Adapter 의 configure 메서드만 조금 바꿔주면 될 것입니다. 아래처럼 말입니다.

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
         http
            .authorizeRequests()
                .antMatchers(SecurityConstants.SIGN_UP_URLS).permitAll()
                .anyRequest() // 설정한 요청 이외의 request 요청을 표현
                    .authenticated().and() // 해당 요청을 인증된 사용자만 사용 가능
            .headers()
                .frameOptions()
                .sameOrigin().and()
            .cors().and()
            .csrf().disable()
            .exceptionHandling()
                .authenticationEntryPoint(unauthorizedHandler).and()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .addFilterBefore(jwtAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
    }
}
```

### 장점과 단점

그럼 Adapter 패턴의 장단점을 알아보겠습니다.

장점
1. Adapter 패턴의 가장 큰 장점을 기존 코드를 변경하지 않아도 된다는 점입니다.
2. 기존 코드를 변경하지 않기 때문에 클래스 재활용성을 증가시킬 수 있습니다.

단점
1. 구성요소를 위해 클래스를 증가시켜야 하기 때문에 복잡도가 증가할 수 있습니다.
2. 클래스 Adapter 의 경우 상속을 사용하기 때문에 유연하지 못합니다.
3. 반명 객체 Adapter 의 경우는 대부분의 코드를 다시 작성해야 하기 때문에 효율적이지 못합니다.

이렇게 해서 Adapter 패턴에 대해서 알아봤습니다. 

예전에 안드로이드 프로그래밍을 공부했을 때, 가장 많이 사용한 클래스가 Adapter 클래스였습니다. 그 때에는 그냥 연결하면 뷰와 데이터가 연결된다. 라고만 생각하고 넘어갔었는데, 왜 Adapter 로 적용되었는지 생각해보니 재밌기도 하면서 신기하기도 합니다.