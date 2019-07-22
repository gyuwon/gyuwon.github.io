---
layout: post
title:  현실 세상의 TDD
date:   2019-07-22 21:20:00 +0900
categories: blog
---

현실 세상에서 TDD는 가능하지 않다거나 효율이 너무 떨어져 쓸모없다는 주장은 지겹도록 들어왔지만 여전히 안타깝다. 나는 2018년에 이런 답답함을 조금이라도 풀어보고자 페이스북을 통해 5개월간 약 60명을 대상으로 'TDD 참관'이라는 행사를 진행했다.

<!--more-->

방식은 간단했다.

- 일주일에 한 번 퇴근시간 이후 사무실로 2명을 초청해 90분간 TDD를 사용한 개발을 보여준다.
- 나는 행사를 위해 별도로 뭔가 준비하지 않고 회사 업무를 그대로 평소처럼 수행한다.
- 참석자는 일주일 내에 페이스북에 후기를 작성해 공유한다. 분량과 내용에는 제한이 없다.

나는 당시 호텔 비즈니스를 위한 클라우드 서비스를 개발하고 있었고 메시지 중심 분산 아키텍처와 이벤트 소싱, CQRS 등의 설계 기법을 적용했다. 개발팀은 신입 2명, 2년차 주니어 1명, 나를 포함한 시니어 2명으로 구성되어 있었고 모두가 TDD를 사용했으며 나를 제외한 모두는 팀 합류 후 나에게 설계 기법과 TDD를 배웠다. 그 중 둘은 이후 함께 이직해 지금도 내 팀에서 배우는 중이다.

테스트 케이스는 약 3000개 정도에 95% 정도는 단위 테스트 케이스, 나머지는 기능 테스트 케이스였다. 지속 배치(Continuous Deployment) 환경이 만들어져 있었고 많은 경우 하루 30회 정도 새로운 코드가 배치되었다. 그러니까 빌드 서버는 하루 최대 9만 번 테스트 케이스를 실행했다. 이를 통해 프로그래머는 안정감을 얻었고 안정감을 바탕으로 개발 속도를 꾸준히 유지했다.

이런 개발 환경이 절대 상상속의 유니콘이 아니며 현장의 소프트웨어 개발에 기여함을 직접 보여주는 것이 TDD 참관 행사의 목적이었다.

다음은 참석자 분들의 소중한 후기다.

