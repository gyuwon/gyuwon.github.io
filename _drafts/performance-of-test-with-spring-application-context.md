---
layout: post
categories: blog
---

Spring 응용프로그램 개발에 TDD를 적용하는 과정에서 테스트 실행 속도와 관련된 질문이 자주 제기된다. 특히, `@SpringBootTest`를 사용한 테스트의 실행 성능과 관련한 고민을 많이 듣는다. 이와 관련된 [인프런 Spring Boot TDD 강의의 질문](https://www.inflearn.com/community/questions/1599646/)에 대한 답변을 작성했고, 응용프로그램 컨텍스트(Spring application context)를 사용하는 테스트의 실행 성능에 대한 경험을 한 번 더 정리한다.

<!--more-->

우선 인프런 Spring Boot TDD 강의 실습은 120개 이상의 테스트가 모두 `@SpringBootTest`를 사용해 작성되었다. 그렇게 작성한 이유는 Spring Boot 앱을 TDD로 개발하는 기본적인 방법을 단단히 다지는 것이 강의의 목적이기 때문이다. 실습 마지막에 테스트 실행 성능을 개선하는데, 거의 모든 테스트가 HTTP 요청으로 동작하고 일부 테스트는 복잡하고 긴 시나리오에 기반함에도 테스트를 모두 실행하는데 걸리는 시간은 대략 6초 정도로 감소된다. 이 정도면 작업 흐름이 끊기는 일은 거의 없을 것이다.

여기서 `@SpringBootTest`를 사용한 테스트 실행 시간에 많은 시간이 소비되지 않는 가장 큰 이유는 응용프로그램 컨텍스트 재사용이다. 테스트 수가 100개 이상이지만 응용프로그램 컨텍스트는 2개만 준비되어 재사용된다. 강의 실습의 경우 응용프로그램 컨텍스트 하나가 준비되는 데 400~700 밀리초 정도 소요된다. 이 시간은 테스트 수가 늘어나도 변경되지 않기 때문에 테스트가 많아질수록 상대적으로 응용프로그램 컨텍스트 준비 시간이 차지하는 비중이 줄어드는 것이다.

<figure style="text-align: center; margin: 2em auto;">
    <figcaption style="margin-bottom: 1em; font-size: 16px;">여러 테스트가 하나의 응용프로그램 컨텍스트 공유</figcaption>
    <div style="text-align: center;">
        <svg width="550" height="260" xmlns="http://www.w3.org/2000/svg" style="margin: 0 auto; display: block;">
            <!-- Context -->
            <rect x="25" y="20" width="500" height="150" rx="10" fill="#f8f8f8" stroke="#666666" stroke-width="2"/>
            <text x="275" y="50" font-size="16" fill="#424242" text-anchor="middle">Context</text>
            <!-- Beans -->
            <rect x="55" y="80" width="120" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="115" y="105" font-size="16" fill="#424242" text-anchor="middle">Bean A</text>
            <rect x="215" y="80" width="120" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="275" y="105" font-size="16" fill="#424242" text-anchor="middle">Bean B</text>
            <rect x="375" y="80" width="120" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="435" y="105" font-size="16" fill="#424242" text-anchor="middle">Bean C</text>
            <!-- Test classes -->
            <rect x="55" y="200" width="120" height="40" rx="5" fill="#e8e8e8" stroke="#888888" stroke-width="1.5"/>
            <text x="115" y="225" font-size="16" fill="#424242" text-anchor="middle">Test #1</text>
            <rect x="215" y="200" width="120" height="40" rx="5" fill="#e8e8e8" stroke="#888888" stroke-width="1.5"/>
            <text x="275" y="225" font-size="16" fill="#424242" text-anchor="middle">Test #2</text>
            <rect x="375" y="200" width="120" height="40" rx="5" fill="#e8e8e8" stroke="#888888" stroke-width="1.5"/>
            <text x="435" y="225" font-size="16" fill="#424242" text-anchor="middle">Test #3</text>
            <!-- Connecting arrows -->
            <line x1="115" y1="200" x2="115" y2="170" stroke="#666666" stroke-width="2" stroke-dasharray="5,5"/>
            <line x1="110" y1="180" x2="115" y2="170" stroke="#666666" stroke-width="2"/>
            <line x1="120" y1="180" x2="115" y2="170" stroke="#666666" stroke-width="2"/>
            <line x1="275" y1="200" x2="275" y2="170" stroke="#666666" stroke-width="2" stroke-dasharray="5,5"/>
            <line x1="270" y1="180" x2="275" y2="170" stroke="#666666" stroke-width="2"/>
            <line x1="280" y1="180" x2="275" y2="170" stroke="#666666" stroke-width="2"/>
            <line x1="435" y1="200" x2="435" y2="170" stroke="#666666" stroke-width="2" stroke-dasharray="5,5"/>
            <line x1="430" y1="180" x2="435" y2="170" stroke="#666666" stroke-width="2"/>
            <line x1="440" y1="180" x2="435" y2="170" stroke="#666666" stroke-width="2"/>
        </svg>
    </div>
</figure>

반면에 실무에서 테스트 클래스마다 필요한 테스트 대역을 준비하기 위해서 응용프로그램 컨텍스트 구성이 계속 달라지는 경우는 새로운 응용프로그램 컨텍스트를 계속 준비하기 때문에 실행 시간이 길어질 수 있다. 과도한 테스트 대역 사용이 일으킬 수 있는 문제 중 하나다.

<figure style="text-align: center; margin: 2em auto;">
    <figcaption style="margin-bottom: 1em; font-size: 16px;">각 테스트가 별도의 응용프로그램 컨텍스트 사용</figcaption>
    <div style="text-align: center;">
        <svg width="550" height="345" xmlns="http://www.w3.org/2000/svg" style="margin: 0 auto; display: block;">
            <!-- Context 1 -->
            <rect x="25" y="20" width="150" height="230" rx="10" fill="#f8f8f8" stroke="#666666" stroke-width="2"/>
            <text x="100" y="50" font-size="16" fill="#424242" text-anchor="middle">Context 1</text>
            <!-- Context 2 -->
            <rect x="200" y="20" width="150" height="230" rx="10" fill="#f8f8f8" stroke="#666666" stroke-width="2"/>
            <text x="275" y="50" font-size="16" fill="#424242" text-anchor="middle">Context 2</text>
            <!-- Context 3 -->
            <rect x="375" y="20" width="150" height="230" rx="10" fill="#f8f8f8" stroke="#666666" stroke-width="2"/>
            <text x="450" y="50" font-size="16" fill="#424242" text-anchor="middle">Context 3</text>
            <!-- Beans in Context 1 -->
            <rect x="45" y="70" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="100" y="95" font-size="16" fill="#424242" text-anchor="middle">Test Double A</text>
            <rect x="45" y="120" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="100" y="145" font-size="16" fill="#424242" text-anchor="middle">Bean B</text>
            <rect x="45" y="170" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="100" y="195" font-size="16" fill="#424242" text-anchor="middle">Bean C</text>
            <!-- Beans in Context 2 -->
            <rect x="220" y="70" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="275" y="95" font-size="16" fill="#424242" text-anchor="middle">Bean A</text>
            <rect x="220" y="120" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="275" y="145" font-size="16" fill="#424242" text-anchor="middle">Test Double B</text>
            <rect x="220" y="170" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="275" y="195" font-size="16" fill="#424242" text-anchor="middle">Bean C</text>
            <!-- Beans in Context 3 -->
            <rect x="395" y="70" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="450" y="95" font-size="16" fill="#424242" text-anchor="middle">Bean A</text>
            <rect x="395" y="120" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="450" y="145" font-size="16" fill="#424242" text-anchor="middle">Bean B</text>
            <rect x="395" y="170" width="110" height="40" rx="5" fill="#f0f0f0" stroke="#666666" stroke-width="1.5"/>
            <text x="450" y="195" font-size="16" fill="#424242" text-anchor="middle">Test Double C</text>
            <!-- Test classes -->
            <rect x="40" y="280" width="120" height="40" rx="5" fill="#e8e8e8" stroke="#888888" stroke-width="1.5"/>
            <text x="100" y="305" font-size="16" fill="#424242" text-anchor="middle">Test #1</text>
            <rect x="215" y="280" width="120" height="40" rx="5" fill="#e8e8e8" stroke="#888888" stroke-width="1.5"/>
            <text x="275" y="305" font-size="16" fill="#424242" text-anchor="middle">Test #2</text>
            <rect x="390" y="280" width="120" height="40" rx="5" fill="#e8e8e8" stroke="#888888" stroke-width="1.5"/>
            <text x="450" y="305" font-size="16" fill="#424242" text-anchor="middle">Test #3</text>
            <!-- Connecting arrows -->
            <line x1="100" y1="280" x2="100" y2="250" stroke="#666666" stroke-width="2" stroke-dasharray="5,5"/>
            <line x1="95" y1="260" x2="100" y2="250" stroke="#666666" stroke-width="2"/>
            <line x1="105" y1="260" x2="100" y2="250" stroke="#666666" stroke-width="2"/>
            <line x1="275" y1="280" x2="275" y2="250" stroke="#666666" stroke-width="2" stroke-dasharray="5,5"/>
            <line x1="270" y1="260" x2="275" y2="250" stroke="#666666" stroke-width="2"/>
            <line x1="280" y1="260" x2="275" y2="250" stroke="#666666" stroke-width="2"/>
            <line x1="450" y1="280" x2="450" y2="250" stroke="#666666" stroke-width="2" stroke-dasharray="5,5"/>
            <line x1="445" y1="260" x2="450" y2="250" stroke="#666666" stroke-width="2"/>
            <line x1="455" y1="260" x2="450" y2="250" stroke="#666666" stroke-width="2"/>
        </svg>
    </div>
</figure>

참고로 나는 그동안 실무에서 Spring Boot 응용프로그램을 개발할 때 `@SpringBootTest`를 사용한 테스트는 10~20% 정도만 작성하고, 나머지는 Spring 독립적인 순수한 모델 영역의 범위를 크게 만들어서 이 부분을 대상으로 80~90% 정도 테스트를 작성했다. 이렇게 하면 응용프로그램 컨텍스트 준비 시간이 방해가 되는 일이 많지는 않다. 또, 여기서 자세히 설명하지는 않지만 응용프로그램 컨텍스트를 재사용하고 캐싱을 유지하면서 테스트 대역을 사용하는 방법도 있다.

테스트 실행 시간이 짧으면 분명히 장점이 있지만 제품 요구사항과 밀접한 테스트의 자동화가 주는 가치가 크기 때문에 각 환경에 따라 응용프로그램 컨텍스를 사용한 테스트에 무조건 거부감을 갖기보다는 실용적인 접근이 필요하다.
