---
layout: post
title:  "@Size 애노테이션 사용 컬렉션 크기 지정"
date:   2025-05-17 12:30:00 +0900
categories: [blog, AutoParams]
permalink: /autoparams/collection-size/
---

AutoParams는 컬렉션 형식 인자를 생성할 때 기본적으로 3개의 요소를 포함한다. 많은 경우 이 기본 크기로 충분하지만, 테스트 대상에 따라 더 크거나 더 작은 컬렉션이 필요한 경우도 있다. `Factory<T>` 클래스나 `@Customization` 애노테이션을 사용해서 컬렉션 크기를 조절할 수 있지만, [**10.1.1 버전 릴리스**](https://github.com/AutoParams/AutoParams/releases/tag/10.1.1)에 `@Size` 애노테이션을 사용해 컬렉션을 생성할 때 크기를 지정할 수 있는 기능이 추가되었다.

<!--more-->

`@Size` 애노테이션을 매개변수에 적용하고 `min` 요소에 크기를 지정하면, AutoParams는 지정된 크기의 컬렉션을 생성한다.

```java
@Test
@AutoParams
void testMethod(@Size(min = 5) List<String> list) {
    assertThat(list).hasSize(5);
}
```

`@Size` 애노테이션과 `@FreezeBy` 애노테이션을 조합하면 컬렉션 형식 속성의 크기도 지정할 수 있다. `Book` 클래스가 다음과 같을 때,

```java
@AllArgsConstructor
@Getter
public class Book {

    private final String title;
    private final List<String> authors;
    private final List<String> quotes;
}
```

AutoParams가 생성한 다음 테스트 메서드의 `book` 인자의 `authors` 속성과 `quotes` 속성은 각각 5개의 요소와 7개의 요소를 갖는다.

```java
@Test
@AutoParams
void testMethod(
    @Size(min = 5) @FreezeBy(PARAMETER_NAME) List<String> authors,
    @Size(min = 7) @FreezeBy(PARAMETER_NAME) List<String> quotes,
    Book book
) {
    assertThat(book.getAuthors()).hasSize(5);
    assertThat(book.getQuotes()).hasSize(7);
}
```

> *위 예제는 매개변수 이름에 의존합니다. 매개변수 이름에 대해서는 [이 글](/autoparams/parameter-name/)을 참고하세요.*

[**10.1.2 버전 릴리스**](https://github.com/AutoParams/AutoParams/releases/tag/10.1.2) 이후로는 `Stream<T>` 형식 매개변수에도 `@Size` 애노테이션을 사용해서 스트림의 요소 수를 지정할 수 있다.