- [김동현 님의 후기](https://www.facebook.com/wplong11/posts/1575401559249900)
- [김인태 님의 후기](https://www.facebook.com/greatkit/posts/1996897607050116)
- [이진석 님의 후기](https://www.facebook.com/allieuslee/posts/1798328516855812)
- [유성민 님의 후기](https://www.facebook.com/graytraces/posts/1720063024779678)
- [심중섭 님의 후기](https://www.facebook.com/sosolo0307/posts/2199057373663198)
- [Bright Lee 님의 후기](https://www.facebook.com/benevbright/posts/1993883860675723)
- [Beren Ko 님의 후기](https://www.facebook.com/berentheonehanded/posts/1637971266302163)
- [김현민 님의 후기](https://www.facebook.com/evilskel/posts/2013698188674255)
- [권경덕 님의 후기](https://www.facebook.com/kkd927/posts/1529749463798112)
- [임진호 님의 후기](https://www.facebook.com/jino.yim.7/posts/2098404217100515)
- [이문기 님의 후기](https://www.facebook.com/wallahbaba/posts/1884150944993789)
- [조철현 님의 후기](https://www.facebook.com/ilovehojin/posts/1977820852248427)
- [오종인 님의 후기](https://www.facebook.com/ohjongin/posts/1903898602974924)
- [이희창 님의 후기](https://www.facebook.com/heechang.lee.7/posts/1796171007127936)
- [정원재 님의 후기](https://www.facebook.com/Anthony.Wonjay.Jung/posts/1838305199563914)
- [박찬엽 님의 후기](https://www.facebook.com/mrchypark/posts/1989334154439756)
- [최승길 님의 후기](https://www.facebook.com/seungkil/posts/1983339895050856)
- [이태현 님의 후기](https://www.facebook.com/TH.Jake.Lee/posts/1770108563067384)
- [이종호 님의 후기](https://www.facebook.com/jhleed/posts/2091653777829954)
- [박수석 님의 후기](https://www.facebook.com/chiyodadkr/posts/2072828179430813)
- [이형도 님의 후기](https://www.facebook.com/hyeongdo.lee/posts/10213634625434719)
- [박중운 님의 후기](https://www.facebook.com/pheadra4/posts/2135225323215319)
- [오종암 님의 후기](https://www.facebook.com/oh.am.3/posts/10216246782539221)
- [김종민 님의 후기](https://www.facebook.com/NoviceRambo/posts/2310144662538319)
- [오명운 님의 후기](https://www.facebook.com/hanmomhanda/posts/10217324283323245)
- [진나영 님의 후기](https://www.facebook.com/na.jin.961/posts/1918281881548572)
- [유호성 님의 후기](https://www.facebook.com/daniel.ryu.129/posts/10160690188215386)
- [최광훈 님의 후기](https://www.facebook.com/LightatmosphereChoi/posts/2149609051780840)
- [김근후 님의 후기](https://www.facebook.com/permalink.php?story_fbid=1125590484258265&id=100004219721899)
- [김태운 님의 후기](https://www.facebook.com/kofktu/posts/2148194435214816)
- [조은우 님의 후기](https://www.facebook.com/choeunwoo/posts/1936915879699282)
- [김태성 님의 후기](https://www.facebook.com/kimtaesung/posts/2174889389249877)
- [남궁성 님의 후기](https://www.facebook.com/permalink.php?story_fbid=2040952565954795&id=100001202130934)
- [문겸 님의 후기](https://www.facebook.com/permalink.php?story_fbid=278308943007876&id=100024863161084)
- [박상현 님의 후기](https://www.facebook.com/seanlab/posts/10217374452857910)
- [유승호 님의 후기](https://www.facebook.com/hahaysh/posts/2065233700178075)
- [이근배 님의 후기](https://www.facebook.com/geunbae.lee.5/posts/2372704879422871)
- [이경원 님의 후기](https://www.facebook.com/woniper/posts/2224929310911549)
- [장강현 님의 후기](https://www.facebook.com/permalink.php?story_fbid=602157406853414&id=100011774793180)
- [강동희 님의 후기](https://www.facebook.com/dk.kang.56/posts/1995289457194290)
- [이제연 님의 후기](https://www.facebook.com/permalink.php?story_fbid=2172714226284235&id=100006370366475)
- [김진억 님의 후기](https://www.facebook.com/permalink.php?story_fbid=10215050156518600&id=1571032606)
- [이동욱 님의 후기](https://www.facebook.com/jojoldu/posts/1697368843726205)
- [권도연 님의 후기](https://www.facebook.com/dorian.kwon/posts/2021410101255136)
- [성의현 님의 후기](https://www.facebook.com/permalink.php?story_fbid=2018937304859792&id=100002306273751)
- [송수경 님의 후기](https://www.facebook.com/happymint23/posts/1839011429485670)
- [신재원 님의 후기](https://www.facebook.com/babosonyun/posts/1827181944017933)
- [주영익 님의 후기](https://www.facebook.com/permalink.php?story_fbid=2068720179846625&id=100001259884599)
- [주현탁 님의 후기](https://www.facebook.com/momamene/posts/1117178815131239)
- [최지혜 님의 후기](https://www.facebook.com/permalink.php?story_fbid=2202359493412199&id=100009145081826)
- [이원우 님의 후기](https://www.facebook.com/wonury/posts/2195253237151438)
- [이명훈 님의 후기](https://www.facebook.com/kh2821/posts/1920594174643123)
- [박종복 님의 후기](https://www.facebook.com/permalink.php?story_fbid=1884534778289540&id=100001991079994)
- [김현제 님의 후기](https://www.facebook.com/permalink.php?story_fbid=1152930961556480&id=100005187601635)
- [배인진 님의 후기](https://www.facebook.com/permalink.php?story_fbid=507156826431957&id=100014129310194)
- [우여명 님의 후기](https://www.facebook.com/voyager.woo/posts/2364830853744116)
- [이지명 님의 후기](https://www.facebook.com/jeemyeonglee/posts/1855313971232156)
