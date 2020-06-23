---
layout: post
title:  이벤트 소싱의 본질
date:   2020-06-23 23:00:00 +0900
categories: blog
---

이벤트 소싱은 그렇게 어렵지 않다. 이벤트 소싱 패턴은 명령 패턴, CQRS, EDA, DDD 등과 자주 함께 설명되지만 그것들은 이벤트 소싱과 조합될 수 있는 설계 도구일 뿐 이벤트 소싱의 핵심이 아니다. 이벤트 소싱의 본질은 함수형 데이터 기록과 복원이다. 이벤트 소싱을 사용하는 시스템은 과거의 이벤트에 기반해 입력을 새로운 이벤트로 변환한다. 시스템의 이벤트 스트림을 만들어가는 과정이 이벤트 소싱의 정수다.

<!--more-->

> 이 글에서 사용된 Java 코드가 문법을 지키지 않거나 동작하지 않으면 [pull request](https://github.com/gyuwon/gyuwon.github.io/pulls)를 보내 달라.

## 이벤트 소싱 파이프라인 인터페이스

`E`를 이벤트, `[E]`를 이벤트 목록, `C`를 명령이라고 할 때 이벤트 소싱 파이프라인의 입력과 출력은 `[E] * C -> [E]`가 된다. 이벤트 스트림과 이것에 적용될 명령을 사용해 이벤트 스트림에 추가될 새 이벤트를 만드는 것이다. Java 함수로 표현하면 다음과 같다.

```java
BiFunction<Stream<E>, C, Stream<E>>
```

이 간단한 인터페이스를 갖는 함수는 비즈니스 지식을 표현하는 하위 요소들과 이 요소들을 엮는 뼈대로 이뤄진다.

## 비즈니스 지식

모든 응용프로그램은 각자의 독특한 지식을 가진다. 이 지식들을 이벤트 소싱 패턴에 적합하게 구분해 볼 수 있다.

### 시스템 초기 상태 공장

아무 이벤트도 반영되지 않은 시스템의 초기 상태를 만든다. 시스템은 설계에 따라 하나의 엔터티일 수도, 응용프로그램 전체를 나타내는 것일 수도 있다. `T`를 시스템 상태 형식이라고 할 때 입출력은 `() -> T`가 된다. Java 함수로 표현하면 다음과 같다.

```java
Function<Void, T>
```

아주 간단한 구현의 경우 `null`을 반환하는 것도 가능하다.

```java
Function<Void, T> createSeed = unit -> null;
```

### 이벤트 처리기

기존 시스템 상태에 이벤트를 반영해 새로운 시스템 상태를 만든다. 이벤트가 시스템 상태를 변화시키는 규칙은 중요한 비즈니스 지식이다. 이벤트 처리기의 입출력은 `T * E -> T`이며 Java 함수로 표현하면 다음과 같다.

```java
BiFunction<T, E, T>
```

이벤트는 이미 과거에 발생된 사실이기 때문에 이벤트 처리기는 기존 시스템 상태와 이벤트를 검증하지 않으며 항상 성공한다. 예를 들어 `Account` 시스템과 `BalanceChanged` 이벤트가 있을 때 이벤트 처리기는 이렇게 구현될 수 있다.

```java
BiFunction<Account, Object, Account> handleEvent = (account, event) ->
    event instanceof BalanceChanged
    ? new Account(account.getNumber(), ((BalanceChanged)event).getBalance())
    : account;
```

### 이벤트 생성기

기존 시스템 상태를 고려해 명령을 이벤트로 변환한다. 경우에 따라 하나의 명령이 둘 이상의 이벤트를 연달아 생성할 수도 있다. 이벤트 생성기는 불변식(invariants)을 통해 명령이 시스템에 적용돼도 괜찮은지 검증한다. 예를 들어 계좌 시스템에 출금 명령이 전달되면 출금액이 잔액보다 크지 않은지 검증한다. 불변식과 명령의 결과를 표현하는 이벤트 설계 역시 중요한 비즈니스 지식이다. 불변식을 위배하면 예외를 던지는 것이 흔한 구현이지만 부작용을 고려하지 않은 이벤트 생성기의 입출력은 `T * C -> [E]`이며 Java 함수로 표현하면 다음과 같다.

```java
BiFunction<T, C, Stream<E>>
```

예를 들어 `Account` 시스템과 `Withdraw` 명령이 있을 때 이벤트 생성기는 이렇게 구현될 수 있다.

```java
BiFunction<Account, Object, Stream<Object>> produceNewEvents = (account, command) ->
    command instanceof Withdraw && ((Withdraw)command).getAmount() <= account.getBalance()
    ? Stream.<Object>of(new BalanceChanged(account.getBalance() - ((Withdraw)command).getAmount()))
    : Stream.<Object>empty();
```

## 이벤트 소싱 파이프라인 조립

이제 준비된 비즈니스 지식 구성요소를 조립하면 이벤트 소싱 파이프라인 함수를 만들 수 있다.

이 때 과거의 이벤트를 집계해 시스템의 최근 상태를 복원해야 하는데 그저 fold left 고차함수에 이벤트 처리기 함수와 시스템 초기 상태를 적용하면 된다. 현대적 언어는 대부분 fold left 함수를 기반 라이브러리로 제공한다. 예를 들어 .NET에는 [`Aggregate`](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.aggregate?view=netcore-3.1#System_Linq_Enumerable_Aggregate__2_System_Collections_Generic_IEnumerable___0____1_System_Func___1___0___1__) 함수가 있고 JavaScript에는 [`reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 함수가 있다. 불행하게도 Java에는 없다. [`reduce`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#reduce-U-java.util.function.BiFunction-java.util.function.BinaryOperator-) 함수도 `combiner`가 필요하기 때문에 적합하지 않다. 당황하지 말고 직접 만들자.
```java
<T, U> U foldl(BiFunction<U, T, U> f, U z, Stream<T> xs) {
    Iterator<T> i = xs.iterator();
    U a = z;
    while (i.hasNext()) a = f.apply(a, i.next());
    return a;
}
```

필요한 모든 함수를 얻었다. 조립하자.

```java
<T, C, E> BiFunction<Stream<E>, C, Stream<E>> buildEventSourcingPipeline(
    Function<Void, T> createSeed,
    BiFunction<T, E, T> handleEvent,
    BiFunction<T, C, Stream<E>> produceNewEvents) {

    return (pastEvents, command) -> createSeed
        .andThen(seed -> foldl(handleEvent, seed, pastEvents))
        .andThen(state -> produceNewEvents.apply(state, command))
        .apply(null);
}
```

이 간단한 코드가 이벤트 소싱 패턴의 핵심 구현이다. 이렇게 조립된 파이프라인 함수는 다양한 이벤트 저장소 구현체, 글머리에 언급된 설계 도구들, 각 현장의 비즈니스 구현 코드와 함께 사용될 수 있다.
