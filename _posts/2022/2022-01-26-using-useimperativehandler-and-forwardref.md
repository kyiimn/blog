---
title:  "[react] useImperativeHandler와 forwardRef 사용하기"
date: 2022-01-26 14:24:46 +0900
category:
 - react
tag: 
 - useImperativeHandler
 - forwardRef
 - react
 - hook
---
## ref
[react 공식 문서](https://ko.reactjs.org/docs/refs-and-the-dom.html)에 의하면 **ref**는 react의 일반적인 데이터 플로우를 벗어나 **직접 자식(react 객체나 dom element)을 수정**해야 할 경우를 위해 제공된다고 하는데, 보통 다음과 같은 방법으로 사용한다.
```
const ExamComp = () => {
  const ref = useRef();
  const handler = () => {
    ... 중략 ...
    ref.current.focus();
  };
  ... 중략 ...

  return <>
    ... 중략 ...
    <input type="text" ref={ref} ...>
  </>;
}
export default ExamComp;
```
위 예제는 간단히 component내에서 자식 dom element에 접근하는 것을 나타냈는데, 그렇다면 component 밖에서는 어떻게 해야 할까?<br>
만약 class형 component나 아니면 일반 [webcomponent](https://developer.mozilla.org/ko/docs/Web/Web_Components)라면 method나 property를 손쉽게 정의하여 사용할 수 있겠지만, 함수형 component는 그렇지 않다.

## useImperativeHandler와 forwardRef
그래서 [useImperativeHandler](https://ko.reactjs.org/docs/hooks-reference.html#useimperativehandle)라는 hook이 존재한다. 그리고 이것은 [forwardRef](https://ko.reactjs.org/docs/react-api.html#reactforwardref)와 같이 사용해야 한다~~(물론 다른 대안도 있긴하다)~~.
<br>
보통 다음과 같이 정의하고
```
const ExamComp = forwardRef((props, ref) => {
  const inputRef = useRef();
  useimperativehandle(ref, () => ({
    methodA: () => {
      inputRef.current.focus();
    },
    methodB: () => {
      inputRef.current.blur();
    },
  }));
  return <>
    ... 중략 ...
    <input type="text" ref={inputRef} ...>
  </>;
});
export default ExamComp;
```
다음과 같이 사용한다.
```
const App = () => {
  const ref = useRef();
  const handler = () => {
    ... 중략 ...
    ref.current.methodA();
  }
  ... 중략 ...

  return <>
    ... 중략 ...
    <ExamComp ref={ref} ...>
  </>;
}
export default App;
```
