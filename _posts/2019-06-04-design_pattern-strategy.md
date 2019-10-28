---
layout: post
comments: true
title:  "[Design Pattern] Strategy 패턴"
date:   2019-06-04
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/strategy
tags: design pattern
---

__Strategy Pattern__ 이란 말 그대로 전략을 쉽게 바꿀 수 있도록 해주는 디자인 패턴입니다.

스트래티지 패턴(Strategy pattern)에서는 알고리즘군을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있도록 만들게 해줍니다. 즉, 스트래티지를 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있게 됩니다.

다시 말하면 어떤 알고리즘을 위한 전략을 정의하는 인터페이스를 정의(__Strategy__)한 후, 상호 교환 가능한 클래스군이 인터페이스를 구현(__Concrete Strategy__)하며, 알고리즘을 사용하는 클래스(__Context__) 를 구현한다. 여기서 Context 는 필요에 따라 전략을 바꿀수 있도록 setter 메서드를 제공해야 합니다..

<img src="/assets/design/strategy/design-strategy-01.jpg" title="strategy01">

### 사용하는 이유

클래스간의 차이가 단순히 어떤 공동의 연산을 수행하는 데 사용하는 전략뿐인 경우가 있습니다. 예를 들면 '사자' 라는 클래스와 '호랑이', '독수리' 라는 클래스가 있다고 할때 둘의 차이는 '울음소리', '날아가기' 라고 생각할 수 있을 것입니다. 

만약 이를 상속을 통해서 해결하게 상속 트리의 폭주라는 문제를 일으킬 수 있습니다. 즉, 계속해서 다른 특징마다 상속을 이어나가게 되면 상속에 상속에 상속을 꼬리를 물고 계속 나타나게 되는 문제가 발생하며, 중복 코드가 발생하는 문제가 생깁니다.

또한 만약 '타조' 라는 객체가 추가되었다고 할 때 이는 날 수 없으므로 기존 Bird 클래스를 다시 건드려야 하는 문제 또한 생기게 됩니다.

<img src="/assets/design/strategy/design-strategy-02.png" title="strategy02">

이런 경우 이럴 때 Strategy 패턴을 이용하게 되면, 'cry' 라는 인터페이스(__Strategy__) 를 정의하고 LionCry, TigerCry, EagerCry(__Concrete Strategy 1__), FlyNoway, FlyWithWings(__Concrete Strategy 2__) 를 구현하고, 이를 Lion, Tiger, Eager 클래스(__Context__) 에서 사용하게 됩니다.

이러면 달라지는 부분을 찾아서 나머지 코드에 영향을 주지 않도록 "캡술화"를 시킴으로써 중복 코드를 삭제할 수 있으며, 오버라이딩 대신 인터페이스를 사용하여 단순해질 수 있습니다.

<img src="/assets/design/strategy/design-strategy-03.png" title="strategy03">

### 장점과 단점

그럼 Strategy 패턴의 장단점을 알아보겠습니다.

장점
1. Strategy 는 파생 클래싱의 좋은 대안입니다. 클래스를 상속하고 메소드를 오버라이딩 하는 대신 단순한 인터페이스를 구현만 하면 되기 때문입니다. <br>
2. Strategy 객체는 Context 클래스를 필요로 하지 않으며, 알고리즘 특정 데이터에 집중할 수 있습니다. <br>
3. 시스템에 새로운 Strategy 를 추가하기 쉽습니다. 위에서 예를 들었듯이 다른 기능을 추가하기 쉽습니다. 또한 이 때, 기존 클래스들을 재컴파일하지 않아도 됩니다. <br>

단점
1. 통신 오버헤드가 클 수 있습니다. 즉, Strategy 객체에 전달된 인자의 일부가 사용되지 않을 수도 있습니다.

### 실제 사용 예제

Spring framework 에서 oauth2 를 이용하여 google, facebook, 등 로그인을 사용하는 예제를 간단하게 알아보겠습니다.

> spring security oauth2 에 내장되어 있는 CommonOAuth2Provider

