---
layout: post
title: 커밋 메시지 주도 개발
date: 2021-04-04 14:00:00 +0900
categories: blog
permalink: /blog/commit-message-driven-development/
redirect_from:
  - /blog/2021/04/04/commit-message-driven-development.html
---

나는 자신이 없는 코딩을 할 수록 계획을 작게 나눈다. 작은 계획을 실천하도록 사용하는 도구 중 한가지는 코드를 쓰기 전에 내가 어떤 코드를 쓸 지 커밋 메시지를 먼저 작성하는 것이다. 누군가에게 이 기법을 소개할 때에 '커밋 메시지 주도 개발'이란 표현을 쓴다.

<!--more-->

만약 내가 잘 해낼 수 있을 거란 자신감이 충분할 때엔 백로그나 태스크 수준에서 코딩을 한 뒤 변경된 코드를 어떻게 나눠 커밋할 지 고민하는 반면, 내가 조금 더 집중해야 한다는 판단이 들면 코드 베이스에 어떤 변화를 줘야할 지 먼저 글로 작성해 본다. 그리고 이 범위를 넘는 코드 추가나 변경은 커밋하기 전에 만들지 않는다. 커밋 메시지 범위를 벗어나지만 꼭 필요한 일이 발견되면 일단 할 일 목록에 추가한다. 그리고는 다음 번 커밋의 메시지를 쓸 때 할 일 목록에서 가장 높은 우선순위를 갖는 하나를 고른다.

커밋 메시지 주도 개발을 사용하면 불필요한 코딩과 생각의 비용을 줄일 수 있고 작은 보폭의 전진이 유지되어 문제가 발생했을 때 값 싸게 고칠 수 있다. 지출 계획을 세운 소비와 그렇지 않은 경우를 떠올리면 어떤 장점을 가지는 지 쉽게 이해할 수 있을 것이다. 이런 방법은 테스트 주도 개발이 습관이 되며 자연스럽게 터득한 방법인데 재밌게도 주위에서 이런 방법을 쓴다고 하는 사람을 한 명 본 적이 있다.

난 이 방식을 쓰는 여부와 무관하게 커밋 메시지는 [50/72 규칙](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)을 따른다. Windows 용 Git Bash의 Vim에는 기본으로 50/72 규칙이 적용되어 있기 때문에 코딩을 먼저 할 때엔 그것을 사용하고, 커밋 메시지 주도 개발을 할 때엔 Visual Studio Code에서 커밋 메시지를 먼저 작성한다.

나는 주로 Consolas와 맑은 고딕 글꼴을 사용하는데 안타깝지만 Visual Studio Code에서 맑은 고딕 글꼴의 한글 문자는 정확한 전폭(full width)을 사용하지 않는다. 고맙게도 네이버가 배포하는 ['네이버 나눔고딕 코딩글꼴'](https://github.com/naver/nanumfont)의 한글 문자는 전폭을 정확히 사용한다. 그래서 이 글꼴을 설치하고 'Git Commit Message' 언어에 대해 이 글꼴과 50, 72 눈금자를 설정하면 문제가 해결된다.

```json
{
    "[git-commit]": {
        "editor.rulers": [50, 72,],
        "editor.fontFamily": "NanumGothicCoding"
    }
}
```

팀에 좋은 코드 리뷰 문화를 정착시키기 위해 작은 커밋 작성은 꼭 필요하다. 작은 커밋을 만드는 건 처음 해보면 쉽지 않을 수 있는데 연습하는 과정에서 커밋 메시지 주도 개발이 도움이 될 수 있을 것이다.
