---
layout: post
title:  당신의 시스템은 도메인 주도 설계에 기반하는가
date:   2019-12-07 17:00:00 +0900
categories: blog
permalink: /blog/is-your-system-based-on-ddd/
redirect_from:
  - /blog/2019/12/07/is-your-system-based-on-ddd.html
---

> 당신의 시스템은 도메인 주도 설계에 기반하는가?

이런 질문을 종종 받는데 난 그렇게 생각하지 않는다고 답한다. 도메인 전문가와의 긴밀한 협력 없이는 도메인 주도 설계(domain-driven design)의 의미와 가치가 급감하는데 이게 소프트웨어 전문가의 노력만으론 해결되지 않더라. 도메인 전문가도 도메인 주도 설계의 철학과 방식에 어느 정도는 공감을 가져야 한다. 그런데 프로그래머들도 도메인 주도 설계를 잘 이해하지 못하는데 어떻게 비즈니스 조직을 설득할 것인가?

<!--more-->

그래서 많은 팀이 도메인 주도 설계의 [전술적 설계 패턴(tactical design patterns, building blocks)](https://en.wikipedia.org/wiki/Domain-driven_design#Building_blocks)의 일부만을 차용한다. 내 팀도 예외는 아니다. Vaughn Vernon은 이것을 [DDD-Lite](http://www.informit.com/articles/article.aspx?p=1944876&seqNum=6)로 정의한다.


말은 'Lite'라고 하지만 이것도 절대 쉽지 않다. 집합체(aggregate) 경계를 결정하거나 인프라 기술에 오염되지 않은 모델을 유지하는 작업은 엄청난 고민이 요구된다. 프로그래밍은 타이핑이 아니라 생각하는 일이란 걸 절감하게 된다. 물론 잘 설계된 집합체와 순수한 도메인 모델은 분명히 프로그래머에게 보답할 것이다. 그만큼 DDD-Lite는 아주 유용한 프로그래밍 도구다.

그래도 난 만족스럽지 않다. 도메인 전문가의 도움 없이는 유비쿼터스 언어와 [분리된 맥락(bounded context)](https://en.wikipedia.org/wiki/Domain-driven_design#Bounded_context)을 잘 세울 수 없다. 언어 구조가 부실하면 전술적 설계 패턴 결과물도 사상누각이다. 그래서 난 DDD-Lite를 DDD의 시작이라 부르는 것을 경계한다. 이렇게 부르는 순간 프로그래머는 경계심을 풀어버릴 지 모른다. 참고로 Eric Evans 트위터 계정의 자기소개는 'Domain Linguist'다.

> 그래서 어쩔 건데?

라고 물어보면 나도 아직 잘 모르겠다. 일반적으로 소프트웨어 전문가는 멍청하고 바쁘며 도메인 전문가는 똑똑하고 바쁘다. 멍청한 전자가 똑똑한 후자를 이해시키는 건 너무 어려운 일이다. 안그래도 둘 다 너무 바쁜데 말이다. 아직 내가 설계한 시스템은 도메인 주도 설계에 기반한다고 말할 수준은 아니라고 생각하는 것이 나의 미래를 위한 유일한 안전장치다.
