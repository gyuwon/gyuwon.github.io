---
layout: post
title:  TDD는 설계 방법론이 아니다
date:   2019-03-03 13:00:00 +0900
categories: blog
---

소셜 미디어에서 국내 저자의 어떤 서적에서 발췌했다는 다음 문장을 발견했다.

> TDD는 설계를 위한 기법이다.

나는 그 책을 읽지 않았기 때문에 전후 맥락을 모르지만 만약 이 문장을 통한 작가의 의도가 TDD(Test-Driven Development)를 설계 방법론이라고 말하는 것이라면 그 위험한 생각에 서슴치 않고 반대하겠다. 나는 TDD를 즐겨 사용하지만 TDD가 잘 못 사용되어 프로젝트나 제품에 나쁜 영향을 미치는 것을 바라지 않는다.

<!--more-->

만약 TDD가 설계 방법론이라면 우리는 TDD를 통해 좋은 설계를 얻어낼 수 있어야 할 것이다. 하지만 실상은 그렇지 않다.

- TDD는 높은 응집을 유도하지 않는다. 오히려 mockist가 되면 모든 코드 조각의 연결을 결합으로 오해하게 될 가능성이 있다.
- TDD는 단일 책임 원칙과 인터페이스 분리 원칙 위배에 어떤 신호도 주지 않는다. 무겁고 복잡한 책임을 갖거나 불필요한 인터페이스를 노출하는 개체도 테스트 하기 쉬울 수 있다.
- TDD는 인터페이스 일관성을 도출하지 않는다. 함수 족 구성원의 매개변수를 불규칙하게 뒤섞거나 유비쿼터스 언어를 사용하지 않아도 TDD는 잘 진행된다. 고통받는 쪽은 클라이언트 프로그래머 뿐이다.
- TDD의 리팩터링 단계는 좋은 구조를 안내하거나 좋은 구조를 갖도록 강제하지 않는다. 프로그래머 스스로 좋은 구조를 찾아 적용할 기회를 제공할 뿐이다.

그만 나열을 멈춰도 TDD가 모든 중요한 설계 원칙을 지키게 하지 않는다는 것은 증명된다.

하지만 반대로 설계가 TDD에 주는 영향은 크다. 테스트는 클라이언트이며 코드의 API에 의존한다. API가 충분히 설계되지 않은 채 TDD를 사용하면 테스트 케이스가 추가되는 과정에서 API 명세가 지속적으로 변경되며 '깨지기 쉬운 테스트 문제'를 유발하는데 이것은 TDD를 포기하게 하거나 TDD는 죽었다고 말하게 하는 대표적인 이유다.

물론 TDD가 설계에 전혀 영향을 미치지 않는 것은 아니다. TDD는 강한 결합과 의존성 역전 원칙 위배를 경고한다. TDD는 불명확한 설계 지점을 발견한다. 실제로 테스트 주도 설계(Test-Driven Design)를 외치는 사람들이 이것들을 거의 유일한 근거로 제시한다. 하지만 이런 몇 가지 피드백만으로 좋은 설계가 만들어지지는 않는다. 나머지는 TDD 범위 밖의 책임이다. 전적으로 TDD에만 설계를 의존하면 테스트 하기 좋은 쓰레기를 얻게 된다.

설계는 TDD를 비롯해 코딩에서 의사소통까지 프로그래머가 수행하는 모든 작업에 큰 영향을 준다. 좋은 설계는 성공적인 TDD의 필요조건이다. 하지만 TDD는 좋은 설계의 필요조건도 충분조건도 아니다. TDD는 설계 방법론이 아니다.

끝으로 관련된 몇 가지 글을 소개하며 마친다.

- [The TDD Apostate - Mark Seemann](http://blog.ploeh.dk/2010/12/22/TheTDDApostate/)
  > I am convinced that TDD itself does not drive us towards SOLID design.

- [Giving Up on TDD - Robert C. Martin](http://blog.cleancoder.com/uncle-bob/2016/03/19/GivingUpOnTDD.html)
  > You have to DESIGN period. No matter what you are writing; whether a unit test, or an acceptance test, or production code, or a mock, or a stub, you have to DESIGN.

- [Fragile Test - Gerard Meszaros](http://xunitpatterns.com/Fragile%20Test.html)
  > Interface Sensitivity is when a test fails to compile or run because some part of the interface of the SUT that is uses has changed.
