---
layout: post
title:  소프트웨어 테스팅의 False Positive
date:   2018-12-20 01:52:00 +0900
categories: blog
---

False Positive는 이진 분류(binary classification) 실험 오류 중 하나로 통계 실험에서는 1종 오류(Type I Error)라고도 부르며 소프트웨어 테스팅에서도 쓰이는 용어다. 하지만 이진 분류와 통계 실험에 대한 배경지식이 없는 경우 소프트웨어 테스팅의 False Positive는 본래의 뜻과 반대의 의미로 잘 못 사용되곤 한다. 언어가 잘 못 사용되면 당연히 의사소통 장애로 이어진다.

<!--more-->

False Positive의 'positive'를 '긍정적'으로 이해하면 이런 문제가 발생한다. 고민할 것 없이 'false'는 '거짓'이고 테스트 케이스의 성공은 프로그래머 입장에서 긍정적인 결과이니, False Positive를 테스트 케이스가 버그를 잡아내지 못하고 성공하는 상황이라 생각하는 것이다. 안타깝게도 이것은 완전히 오해다.

False Positive의 'positive'는 '양성'을 뜻한다. 테스트 케이스에게 양성반응이란 단언(assertion)이 참이라는 가설(귀무가설, null hypothesis)을 기각하고 프로그래머에게 '이봐, 내가 뭔가 발견했어!'라고 알려주는 것이다. 쉽게 말해 테스트 케이스 실패다. 그러니까 소프트웨어 테스팅에서 False Positive는 대상 코드가 정상이지만 테스트 케이스가 실패하는 경우를 뜻한다. 다음 표를 확인하자.

||테스트 실패|테스트 성공|
|--|--|--|
|**비정상 코드**|참 양성(True Positive)|***거짓 음성(False Negative)***|
|**정상 코드**|***거짓 양성(False Positive)***|참 음성(True Negative)|

> 비슷하게 코드 분석기가 문제 없는 코드를 지적하는 것 역시 False Positive라고 부를 수 있다.

False Alarm이 False Positive와 동의어라는 것을 기억하면 False Negative(Type II Error)와의 혼동을 피할 수 있을 것이다. 테스트 케이스는 실패할 때 우리에게 경보(alarm)를 준다. 만약 이 경보가 거짓이라면 False Positive라는 용어로 상황을 표현할 수 있다.