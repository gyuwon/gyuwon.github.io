---
layout: post
title:  AutoParams에 도입된 DSL(Domain-Specific Language)
date:   2025-04-06 12:00:00 +0900
categories: [blog, AutoParams]
---

AutoParams의 [10.0.0 버전 릴리스](https://github.com/AutoParams/AutoParams/releases/tag/10.0.0)에는 처음으로 DSL(domain-specific language)이 도입됐다. 이 버전에 추가된 DSL은 테스트 데이터를 생성할 때 매개변수 인자를 고정하는 몇 가지 간결한 방법을 제공한다. 이 글은 DSL 사용 사례를 하나를 소개하고 기존 방식과의 차이를 비교한다.

<!--more-->

### ObjectGenerator 인터페이스 구현을 통한 매개변수 인자 고정

예를 들어 `Product` 클래스와 `Review` 클래스가 있다고 해보자. `Product`는 상품 정보를 나타내고, `Review`는 특정 상품에 대한 사용자의 평가를 나타낸다. `Review` 클래스는 내부에 `Product`를 포함한다.

```java
@AllArgsConstructor
@Getter
public class Product {

    private final UUID id;
    private final String name;
    private final BigDecimal priceAmount;
}
```

```java
@AllArgsConstructor
@Getter
public class Review {

    private final UUID id;
    private final UUID reviewerId;
    private final Product product;
    private final int rating;
    private final String comment;
}
```

테스트 상황에 따라 `product` 필드와 `rating` 필드의 값을 고정해서 `Review` 개체를 생성하고 싶은 경우가 있다. 지금까지는 이런 요구를 코드로 표현하기 위해서 다음과 같은 준비가 필요했다.

먼저 `product` 필드 값을 고정하려면 생성자 매개변수 인자 값을 제공하는 `ObjectGenerator` 인터페이스 구현체를 만든다.

```java
public record ProductArgumentFreezer(Product product) implements ObjectGenerator {

    @Override
    public ObjectContainer generate(ObjectQuery query, ResolutionContext context) {
        if (query instanceof ParameterQuery parameterQuery) {
            Parameter parameter = parameterQuery.getParameter();
            if (parameter.isNamePresent() && parameter.getName().equals("product")) {
                return new ObjectContainer(product);
            }
        }

        return ObjectContainer.EMPTY;
    }
}
```

`generate` 메서드는 주어진 `ObjectQuery`가 이름이 `"product"`인 매개변수를 가리키는지를 판단하고, 조건에 따라 고정된 `Product` 개체를 반환한다. 처리 과정은 다음과 같다.

1. 입력으로 전달된 `query`가 `ParameterQuery`의 인스턴스인지 확인한다.

1. `getParameter` 메서드를 호출해서 매개변수 정보를 가져온다.

1. `isNamePresent` 메서드를 통해 매개변수 이름이 런타임에 노출되는지를 확인한다.

1. 매개변수 이름이 `"product"`와 일치하는 경우, `ProductArgumentFreezer`의 `product` 필드 값을 `ObjectContainer`에 담아서 반환한다.

1. 위 조건 중 하나라도 만족하지 않으면, 다른 코드에 생성 작업을 위임하도록 빈 컨테이너인 `ObjectContainer.EMPTY`를 반환한다.

`rating` 값도 마찬가지로 비슷한 `ObjectGenerator` 구현체가 필요하다.

```java
public record RatingArgumentFreezer(int rating) implements ObjectGenerator {

    @Override
    public ObjectContainer generate(ObjectQuery query, ResolutionContext context) {
        if (query instanceof ParameterQuery parameterQuery) {
            Parameter parameter = parameterQuery.getParameter();
            if (parameter.isNamePresent() && parameter.getName().equals("rating")) {
                return new ObjectContainer(rating);
            }
        }

        return ObjectContainer.EMPTY;
    }
}
```

테스트 메서드에서 이 구현체들을 적용하면 다음과 같다.

```java
@Test
@AutoParams
void testMethod(Product product, @Max(5) int rating, ResolutionContext context) {
    context.applyCustomizer(new ProductArgumentFreezer(product));
    context.applyCustomizer(new RatingArgumentFreezer(rating));
    Review review = context.resolve();
    assertSame(product, review.getProduct());
    assertEquals(rating, review.getRating());
}
```

이렇게 하면 원하는 동작을 수행하지만, 매개변수 하나를 고정하기 위해 `ObjectGenerator` 구현체를 매번 만들고 조건을 검사하는 코드를 반복해야 한다. 테스트에서 "이 값을 고정하고 싶다"는 단순한 의도를 표현하기에 다소 과한 구조일 수 있다.

### DSL을 사용한 매개변수 인자 고정

하지만 이번에 추가된 DSL을 사용하면 같은 목적을 간단하게 달성할 수 있다.

```java
import static autoparams.customization.dsl.ArgumentCustomizationDsl.freezeArgument;

@Test
@AutoParams
void testMethod(Product product, @Max(5) int rating, ResolutionContext context) {
    context.customize(
        freezeArgument("product").to(product),
        freezeArgument("rating").to(rating)
    );
    Review review = context.resolve();
    assertSame(product, review.getProduct());
    assertEquals(rating, review.getRating());
}
```

`ArgumentCustomizationDsl` 클래스의 `freezeArgument` 정적 메서드로 시작하는 관용구 코드는 매개변수 이름을 기준으로 인자를 고정한다. `ObjectGenerator`를 직접 구현할 필요 없이, 단순하고 선언적인 코드만으로 같은 효과를 낼 수 있다. 코드가 간결해질 뿐 아니라, 테스트의 의도가 더 명확하게 드러난다.

### DSL 도입의 의미

테스트에서 매개변수를 고정하는 이유는 분명하다. 특정 값을 사용한 상태에서 시스템의 동작을 확인하고 싶은 것이다. 10.0.0 버전 이전에는 AutoParams를 사용해서 이 요구를 구현하는 데 적지 않은 준비가 필요했다. `autoparams.customization.dsl` 패키지는 그런 부담을 없애준다. 매개변수 하나를 고정하기 위해서 `ObjectGenerator` 구현체를 새로 만들 필요가 없다. 테스트는 더 간결하고, 더 설명적이며, 더 읽기 쉬워진다.

DSL 추가가 단순한 문법 개선처럼 보일 수도 있지만 실용적인 테스트 작성을 고민해 본 사람이라면 이 변화가 반가울 것이다. AutoParams의 DSL은 테스트의 의도를 효과적으로 드러내는 목적에 한 발짝 더 가까워진 기능이다.
