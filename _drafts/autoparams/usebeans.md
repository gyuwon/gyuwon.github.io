---
layout: post
title:  @UseBeans 애노테이션을 사용한 AutoParams와 Spring 테스트 통합
date:   2025-04-06 15:00:00 +0900
categories: [blog, AutoParams]
permalink: /autoparams/usebeans/
---

AutoParams의 핵심 모듈은 Spring에 전혀 의존하지 않는다. 이 점은 AutoParams를 다양한 환경에서 가볍게 사용할 수 있다는 장점이 되지만, Spring 기반 테스트에서는 조금 불편함을 유발하기도 한다.

예를 들어, 테스트 메서드에서 AutoParams가 자동으로 생성해주는 테스트 데이터와 함께 Spring 빈을 매개변수로 받고 싶을 때가 있다. 하지만 별도의 조치를 하지 않으면 AutoParams는 Spring 빈을 직접 생성하려고 시도하고, 이 과정에서 오류가 발생하게 된다.

이 글은 이 문제를 해결하는 기존의 방식과 [**10.0.0 버전 릴리스**](https://github.com/AutoParams/AutoParams/releases/tag/10.0.0)에 추가된 **`@UseBeans`** 애노테이션을 사용하는 방식을 비교 설명한다.

<!--more-->

### @BrakeBeforeAnnotation 애노테이션 사용

먼저 아래와 같은 인터페이스와 이를 구현한 Spring 빈이 있다고 가정해보자.

```java
public interface MessageSupplier {

    String getMessage(String name);
}
```

```java
@Component
public class HelloSupplier implements MessageSupplier {

    @Override
    public String getMessage(String name) {
        return "Hello, " + name + "!";
    }
}
```

이 Spring 빈을 AutoParams를 사용한 테스트에서 사용하기 위해 다음 예시처럼 `@BrakeBeforeAnnotation` 애노테이션을 사용할 수 있다.

```java
@SpringBootTest
public class TestClass {

    @ParameterizedTest
    @AutoSource
    @BrakeBeforeAnnotation(Autowired.class)
    void testMethod(String name, @Autowired MessageSupplier service) {
        String message = service.getMessage(name);
        assertTrue(message.startsWith("Hello"));
        assertTrue(message.contains(name));
    }
}
```

이 테스트는 다음과 같은 조건을 만족해야만 제대로 동작한다:

- `@ParameterizedTest` 메서드에 `@AutoSource`를 함께 사용해서 AutoParams가 테스트 매개변수 인자를 제공하게 한다.

- Spring 빈 매개변수에는 반드시 `@Autowired` 애노테이션을 붙여서 Spring 테스트에 주입을 요청한다.

- Spring 빈 매개변수는 AutoParams가 자동으로 생성하는 다른 테스트 데이터 매개변수보다 뒤에 위치해야 한다.

- `@BrakeBeforeAnnotation(Autowired.class)`를 통해 AutoParams가 순차적으로 매개변수 인자를 생성하다가 `@Autowired` 애노테이션을 만나면 인자 생성을 멈추도록 알려야 한다.

이런 설정은 그리 복잡하지는 않지만 그중에 일부는 테스트의 본질과는 거리가 있고 AutoParams와 Spring 테스트가 충돌하지 않도록 하기 위한 방어 코드에 가깝다.

### @UseBeans 애노테이션 사용

이런 점을 개선하기 위해 10.0.0 버전에서는 [**autoparams-spring 확장**](https://mvnrepository.com/artifact/io.github.autoparams/autoparams-spring)이 새롭게 추가되었다. AutoParams의 핵심 모듈은 여전히 Spring에 의존하지 않지만, 이 확장은 AutoParams를 Spring 테스트와 더 편리하게 사용할 수 있도록 지원한다.

autoparams-spring 확장이 제공하는 `@UseBeans` 애노테이션을 사용하면 앞서 살펴봤던 테스트를 이렇게 변경할 수 있다.

```java
@SpringBootTest
public class TestClass {

    @Test
    @AutoParams
    @UseBeans
    void testMethod(MessageSupplier service, String name) {
        String message = service.getMessage(name);
        assertTrue(message.startsWith("Hello"));
        assertTrue(message.contains(name));
    }
}
```

이 코드에는 다음과 같은 변화가 있다.

- `@ParameterizedTest`와 `@AutoSource` 조합을 유지할 수도 있지만 `@Test`와 `@AutoParams` 조합을 사용할 수도 있다.

- `@UseBeans` 애노테이션을 테스트 메서드에 붙이면, 매개변수 중에서 Spring 빈은 `ApplicationContext`에서 가져온 개체가 주입되고 나머지는 AutoParams가 생성해준다.

- 매개변수의 순서와 관계없이 동작한다. Spring 빈이 먼저 오든, 다른 테스트 데이터가 먼저 오든 상관없다.

- Spring 빈 매개변수에 `@Autowired`를 적용할 필요가 없고, **오히려 적용하면 오류가 발생한다**.

이렇게 `@UseBeans` 애노테이션을 사용하는 방식은 기존 방식보다 조금 더 간단하고 명확하게 의도를 표현하고 구현할 수 있다.
