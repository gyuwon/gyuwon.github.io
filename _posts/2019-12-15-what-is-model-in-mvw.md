---
layout: post
title:  MV* 패턴에서 모델이란 무엇인가
date:   2019-12-15 20:00:00 +0900
categories: blog
permalink: /blog/what-is-model-in-mvw/
redirect_from:
  - /blog/2019/12/15/what-is-model-in-mvw.html
---

> *이 글이 참조한 두 문서의 링크가 현재 정상적으로 동작하지 않는다. 대신 [여기](http://urn.nb.no/URN:NBN:no-14314)에 방문하면 두 문서의 내용이 모두 담긴 파일을 확인할 수 있다.*

두괄식으로 결론부터 제시한다.

> "Models represent knowledge." - [MODELS - VIEWS - CONTROLLERS, 1979](https://heim.ifi.uio.no/~trygver/1979/mvc-2/1979-12-MVC.pdf)

<!--more-->

MVC 패턴을 이해할 때 자주 오해받는 대상이 모델이다. 가장 흔히 발견되는 사례는 모델을 그저 데이터 구조체 정도로 이해하는 것이다. 이러면 자연스럽게 컨트롤러에 비즈니스 논리를 구현해야 한다는 생각에 이른다.<sup><a href="#comment-1">[1]</a></sup> 결국 이런 설계는 결합을 높이고 응집을 낮추며 코드를 재사용하기 어렵게 만든다.

하지만 MV* 패턴의 모델은 소프트웨어가 표현하거나 해결하고자 하는 대상에 대한 지식이다. 모델은 경우에 따라 단순한 데이터 엔터티일 수도, 훨씬 복잡하고 거대한 것일 수도 있다. 만약 도메인 주도 설계(domain-driven design)를 사용하고 있다면 모델은 곧 도메인 모델이 표현된 코드일 수 있다.

본래의 MVC 패턴이 유용한지 아닌지를 떠나서 그것을 더 이해하고 싶다면 [이 문서(THING-MODEL-VIEW-EDITOR, 1979)](https://heim.ifi.uio.no/~trygver/1979/mvc-1/1979-05-MVC.pdf)도 함께 읽으면 좋다.

그런데 이 글에는 다음과 같은 모델에 대한 정의가 있고,

> "A Model is an active representation of an abstraction in the form of data in a computing system"

이 부분만 읽고 끝내면 모델을 단지 데이터 구조 추상화 정도로 오해할 수 있다. 하지만 조금만 더 읽으면 그렇지 않다는 것을 알 수 있다.

> "The models are represented in the computer as a collection of data together with the methods necessary to process these data."

모델은 데이터와 그것과 관련된 행위의 모음인 것이다.

MV* 패턴에서 모델은 도메인 지식을 나타내며 도메인 지식은 많은 명사와 동사를 가진다. 모델이 지식을 풍푸하게 표현하도록 설계될 때 MV* 패턴은 더욱 유용하다.

---

1. <span id="comment-1">난 이런 대답을 기술 면접에서도 많이 들었다. 물론 원칙은 첫번째 고려사항일 뿐이다. 치열한 현장에서 집중해야 할 것은 다른 무엇보다 문제 해결이다.</span>
