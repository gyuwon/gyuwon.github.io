---
layout: post
title:  정말로 테스트 대역이 필요한가
date:   2020-05-10 22:30:00 +0900
categories: blog
permalink: /blog/do-you-really-need-test-doubles/
redirect_from:
  - /blog/2020/05/10/do-you-really-need-test-doubles.html
---

얼마전 주니어 동료가 내 코드를 리뷰하며 이런 질문을 했다.

> 의존 대상을 추상화 해서 테스트 대역을 사용하면 테스트 조건을 설정하기 쉬울 것 같은데 왜 실제 코드를 사용했나?

처음 받는 질문이 아니다. 그동안 같은 질문을 여러 프로그래머들에게서 수십 번은 받았다. [테스트 대역(test double)](https://en.wikipedia.org/wiki/Test_double)보다 실제 [DOC(depended-on component)](http://xunitpatterns.com/DOC.html)를 선호해야 한다는 주장이 최근에 시작된 건 아니지만 아직 많은 프로그래머들이 단위 테스팅을 위해 테스트 대역이 필수라고 생각한다.

다른 주니어 동료가 반문했다.

> 테스트를 편하게 하기 위해 운영 코드 설계를 변경하는 것이 옳은 선택인가?

<!--more-->

아주 중요한 지적이다. 내가 만든 실제 DOC는 상속이 금지된 클래스다. 난 상속 필요가 발견되지 않는 한 클래스의 상속을 금지시킨다. 상속 허용을 설계로 간주하며 설계 확장에 보수적 입장을 유지하기 때문이다. 그래서 테스트 대역을 사용하려면 인터페이스를 추가하거나 상속을 허용해 추상화 계층을 설계에 더해야 한다. 테스트를 위해 운영 코드 설계가 복잡해지는 것이다. 그리고 이번 코드의 경우 [SUT(system under test)](http://xunitpatterns.com/SUT.html)와 실제 DOC는 결합 대상이 아니라 응집 대상이다. 추상화 계층은 응집을 낮출 것이다.

추상화가 테스트를 위한 것이 아닌 운영 코드 설계에 의한 상황에서도 명심해야 할 것이 있다. 테스트 대역은 가정을 포함한다. 당연한 얘기다. 테스트 대역을 사용하는 테스트는 동일한 입력에 대해 테스트 대역이 실제 DOC와 동일하게 동작할 것이라는 가정 위에 쓰여진다. 가정이 생긴다는 건 테스트 신뢰도와 테스트를 통해 얻을 수 있는 안정감이 줄어든다는 뜻이다. 예를 들어 시간이 흘러 실제 DOC 코드가 수정되어도 테스트가 의존하는 가정이 여전히 견고할 수 있을지 생각해보자. 실제로 내가 mockist에서 classicist로 전향한 결정적 계기는 테스트 대역이 만든 가정이 깨져 시스템 장애가 발생한 경험이다.

그러니까 나는 가능하면 테스트 대역을 사용하지 않으려 한다. 테스트 대역을 써야만 단위 테스트라는 주장에 반하는 것이다. 나같이 보잘 것 없는 프로그래머의 의견은 설득력이 없으니 권위에 호소하는 논증을 저지르자면 Kent Beck은 테스트 대역을 잘 사용하지 않는다고 말했다.

그러면 테스트 대역은 항상 피해야 할 대상인가? Martin Fowler는 '항상이라고 말하는 것을 항상 피해야한다'고 했다. 나는 몇가지 질문을 통해 테스트 대역 사용을 결정한다.

첫번째, 의존성 구현체가 존재하는가? SUT와 실제 DOC 사이의 계약은 설계되었지만 두 구성요소가 다른 프로그래머나 조직에 의해 동시에 개발될 수 있다. 이런 경우 SUT는 선택의 여지 없이 테스트 대역에 의존해야만 테스트 될 수 있다.

두번째, 같은 입력에 대응하는 출력이 결정적인가? 실제 DOC는 불안정할 수 있다. 실제 DOC가 다른 팀에 의해 관리되는 원격 서비스나 협력사 API라면 네트워크 문제나 클라우드 플랫폼 장애 등으로 인해 언제라도 명세를 벗어나는 출력을 얻을 수 있다. SUT가 이런 명세를 벗어나는 상황에 대응해야 할지라도 테스트가 의존성의 동작을 제어할 수 없다면 테스트 결과는 신뢰할 수 없다. 나는 이런 경우 테스트 대역을 적극 검토한다.

세번째, 실제 DOC 동작이 충분히 빠른가? 테스팅에, 특히 단위 테스팅에 속도는 아주 중요하다. 테스트 실행에 긴 시간이 소요되면 프로그래머는 테스트 실행을 주저하게 된다. 이것이 지속되면 테스트 작성마저 주저하게 된다. 보통 테스트 대역은 아주 빠르다. 실제 DOC가 한 번 동작하는 데 수백 밀리초에서 수 초가 소요된다면 나는 테스트 대역을 고려해 본다. 테스트 대역은 천 배 이상 빠를 것이다. 테스트 작성과 실행을 주저하는 것은 테스트 대역이 만드는 가정보다 해롭다.

네번째, 인터페이스가 단순한가? 실제 DOC의 인터페이스가 단순할수록 테스트 대역이 만드는 가정은 작아진다. 테스트 대역이 테스트 신뢰도와 안정감을 적게 훼손한다는 뜻이다. 하지만 인터페이스를 단순하게 만드는 건 많은 훈련이 필요하다. 코드의 요구사항을 집요하게 파고들어야 하고 그렇게 발견한 요구사항에 필요하지 않은 설계는 넣지 않아야 한다. 하지만 훈련되지 않은 프로그래머는 요구사항을 쉽게 받아들이고 예측에 기반해 설계를 확장하려는 경향을 가진다. 단순한 입출력은 테스팅 뿐 아니라 건강한 설계에도 결정적인 역할을 한다.

나는 이런 기준들을 사용해 테스트 대역 사용 여부를 결정한다. 테스트 대역은 프로그래머가 사용할 수 있는 도구다. 모든 도구는 각각 쓰임새가 있고 잘못된 곳에 사용되면 얻는 것보다 잃는 것이 클 수 있다. 테스트 대역을 통해 무엇을 얻을 수 있고 무엇을 잃을 수 있는지 이해하고 테스트 대역을 사용하라.
