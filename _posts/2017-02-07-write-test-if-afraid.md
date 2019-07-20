---
layout: post
title:  두렵다면 테스트를 작성하라
date:   2017-02-07 01:11:00 +0900
categories: blog
---

얼마전 다음과 같은 코드에 매개변수 `param`에 대한 `null` 여부 검사가 필요한지 의견을 묻는 글을 발견했다.

```csharp
public void MyCode(string param)
{
    if (TheirCode(param))
    {
        DoSomething();
    }
}
```

<!--more-->

나는 원칙적인 의견을 제시했다.

> `MyCode()` 메서드에서 `param` 매개변수의 멤버를 호출하지 않으며 `TheirCode()` 메서드에 전달하기만 하기 때문에 `null` 여부 검사는 할 필요가 없다. `null` 참조 오류 방어에 대한 책임은 `TheirCode()` 메서드에 있다.

하지만 현실에서 원칙을 벗어나는 경우는 허다하다. 때문에 내 의견 뒤로 `TheirCode()`를 신뢰할 수 없으니 안전하게 `null` 여부 검사를 하는 것이 좋겠다는 의견도 다수 있었고 이를 지원하는 기법들도 소개되었다. 이런 의견들을 지켜보며 공통점을 두가지 발견했는데, 운영환경에서 `TheirCode()`가 `null` 참조 오류를 발생시킬지 두려워한다는 것과 그러면서도 아무도 코드 배치(deployment) 전에 직접 확인하려 하지는 않는다는 것이었다.

당시에 조금 바쁜 상황이라 의견을 추가하지 못했는데 이제 그 두려움을 털어낼 방법을 알려주겠다.

테스트를 작성하라.

```csharp
public void when_param_is_null_MyCode_does_not_throw_NullReferenceException()
{
    string param = null;
    Action act = () => sut.MyCode(param);
    act.Should().NotThrow<NullReferenceException>();
}
```

이 테스트 케이스는 `MyCode()`가 `param` 매개변수에 대해 `null` 여부 검사를 해야하는지 명확히 판단해 줄 뿐 아니라 소프트웨어 회귀를 검출하기 때문에 앞으로의 코드 변경에 대해서도 자신감을 줄 것이다. 지불해야 할 댓가는 약간의 지루함 뿐이다. 이제 겁먹지 말고 리팩터하라.
