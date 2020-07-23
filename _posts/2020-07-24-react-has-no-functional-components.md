---
layout: post
title: React에는 '함수형 컴포넌트'가 없다
date: 2020-07-24 12:00:00 +0900
categories: blog
---

이 글이 작성되는 시점에 React에는 '함수형(functional) 컴포넌트'가 없다. 내가 미쳤다고? 훗. 바로 증명하겠다. 지금 당장 [공식 사이트](https://reactjs.org)에 들어가서 'functional component'라는 말이 나오는지 눈씻고 찾아보라. [한국어 사이트](https://ko.reactjs.org/)에 들어가서 '함수형 컴포넌트'라는 말도 찾아보라. 검색 기능도 제공하니 몇 초면 내가 제정신이란 걸 알게 될거다. React는 함수형 컴포넌트란 걸 제공하지 않는다.

하지만 넓은 인터넷 세상엔 React와 관련해 '함수형 컴포넌트' 또는 'functional component'라는 말이 넘친다. 국내에 발행된 각종 서적 역시 마찬가지다. 어떻게 된거야?

<!--more-->

별다른 수식 없이 함수형 프로그래밍이라 하면 보통 순수(pure) 함수를 주로 다루는 프로그래밍 기법을 뜻한다. C는 함수를 이용해 논리를 표현하지만 순수하지 않는 함수도 많이 사용하기 때문에 이 언어를 함수형 프로그래밍이란 말과 잘 연결짓지 않는다. 프로그래밍 세상의 순수 함수란 수학 함수와 유사한 함수를 뜻하며 다음 두 조건을 만족하면 순수하다고 표현한다.

- 같은 입력에 대해 출력이 항상 같다.
- 부작용(side effect)이 없다.

부작용은 반환값 외의 출력을 뜻한다. 예를 들어 다음 코드의 `double` 함수는 순수하고 `loggingDouble` 함수는 순수하지 않다.

```js
function double(x) {
  const y = x * 2;
  return y;
}

function loggingDouble(x) {
  const y = x * 2;
  console.log(y); // Side effect!
  return y;
}
```

둘 모두 같은 `x` 값에 대해 항상 같은 값을 반환하지만 `loggingDouble` 함수는 반환값 외에 `console.log` 함수를 통해 계산 값을 출력한다. 부작용이다.

함수 중에 순수 함수는, 그리고 순수 함수만이, 참조 투명성(referential transparency)을 가진다. 참조 투명성이란 어떤 식을 그 식을 풀이한 값으로 대체해도 프로그램의 동작이 변하지 않는 성질을 뜻한다. `double(3)`을 `6`으로 대체해도 프로그램의 동작은 변하지 않는다.

```js
console.log(double(3));
// 6

console.log(6);
// 6
```

하지만 `loggingDouble(3)`을 반환값인 `6`으로 대체하면 콘솔에 출력되는 값이 달라진다. 프로그램의 동작이 변하는 것이다.

```js
console.log(loggingDouble(3));
// 6
// 6

console.log(6);
// 6
```

`loggingDouble` 함수는 순수하지 않기 때문에 참조 투명하지 않다. 프로그래밍 세상에서 `loggingDouble`은 여전히 함수지만 함수형 프로그래밍에서는 `loggingDouble`이 아닌 순수한 `double` 함수를 사용한다.

> 함수의 순수함과 참조 투명성이 어떤 장점이 갖는지는 이 글의 주제를 벗어나므로 다루지 않는다.

다시 주제인 React 얘기로 돌아가자. 공식 문서에 '함수형 컴포넌트'라는 말은 없지만 '함수 컴포넌트(function component)'는 설명한다. 함수가 순수하거나 순수하지 않을 수 있는 것과 마찬가지로 함수 컴포넌트는 구현에 따라 순수하거나 순수하지 않을 수 있다.

순수하지 않은 함수 컴포넌트의 한가지 예시로 [`useEffect`](https://reactjs.org/docs/hooks-effect.html) 사용이 있다. `useEffect`는 이름에 드러나듯 함수 컴포넌트가 부작용을 이용하는 도구다.

```jsx
function ImpureFunctionComponent() {
  const message = 'hello world';
  useEffect(() => { document.title = message; }); // Side effect!
  return (<h1>{message}</h1>);
}
```

`ImpureFunctionComponent` 호출을 이 함수의 반환값으로 대체하면 부작용이 사라져 문서 제목이 변경되지 않는다. 참조 투명하지 않기 때문이다. 다음 두 코드 조각의 동작은 다르다.

```jsx
<ImpureFunctionComponent />
```

```jsx
(<h1>hello world</h1>)
```

결론짓자면 함수 컴포넌트는 참조 투명할 수도, 아닐 수도 있기 때문에 '함수형' 수식어를 붙이는 건 오해를 일으킬 수 있다. 아마 그래서 React는 'functional component' 대신 'function component'라고 이름 지었을 것이다. 함수 컴포넌트를 함수형 컴포넌트라고 부르는 사람들은 최소한 부주의하고 무책임하거나 아마도 자신이 다루는 기술을 충분히 이해하지 못하고 있는 것이다.

내가 React를 공부한 것은 채 한달도 되지 않았기 때문에 오래전 언젠가는 공식 사이트 조차 잘 못 표현하고 있었는지도 모르겠다. 안타깝지만 나에게 그런 과거까지 조사할 열정 따위 없다.
