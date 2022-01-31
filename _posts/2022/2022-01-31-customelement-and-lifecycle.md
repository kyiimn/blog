---
title:  "[webcomponent] customelement과 생명주기"
date: 2022-01-31 15:36:38 +0900
category:
 - webcomponent
tag:
 - customelement
 - lifecycle
---
## customElement
WebComponent를 구현하기 위한 요소 중 하나인 CustomElement는 완전히 새로운 태그를 생성하거나 이미 존재하는 태그를 확장하여 개발하도록 해주는 기능이다.<br>
ShadowDOM과 함께 사용하면 자식노드를 캡슐화 할 수도 있다.<br>
customElement를 구현할 때는 HTMLElement나, 특정태그의 클래스를 상속받아 구현한다. 이때 Element는 일련의 생명주기를 가지고 있는데, 이것을 잘 이해해야 제대로된 WebComponent를 구현할 수 있다.

## 기본 구조
customElement를 구현하게 되면 다음과 같은 모양을 띄게 된다.
```
class HelloElement extends HTMLElement {
    constructor() {
        super();
    }

    static get observedAttributes() {
        return ['width', 'height'];
    }

    connectedCallback() {
    }

    adoptCallback() {
    }

    attributeChangedCallback(name, oldval, newval) {
    }

    disconnectedCallback() {
    }
}
customElements.define('x-hello', HelloElement);
```
먼저 HTMLElement 등을 상속받아 실제 코드를 작성하고, `customElements.define()`에 태그명과 클래스를 넘겨주어 등록하면 새로운 태그가 생성된다.<br>
이때 태그명에는 반드시 `-`가 포함되어야 한다. 브라우저에서 기본으로 제공하는 태그와 customElement를 구분하기 위한 목적이다.<br>

## 생명주기
customElement는 다음과 같은 생명주기를 가진다.

### connectedCallback()
customElement가 생성되어 DOM에 추가된 후 호출된다. DOM에 추가가 된후에야 비로소 display와 관련된 속성들이 활성화되기 때문에 별도의 렌더링이 필요한 경우라면 이때 행해주면 된다.

### adoptCallback()
문서간에 이동된 경우 발생한다. 예를 들면 iframe이나 자식 window에서 node가 이동된 경우로 생각하면 된다.

### attributeChangedCallback(name, oldval, newval)
아래의 observedAttributes()에 정의된 속성에 대해 추가/변경/삭제가 되면 호출된다. 이때 넘겨온 파라메터값은 이름만으로도 유추가 가능할 것이다.

### observedAttributes()
위의 attributeChangedCallback()에서 사용할 속성을 정의하여, 배열형태로 리턴한다.

### disconnectedCallback()
DOM에서 삭제가 되었을 경우 발생한다.