```java
/**
 * Common OAuth2 Providers that can be used to create
 * {@link org.springframework.security.oauth2.client.registration.ClientRegistration.Builder
 * builders} pre-configured with sensible defaults.
 *
 * @author Phillip Webb
 * @since 5.0
 */
public enum CommonOAuth2Provider {

	GOOGLE {
		@Override
		public Builder getBuilder(String registrationId) {
			ClientRegistration.Builder builder = getBuilder(registrationId,
					ClientAuthenticationMethod.BASIC, DEFAULT_LOGIN_REDIRECT_URL);
			builder.scope("openid", "profile", "email");
			builder.authorizationUri("https://accounts.google.com/o/oauth2/v2/auth");
			builder.tokenUri("https://www.googleapis.com/oauth2/v4/token");
			builder.jwkSetUri("https://www.googleapis.com/oauth2/v3/certs");
			builder.userInfoUri("https://www.googleapis.com/oauth2/v3/userinfo");
			builder.userNameAttributeName(IdTokenClaimNames.SUB);
			builder.clientName("Google");
			return builder;
		}
	},

	GITHUB {

		@Override
		public Builder getBuilder(String registrationId) {
			ClientRegistration.Builder builder = getBuilder(registrationId,
					ClientAuthenticationMethod.BASIC, DEFAULT_LOGIN_REDIRECT_URL);
			builder.scope("read:user");
			builder.authorizationUri("https://github.com/login/oauth/authorize");
			builder.tokenUri("https://github.com/login/oauth/access_token");
			builder.userInfoUri("https://api.github.com/user");
			builder.userNameAttributeName("id");
			builder.clientName("GitHub");
			return builder;
		}
	},

	FACEBOOK {
		@Override
		public Builder getBuilder(String registrationId) {
			ClientRegistration.Builder builder = getBuilder(registrationId,
					ClientAuthenticationMethod.POST, DEFAULT_LOGIN_REDIRECT_URL);
			builder.scope("public_profile", "email");
			builder.authorizationUri("https://www.facebook.com/v2.8/dialog/oauth");
			builder.tokenUri("https://graph.facebook.com/v2.8/oauth/access_token");
			builder.userInfoUri("https://graph.facebook.com/me");
			builder.userNameAttributeName("id");
			builder.clientName("Facebook");
			return builder;
		}
	};

    // ...

	private static final String DEFAULT_LOGIN_REDIRECT_URL = "{baseUrl}/login/oauth2/code/{registrationId}";

	protected final ClientRegistration.Builder getBuilder(String registrationId,
															ClientAuthenticationMethod method, String redirectUri) {
		ClientRegistration.Builder builder = ClientRegistration.withRegistrationId(registrationId);
		builder.clientAuthenticationMethod(method);
		builder.authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE);
		builder.redirectUriTemplate(redirectUri);
		return builder;
	}

	/**
	 * Create a new
	 * {@link org.springframework.security.oauth2.client.registration.ClientRegistration.Builder
	 * ClientRegistration.Builder} pre-configured with provider defaults.
	 * @param registrationId the registration-id used with the new builder
	 * @return a builder instance
	 */
	public abstract ClientRegistration.Builder getBuilder(String registrationId);

}
```

> 전략을 사용하고 있는 Registration 및 GoogleRegistration, FacebookRegistration 구현

```java
// 
abstract class OAuth2Registration {
    ClientRegistration.Builder builder;

    public abstract ClientRegistration getOAuth2Registration();

    public void setBuilder(ClientRegistration.Builder builder) {
        this.builder = builder;
    }
}

public class GoogleRegistration extends OAuth2Registration {

    @Value("${oauth2.google.client-id}")
    private String clientId;

    @Value("${oauth2.google.client-secret}")
    private String clientSecret;

    GoogleRegistration() {
        builder =  CommonOAuth2Provider.GOOGLE.getBuilder("google");
    }

    @Override
    public ClientRegistration getOAuth2Registration() {
        return builder
                .clientId(clientId)
                .clientSecret(clientSecret)
                .scope("email", "profile")
                .build();
    }
}

public class FacebookRegistration extends OAuth2Registration {

    @Value("${oauth2.facebook.client-id}")
    private String clientId;

    @Value("${oauth2.facebook.client-secret}")
    private String clientSecret;

    FacebookRegistration() {
        builder =  CommonOAuth2Provider.FACEBOOK.getBuilder("facebook");
    }

    @Override
    public ClientRegistration getOAuth2Registration() {
        return builder
                .clientId(clientId)
                .clientSecret(clientSecret)
                .userInfoUri("https://graph.facebook.com/me?fields=id,name,email,link") // 그래프API 의 경우 scope 로 안되기 때문에 직접 파라미터를 넣는다.
                .scope("email")
                .build();
    }
}
```

위를 보면 Google, Github, Facebook 로그인을 사용하기 위한 Builder 를 각각 다르게 정의해주고 있으며, registration 에서 이를 사용하고 있습니다. 그런데 특이한 점은 Enum 을 사용했다는 점인데 이와 같이 Enum 을 사용하면 클래스의 갯수를 조금 더 줄이면서 여 Strategy 패턴을 구현할 수 있습니다. 

즉, CommonOAuth2Provider 란 인터페이스(__Strategy__)가 정의되고, 각각의 GOOGLE, GITHUB, FACEBOOK 에서 알고리즘을 구현(__Concrete Strategy__) 했으며 이를 사용하는 각각 ClientRegistration 를 만들어(__Context__) 사용하게 됩니다. 여기서 ClientRegistration는 ClientRegistrationRepository 에 등록하여 사용할 수 있습니다.

만약 여기서 내가 KAKAO 로그인을 추가시키고 싶다면 아래처럼 Enum 에 KAKAO 관련 코드를 추가시켜주면 됩니다.

```java
KAKAO {
    @Override
    public ClientRegistration.Builder getBuilder(String registrationId) {
        ClientRegistration.Builder builder = getBuilder(registrationId,
        ClientAuthenticationMethod.POST, DEFAULT_LOGIN_REDIRECT_URL);

        builder.scope("profile");
        builder.authorizationUri("https://kauth.kakao.com/oauth/authorize");
        builder.tokenUri("https://kauth.kakao.com/oauth/token");
        builder.userInfoUri("https://kapi.kakao.com/v1/user/me");
        builder.userNameAttributeName("id");
        builder.clientName("Kakao");

        return builder;

    }
},
```

이렇게 전략 패턴에 대해서 알아봤습니다.

요즘 Spring boot 를 활용하여 개발하고 있는데 디자인 패턴에 대한 내용을 정리하는 김에 특별한 경우가 아니라면 spring framework 에 적용되어 있는 디자인 패턴을 예시로 들어 계속해서 사용해볼까 합니다. 