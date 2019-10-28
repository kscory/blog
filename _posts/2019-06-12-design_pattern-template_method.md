---
layout: post
comments: true
title:  "[Design Pattern] Template Method 패턴"
date:   2019-06-12
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/template-method
tags: design pattern
---

__Template Method Pattern__ 이란 상속을 통해서 기능을 확장하는 패턴을 의미합니다.

즉, 상위 클래스(__Abstract 클래스__)에 알고리즘의 로직 흐름을 정의하고, 그 일부를 abstract 메소드(혹은 protected 메소드)를 호출하여 상위 클래스에서 일반화될 수 없는 연산을 수행합니다. 그러면 전체적인 큰 구조를 변경하지 않고 알고리즘의 행위를 변경하지 않아도 됩니다. 또한 __Hook 메서드(일반 메서드)__ 를 함께 제공하여 상속을 통해 기반 클래스에 기능을 삽입할 수 있는 구조를 자주 사용하게 됩니다.

<img src="/assets/design/template-method/template-method-01.png" alt="template-method-01" style="width:40%;">

### 사용하는 이유

Template Method 패턴은 상속 기반의 프레임워크에서 사용됩니다. 대표적으로 JAVA 의 __Spring__ 이 있습니다. (Spring 에서 유명한 Template Method 패턴을 적용한 클래스는  __JdbcTemplate, JmsTemplate, JpaTemplate__ 등 DB 관련 템플릿들이 존재하죠.) 즉, 작업의 대부분을 추상 클래스를 통해 제공하고, 애플리케이션 용도에 맞게 맞춤화할 필요가 있는 부분을 추상 메서드로 남겨서 구현하게 됩니다. 

걀국 프레임 워크에서는 특정 정책을 정의해야 할 때 사용자에게 구현할 것을 요청하게 됩니다. 가장 큰 예로는 __Filter__ 가 존재합니다. 
Spring 에는 __OncePerRequestFilter__ 의 클래스가 존재하는데 이를 자세히 보면 doFilterInternal 메서드(__abstract 메서드__) 을 protected 로 빼고 doFilter (__hook 메서드__) 에서 이를 사용하고 있습니다.

만약 JWT Token 을 받아야 하는 경우라면 이를 상속받아서 값을 체크해줄 수 있을 것입니다.

OncePerRequestFilter (Abstract Class) - Spring 에서 제공

```java
public abstract class OncePerRequestFilter extends GenericFilterBean {

	// ...

    public final void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        if (request instanceof HttpServletRequest && response instanceof HttpServletResponse) {
            HttpServletRequest httpRequest = (HttpServletRequest)request;
            HttpServletResponse httpResponse = (HttpServletResponse)response;
            String alreadyFilteredAttributeName = this.getAlreadyFilteredAttributeName();
            boolean hasAlreadyFilteredAttribute = request.getAttribute(alreadyFilteredAttributeName) != null;
            if (!hasAlreadyFilteredAttribute && !this.skipDispatch(httpRequest) && !this.shouldNotFilter(httpRequest)) {
                request.setAttribute(alreadyFilteredAttributeName, Boolean.TRUE);

                try {
                    this.doFilterInternal(httpRequest, httpResponse, filterChain);
                } finally {
                    request.removeAttribute(alreadyFilteredAttributeName);
                }
            } else {
                filterChain.doFilter(request, response);
            }

        } else {
            throw new ServletException("OncePerRequestFilter just supports HTTP requests");
        }
    }

	// ...

	protected abstract void doFilterInternal(HttpServletRequest var1, HttpServletResponse var2, FilterChain var3) throws ServletException, IOException;
}
```

> JwtAuthenticationFilter (Concrete Class) - 직접 구현

```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private JwtTokenProvider jwtTokenProvider;

    @Autowired
    private CustomUserDetailService customUserDetailService;

    @Override
    protected void doFilterInternal(HttpServletRequest httpServletRequest,
                                    HttpServletResponse httpServletResponse,
                                    FilterChain filterChain) throws ServletException, IOException {

        try {

            String jwt = getJWTFromRequest(httpServletRequest);

            if(StringUtils.hasText(jwt) && jwtTokenProvider.validateToken(jwt)) {
                Long userId = jwtTokenProvider.getUserIdFromJWT(jwt);
                User userDetails = customUserDetailService.loadUserById(userId);

                UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(
                        userDetails, null, Collections.emptyList()
                );

                authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(httpServletRequest));
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }

        } catch (Exception ex) {
            logger.error("Could not set user authentication in security context", ex);
        }

        filterChain.doFilter(httpServletRequest, httpServletResponse);
    }

    private String getJWTFromRequest (HttpServletRequest request) {
        String bearerToken = request.getHeader(HEADER_STRING);

        if(StringUtils.hasText(bearerToken) && bearerToken.startsWith(TOKEN_PREFIX)) {
            return bearerToken.substring(7, bearerToken.length());
        }

        return null;
    }
}
```

### 장점과 단점

그럼 템플릿 메서드 패턴의 장단점을 알아보겠습니다.

장점
1. 전체적인 알고리즘의 뼈대를 유지하되 유연하게 기능을 변경할 수 있다.
2. 중복된 코드를 줄일 수 있다.

단점
1. 사실 대부분의 프레임 워크에서는 템플릿 메서드지만 사용하지 않고 프레임워크에서 기본으로 내장된 것을 쓰는게 낫다.
2. Strategy 패턴은 보다 나은 방법을 제공해 줄 수 있다.

### 실제 사용 예제
위에서 Spring 으로 예시를 들었으니 생략하겠습니다...

이렇게 해서 템플릿 메서드 패턴에 대해서 알아봤습니다. 

참고로 저자의 경우 디자인 패턴을 공부히면서 어떻게 프레임워크들에 적용되고, 내가 어떻게 사용하고 있는가를 이애하면서 배우고 있습니다.

