---
layout: post
title: "WEEK2 프로토타입"
categories: Challenge-Week2
---

## 클래스와 JavaScript

자바스크립는 ES6에서 클래스가 도입되었으나, 아래에도 설명했지만 프로토타입 기반의 언어이기 때문에 우리가 흔히 사용하는 클래스(ex. Java에서)와 동작하는 느낌이 다르다.

```jsx
function Greet(name) {
  this.name = name;
}

Greet.prototype.hello = function () {
  console.log(`hello ${name}`);
};
```

## 프로토타입과 JavaScript

부캠을 통해 깊게 자바스크립트로 미션을 진행하기 전에, 나는 자바 기반의 학습을 진행했었다. 자바는 클래스 기반의 객체지향 프로그래밍 언어인데, 이와 달리 자바스크립트는 ‘문법적 설탕’처럼 클래스를 만들어 사용한다고 들었다. 그래서 객체지향 프로그래밍인줄도 몰랐다… (이건 내가 바보임)

명색이 프로토타입 기반 객체지향인데, 내가 이 언어를 사용하면서 프로토타입에 대해 모르면 그냥 이 언어 자체를 아무것도 모르고 사용하는 꼴이 되는 것 같아 가볍게 이해하고 넘어가고자 한다. (아직 많이 쓰지 못해서 개념 자체도 미숙하지만 자주 사용하면서 많은 이슈들을 해결하면 잘 와닿게 되지 않을까..?)

### 문법적 설탕이라고 부르는 간단한 이유

```jsx
// 위에서 작성한 예시를 단지 클래스 형태(ES6)로 바꾼 코드이다.
class Greet(name) {
	constructor(name) {
		this.name = name;
	}
	hello() {
		console.log(`hello ${name}`);
	}
}
```

클래스로 변경만 한 것이지, 결과나 동작방식은 동일하다. 그렇기 때문에 단지 생성자-프로토타입 형태를 숨기기 위한 ‘문법적 설탕’이라 부르는 것이다!

### 오 근데 편의 문법이 아니래요,,

1. 클래스에 **특수 내부 프로퍼티** `[[IsClassConstructor]]: true` 가 붙습니다.
2. 클래스에 정의된(prototype 프로퍼티에 추가된) **메서드는 열거X**

   `enumerable: false`

3. 클래스는 **엄격 모드**로 실행

### 그래서 다른 클래스 기반 객체지향 프로그래밍과 다른 점

당장의 Java만 예로 들어도 클래스는 객체에 대한 속성과 기능?을 갖는 틀이며 인스턴스를 생성하게 되면 하나의 새로운 객체이기 때문에 서로 개별적이며 **참조하지 않는다**. 그러나 프로토타입 기반의 JS에서는 ‘클래스-인스턴스’ 개념이 아닌 ‘객체’ 개념만 갖는다. 이 말은 생성된 객체가 함수의 프로토타입 객체(부모?)와 연결되어 있다는 말이다.

## 간단히 정리해보는 프로그래밍 용어

- `프로퍼티` = 해당 Object의 특징
  - 인스턴스 / 정적 프로퍼티 2가지로 나뉨
  - 인스턴스는 해당 객체 인스턴스의 데이터, 정적은 모든 객체에 공유되는 데이터
  - ‘이름, 값’을 한 쌍으로 가짐
  - (헷갈렸던게 있는데) 프로퍼티를 참조하는 방식은 2가지
    ex. 객체명.프로퍼티명 / 객체명[프로퍼티명]
- `필드` = 데이터 멤버

## 프로토타입

= 객체의 특성을 다른 객체로 **상속**을 가능케 하는 메커니즘

= 자신의 부모 역할을 담당하는 객체

(근데 엄밀히 말하면 상속이라는 단어도 적절치 않다고 한다)

(장점) 아마도 *메모리 자원을 효율적*으로 사용할 수 있다는 거..?

⇒ 공통된 특성을 프로토타입 객체에 모아서 따로 생성하지 않고 참조하게 만드니까

(단점) 링크를 통해 프로퍼티가 동적으로 수정되므로 Link가 길어지면 다른 쪽에서 부수 효과(이 객체를 건드림으로써)가 발생할 수 있다.

- `[[Prototype]]` = **Prototype Link** = 자신을 만들어낸 객체의 원형
  이 **내부 슬롯**(JS 엔진의 내부 로직)을 가지며, 상속을 구현하는 프로토타입 객체를 가리킴
  ⇒ 그러나 이 내부 슬롯에 접근이 불가능함!
  (because of 유지 for 프로토타입 체인 단방향)
  ++ 함수 객체는 Prototype 프로퍼티를 가진다.
  (but 일반 객체 ex. const obj = {} 는 prototype 프로퍼티가 없다
  추가적으로 화살표 함수와 메서드 축약 표현 함수도 X …)
- 프로토타입과 생성자 함수는 늘 함께 존재한다

### 간단히 알아보는 메서드

- `Object.create()` 는 지정된 프로토타입 객체와 **연결된 빈 객체를 반환**한다.
- `__proto__` 프로퍼티를 통해 자신의 부모 객체를 가리킨다.
  - but 같은 의미를 가진 `Object.getPrototypeOf()`의 사용을 권장함

### 객체의 프로퍼티 존재여부 확인 과정

1. 현재 객체에서 프로퍼티 존재 x ⇒ 연결된 프로토타입 객체로 이동 후 탐색
2. 그 객체에도 프로퍼티 존재 X ⇒ 프로퍼티와 연결된 프로토타입 객체로 이동 후 탐색
3. 1~2 번의 **연쇄적인 흐름** 속 프로퍼티를 발견 ⇒ 그 값을 반환

   “연쇄적인 흐름” = **Prototype Chain**

4. but x ⇒ 탐색 종료 후 undefined 반환

## 읽어보면좋을글

https://velog.io/@tami/TIL-3-%EB%85%B8%EB%8B%B5-prototype-%EC%82%BC%ED%98%95%EC%A0%9C%EC%99%80%EA%B3%BC-%EB%A7%9E%EC%9E%A5%EB%9C%A8%EA%B8%B0
https://ko.javascript.info/private-protected-properties-methods

## 참고

https://tecoble.techcourse.co.kr/post/2021-06-14-prototype/
https://blog.naver.com/magnking/220966405605
https://www.howdy-mj.me/javascript/prototype-and-proto
https://velog.io/@soom/Prototype
https://ko.javascript.info/class