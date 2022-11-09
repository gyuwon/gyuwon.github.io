---
layout: post
title: 테스트 데이터 생성기 AutoParams 소개
date: 2022-12-10 00:00:00 +0900
categories: blog
---

AutoParams를 한 문장으로 표현하면 AutoFixture의 Java 버전이다. JUnit 5 매개변수화 테스트(parameterized tests)를 지원할 목적으로 만들어졌기 때문에 `@ParameterizedTest` 애노테이션 프레임워크를 따르고 엔진 구현에 AutoFixture 보다 순수함수를 사용한 설계가 많이 사용되었지만 핵심은 AutoFixture를 Java 세상에 투영한 것이다. 적어도 지금까진 그렇다. 그렇다면 왜 시작시점이 2021년 3월이었을까?

<!--more-->

AutoParams 프로젝트의 [첫 커밋](https://github.com/AutoParams/AutoParams/commit/485efadc82f76f2d8fcce429610ee68cab1b0fa9)은 2021년 3월 6일에 작성되었다. 그리고 [첫 배포가 이뤄진 커밋](https://github.com/AutoParams/AutoParams/commit/cfc53008758df371ea2d27be9ea3a07a15a27614)이 작성된 건 2021년 3월 13일이다. 그리고 그 사이 2021년 3월 8일에 나는 강남언니 팀에 합류했다.

![Initial commit](/assets/autoparams-initial-commit.png)
![Publish 0.0.1](/assets/autoparams-0.0.1-commit.png)

강남언니 이전 회사에서 내 코드 기여의 80% 정도는 C#으로 작성되었다. 나머지의 대부분은 Java, 그리고 Kotlin과 Python 약간씩. 나는 거의 모든 코드를 TDD로 작성하고 거의 모든 테스트 코드는 AAA(Arrange/Act/Assert) 패턴을 사용하는데 .NET 생태계에는 Arrange 코드 작성을 도와주는 AutoFixture라는 도구가 있다. 테스트에 필요한 데이터나 개체를 자동으로 만들어준다. AutoFixture는 10년 이상 된 프로젝트이고 나는 2015년부터 사용했다. AutoFixture는 내 테스트 코드 작성 생산성을 극적으로 높여줬다.

난 팀을 선택할 때 사용하는 프로그래밍 언어를 고려하지 않는다. 강남언니는 코드베이스 대부분이 Java로 작성되어 있었다. AutoFixture는 사용할 수 없는 것이다. 다시 말하면 이제 Arrange 코드를 한땀한땀 직접 만들어야 한다는 뜻이고 이건 나에게 괴로운 상황이었다. 유선 전화기를 쓰다가 스마트 폰으로 바뀌 몇년간 사용했는데 다음주부터 다시 유선 전화기를 사용해야 한다고 감정이입 해보자. 나만 괴로운가? 팀원들을 괴로움을 인지하지도 못한 채 낮은 생산성 진흙탕에서 무작정 열심히 뛰게 할 것인가?

아마 이 글을 읽는 가장 많은 Java 프로그래머들이 궁굼해 할 다음 질문은 AutoParams가 어떻게 테스트 코드 작성 생산성을 높여주는가 일거라 추측한다. 예제 코드가 아닌 현장의 프로젝트에서 테스트 데이터를 준비하는 일은 매우 귀찮은 일이고 이 감정이 커지면 프로그래머는 테스트 코드 작성을 기피하게 될 수 있다.
