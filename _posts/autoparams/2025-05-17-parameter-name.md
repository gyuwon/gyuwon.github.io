---
layout: post
title:  "매개변수 이름에 의존하는 AutoParams 기능 사용"
date:   2025-05-17 12:00:00 +0900
categories: [blog, AutoParams]
permalink: /autoparams/parameter-name/
---

Java는 기본적으로 바이트코드에 매개변수 이름을 포함하지 않는다. 그렇기 때문에 일부 매개변수 이름에 의존하는 AutoParams 기능이 제대로 작동하려면 다음 방법 중 하나를 사용해야 한다:

<!--more-->

1. Record 클래스를 사용한다. Record는 설계상 매개변수 이름이 보존된다.

1. `javac`를 사용하는 경우 `-parameters` 옵션을, `kotlinc`를 사용하는 경우 `-java-parameters` 옵션을 사용해 컴파일한다. Spring Boot를 사용하는 경우, [Spring Boot Gradle 플러그인](https://docs.spring.io/spring-boot/gradle-plugin/reacting.html#reacting-to-other-plugins.java)이나 [Spring Boot Maven 플러그인](https://docs.spring.io/spring-boot/maven-plugin/using.html#using)을 사용하면 이 옵션이 자동으로 활성화된다.

1. `@ConstructorProperties` 애노테이션을 명시적으로 선언해 매개변수 이름을 지정한다. 이 애노테이션은 일반 메서드에는 적용되지 않으며, 생성자에만 사용할 수 있다. 만약 생성자가 `@AllArgsConstructor`와 같은 Lombok 애노테이션으로 생성되는 경우, `lombok.anyConstructor.addConstructorProperties = true` 옵션을 설정하면 이 애노테이션이 자동으로 추가된다. 자세한 내용은 공식 문서([https://projectlombok.org/features/constructor](https://projectlombok.org/features/constructor))를 참고하자.
