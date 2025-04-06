---
layout: post
title:  "@AutoParams 애노테이션"
date:   2025-04-06 19:00:00 +0900
categories: [blog, AutoParams]
permalink: /autoparams/autoparams/
---

AutoParams를 처음 만들기 시작할 당시, JUnit 5의 `@Test` 메서드는 매개변수를 가질 수 없고 매개변수가 있는 테스트는 반드시 `@ParameterizedTest`를 사용해야 한다고 오해했다. 이런 인식 때문에 `@ParameterizedTest`와 함께 사용할 수 있는 `@AutoSource` 애노테이션을 만들었고, 이후 AutoParams의 기능은 이 `@AutoSource` 애노테이션을 중심으로 확장되었다.

하지만 시간이 지나고 JUnit 5 API에 대한 이해가 깊어지면서, `@Test` 메서드도 매개변수를 가질 수 있고, 이를 지원하는 확장 기능을 구현할 수 있다는 점을 알게 되었다.

이런 배경에서 [**10.0.0 버전 릴리스**](https://github.com/AutoParams/AutoParams/releases/tag/10.0.0)에서는 `@Test` 메서드에 사용할 수 있는 **`@AutoParams`** 애노테이션이 새로 추가되었다.

<!--more-->

`@AutoParams`와 `@AutoSource`의 사용법을 비교해보면, 간단한 테스트를 기존 방식대로 작성할 경우 다음과 같다.

```java
@ParameterizedTest
@AutoSource
void testMethod(int a, int b) {
    Calculator sut = new Calculator();
    int actual = sut.add(a, b);
    assertEquals(a + b, actual);
}
```

같은 테스트를 `@AutoParams` 애노테이션을 사용해서 작성하면 이렇게 표현할 수 있다.

```java
@Test
@AutoParams
void testMethod(int a, int b) {
    Calculator sut = new Calculator();
    int actual = sut.add(a, b);
    assertEquals(a + b, actual);
}
```

`@AutoSource` 애노테이션은 여전히 기존 기능을 그대로 제공하지만, 10.0.0 버전 이후로는 `@Repeat`와 조합해서 테스트를 반복 실행하는 것이 주된 역할이다.

```java
@ParameterizedTest
@AutoSource
@Repeat(10)
void testMethod(int a, int b) {
    Calculator sut = new Calculator();
    int actual = sut.add(a, b);
    assertEquals(a + b, actual);
}
```

그리고 특별한 소스를 통한 데이터와 AutoParams가 생성한 데이터를 조합해서 테스트 메서드에 전달해야 하는 경우, `@ParameterizedTest` 메서드에 사용할 수 있는 `@ValueAutoSource`, `@CsvAutoSource`와 같은 애노테이션도 그대로 변경 없이 제공된다.
