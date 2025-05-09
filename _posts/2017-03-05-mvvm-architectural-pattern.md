---
layout: post
title:  MVVM 아키텍처 패턴
date:   2017-03-05 15:22:00 +0900
categories: blog
permalink: /blog/mvvm-architectural-pattern/
redirect_from:
  - /blog/2017/03/05/mvvm-architectural-pattern.html
---

MVVM(Model/View/ViewModel) 패턴은 UI를 가지는 응용프로그램을 위한 [아키텍처 패턴(architectural pattern)](https://en.wikipedia.org/wiki/Architectural_pattern)이다. MVVM 패턴은 MVC(Model/View/Controller) 패턴의 변형으로 뷰의 추상화를 만드는 것이 핵심이다. 뷰의 추상화는 재사용할 수 있고(reusable) 테스트하기 쉽다(testable). 뷰의 추상화를 통해 응용프로그램 구조는 단순해지고, 이상적으로, 시각 디자인과 표현 논리를 독립적으로 구현할 수 있다.

<!--more-->

모델(Model)과 뷰(View)는 MVC 패턴의 그것들과 동일하다. 모델은 데이터, 비즈니스 논리, 서비스 클라이언트 등으로 구성된다. 뷰는 선언적으로 구성된 UI 요소들을 의미한다. MVVM 패턴을 학습하며 가장 집중해야할 부분은 뷰모델(ViewModel)이다. 아직까지 .NET 언어들과 XAML(eXtensible Application Markup Language, /zæməl/), Blend<sup><a href="#comment-1">[1]</a></sup>를 제외하고 MVVM 패턴에 대해 얘기하는 것은 쉽지 않은 일이다. 하지만 나는 이 글에서 특정 플랫폼 언급을 가능하면 자제하고 뷰모델을 중심으로 MVVM 패턴의, 구현이 아니라, 개념적 특징들과 가치를 설명하려 한다.

추상화(abstraction)는 MVVM 패턴의 뷰모델을 설명하며 빠뜨릴 수 없는 용어이다. 뷰모델이란 이름은 '뷰의 모형'을 뜻한다. 즉, 추상화된 뷰라는 의미다. 구동되는 응용프로그램에서 뷰는 UI 플랫폼이 제공하는 컨트롤들을 조합해 사용자에게 시각적으로 접근하지만 뷰모델은 이러한 뷰를 플랫폼 독립적인 공간에서 추상화한다. 뷰모델은 뷰를 추상화하기 위해 추상화된 뷰 상태(ViewState)를 유지한다. 예를 들어 뷰모델은 읽기와 쓰기가 가능한 문자열 속성을 통해 텍스트 입력기 컨트롤을 추상화한다. 데이터 목록을 보여주는 컨트롤에 대해서는 각 요소의 뷰 상태가 들어있는 컬렉션이 사용된다.

모델이 제공하는 정보가 사용자에게 전달될 때, 혹은 그 반대의 경우 원본값이 그대로 사용되기도 하지만 그렇지 않은 경우도 있다. 모델이 표준시 기준 날짜 정보를 제공할 때 이것을 그대로 노출하는 것은 사용자에게 달갑지 않은 일이다. 지역 시간대 및 현지 언어가 적용되거나 '어제'와 같은 상대적 표현으로 변환된다면 사용자는 더 나은 경험을 하게된다. 이런 변환 작업을 위해 뷰모델은 값 변환기(ValueConverters)를 가진다.

뷰는 자신이 가진 상태를 사용자에게 표현할 뿐 아니라 사용자가 응용프로그램에 명령을 내릴 수단을 제공한다. 뷰모델은 이런 기능을 추상화하기 위해 명령(Commands)을 가진다. 명령을 통해 사용자는 모델의 행위를 실행할 수 있다.

뷰 상태, 값 변환기, 명령을 통해 뷰모델은 추상화된 뷰가 된다. 따라서 뷰모델은 개념적으로 사용자와 소통한다. 개념적 공간에서 사용자는 뷰모델의 속성을 통해 정보를 입력하고 뷰모델의 명령을 실행시키며 뷰모델은 자신의 속성을 갱신해 명령 실행 결과와 응용프로그램 상태 변화를 사용자에게 표현한다. 이메일과 암호를 이용한 로그인 화면을 떠올려보자. 이 화면을 추상화하는 뷰모델은 이메일 입력기와 암호 입력기에 대응하는 쓰기 가능한 문자열 속성 두 개와 로그인 버튼에 대응하는 명령을 가질 것이다. 만약 서비스(모델)가 로그인 실패 코드를 반환한다면 이 코드는 값 변환기에 의해 사용자 친화적인 메시지로 변환되어 읽기 전용 문자열 속성을 통해 출력된다.

이제 추상화된 뷰와 물리적인 뷰를 연결해 줄 수단이 필요하다. MVC 패턴에 익숙한 프로그래머라면 컨트롤러(Controller)가 뷰와 모델 사이의 작업흐름을 제어하는 모습을 떠올릴 것이다. MVVM 패턴에서는 작업흐름 제어보다는 뷰와 뷰모델의 상태를 동기화해 줄 구성요소가 필요한데 데이터 바인딩(data binding)이 그것이다. MVVM 패턴은 완성도 높은 데이터 바인딩 기반구조에 의존한다.<sup><a href="#comment-2">[2]</a></sup> 데이터 바인딩으로 인해 뷰모델의 상태가 변경되면 뷰의 상태가 함께 변경됨, 그리고 그 역이 보장된다. 데이터 바인딩은 MVVM 패턴 외에도 유용하게 사용된다. 하지만 MVVM 패턴은 데이터 바인딩에 의존하므로 데이터 바인딩이나 그 대체자가 없다면 구현될 수 없다.

추상화는 구체화에 대한 지식을 가지지 않기 때문에 뷰모델은 뷰에 대해 알지 못한다.<sup><a href="#comment-3">[3]</a></sup> 이것은 뷰모델이 플랫폼 독립적이고 다양한 플랫폼에서 재사용될 수 있음을 의미한다. 응용프로그램 플랫폼에 종속된 코드는 생명주기 제약이 많지만 뷰모델 인스턴스는 언제든지 즉시 생성될 수 있다. 프로그래머는 단위 테스트 케이스를 통해 뷰모델에 구현된 응용프로그램 요구사항을 이른 시점에 검증할 수 있고 테스트 주도 개발(test-driven development) 도입도 쉽다.

재사용성과 테스트 용이성 외에 뷰모델이 추상화된 뷰로서 가지는 커다란 장점은 응용프로그램의 많은 부분이 뷰 독립적으로 설명되고 구현된다는 것이다. 응용프로그램은 복잡한 컨트롤러 논리 없이 단순한 구조를 유지할 수 있다. 나는 MVVM 패턴을 학습하고 현장에 적용하며 이것이 MVVM 패턴이 가지는 가장 큰 가치라는 것을 깨달았다.<sup><a href="#comment-4">[4]</a></sup> MVVM 패턴을 시도하더라도 이 가치를 취하지 못한다면 재사용성과 테스트 용이성 조차 온전히 얻을 수 없다.

일반적으로 다른 패턴에 비해 아키텍처 패턴은 이론 그 이상으로 구현법이 장황하고 어렵다. MVVM 패턴은 Microsoft UI 기술의 풍부한 지원이 있었기에 만들어질 수 있었다. 반대로 생각해보면 비 Microsoft 기술에서 MVVM 패턴을 구현하기 위해서는 프로그래머들이 직접 준비해야할 것들이 많다는 뜻이 된다. 하지만 구현법에만 몰입해 패턴 본질의 가치를 등한시한다면 구현하는 과정이 아무리 멋지더라도 그 수확물은 충분하지 않을 것이다.

나는 더 많은 프로그래머들이 MVVM 패턴의 혜택을 누리기 바란다. 마지막으로 MVVM 아키텍처 패턴의 이해와 활용을 도와줄 글 몇 개를 소개하며 마친다.

- [Introduction to Model/View/ViewModel pattern for building WPF apps](https://blogs.msdn.microsoft.com/johngossman/2005/10/08/introduction-to-modelviewviewmodel-pattern-for-building-wpf-apps/)
- [100 Model/View/ViewModels of Mt. Fuji](https://blogs.msdn.microsoft.com/johngossman/2005/10/09/100-modelviewviewmodels-of-mt-fuji/)
- [Patterns - WPF Apps With The Model-View-ViewModel Design Pattern](https://msdn.microsoft.com/en-us/magazine/dd419663.aspx)

---

1. <span id="comment-1">'Blendability'라는 용어가 있을 정도로 MVVM 세상에서 Blend는 중요하다.</span>

1. <span id="comment-2">그렇기 때문에 한동안 MVVM 패턴은 WPF(Windows Presentation Foundation)와 Silverlight의 전유물이었다. 하지만 데이터 바인딩이 지원되지 않는 플랫폼이라면 컨트롤러가 뷰와 뷰모델의 상태 동기화를 담당할 수 있다. John Gossman은 이미 2005년에 Model/View/Controller/ViewModel 패턴을 언급했고 나는 이 패턴을 Windows Forms에서 사용한 적이 있다.</span>

1. <span id="comment-3">'Zero code-behind'에 집착하면 뷰모델이 뷰에 대한 간접적 지식을 갖게되는 경우가 있다. 레이아웃 크기가 뷰모델 속성으로 바인딩되는 것 따위가 그렇다. 이것은 나쁜 신호다. 방치하면 뷰모델과 뷰는 간접적 뒤엉킴에 시달리게 될 것이다. 뷰에 코드를 허용하고 관심사를 분리하는 데에 집중하라.</span>

1. <span id="comment-4">나는 MVVM 패턴을 적용했던 첫 프로젝트에서 최상위 뷰모델의 이름을 흔히 사용하는 'MainViewModel'이 아니라 'ApplicationModel'이라 지었다. 이 이름에 합당한 역할을 유지하는 것은 어려운 일이지만 그 이상의 이득이 있다. 나는 MVVM 패턴을 사용하는 일부 프로그래머들이 이 가치를 깨닫지 못한다고 생각한다. 특히 비 Microsoft 기술을 사용하는 프로그래머들의 경우는 심각하다. 그들은 'ViewModel' 접미사와 데이터 바인딩을 컨트롤러 하나를 더 만드는 일에 사용하고 있다.</span>
