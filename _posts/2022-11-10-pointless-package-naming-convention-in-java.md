---
layout: post
title: 쓸모없는 Java 패키지 이름 관습
date: 2022-11-10 00:00:00 +0900
categories: blog
---

Oracle은 [이 페이지](https://docs.oracle.com/javase/tutorial/java/package/namingpkgs.html)에서 다음과 같이 패키지 이름 관습을 제안한다.

> Companies use their reversed Internet domain name to begin their package names—for example, com.example.mypackage for a package named mypackage created by a programmer at example.com.

관습 제안의 이유는 이렇다.

> This works well unless two independent programmers use the same name for their packages. What prevents this problem? Convention.

이건 전통인가 악습인가?

<!--more-->

두 내용을 정리하면, 프로그래머들이 패키지 이름을 겹치지 않게 지정할 수 있도록 회사의 인터넷 도메인 이름을 뒤집어 패키지 이름 앞에 배치하자는 것이다.

하지만 난 지금까지 이런 이름 공간 명명 관습이 쓰이지 않는 생태계에서 이름 공간 이름이 `com.`, `org.` 등으로 시작하지 않기 때문에 코드 이름이 충돌해 문제가 발생했다는 얘기를 들은 적이 없다.

만약 이름이 'Cool Company'이고 `cool-company.com` 도메인을 사용하는 회사에 소속되어 Java로 새로운 클래스를 만드는 중이라면 패키지 이름은 `com.coolcompany.` 대신 그저 `coolcompany.`으로 시작하자. `coolcompany.coredomain.AwesomeClass`! 간결하고 설명력 충분하고 멋지기까지 한 이름이다. 심지어 소스코드 디렉터리 깊이도 줄여줄 것이다.
