---
layout: post
title: 생성자에 매개변수가 많다면 빌더를 고려해라
subtitle: 생성자에 매개변수가 많다면 빌더를 고려해라
categories: blog
tags: [java]
---

평소에도 lombok의 builder annotation 을 이용하여 처리 한 경험이 있는데 확실히 매개변수가 많을 수록 코드가 clean 하고 가독성이 높아지는 것 같다. 

## 생성자가 많으면 대응하기가 어렵다.

```jsx
public class user{
	private String email;
	private String pw;
	private String nm;
	private String nickname;
	private String phone;
	private String birth;
	...
}

User user = new User(email, pw, nm, nickname, phone, birth, ...)
```

현재 코드를 보면 클래스 필드의 값들이 늘어 날 수록 생성자를 사용하기가 어려워진다.

(값이 무엇인지… , 매개 변수가 정확하게 맞나…?, 순서 정확하게 넣었나…?)

## 자바빈즈 패턴

그래서 점층적 생성자 패턴이 있는데.. 생성 후 set으로 하나씩 처리해주는 방식이다.

확실하게 코드는 가독성이 올라왔지만 심각한 문제점이 있다.

1. 객체를 하나 만들려면 메서드를 여러개 만들어야 한다.
2. 객체가 완전히 생성되기 전까지는 일관성이 무너져있다.
    
    → 해당 이유로 버그를 심은 코드와 버그가 존재 할 시 디버깅이 쉽지가 않다.
    

이것을 보완하려고 freeze라는 메서드가 존재했지만. 어려워서 포기

## 빌더 패턴

```jsx
User user = User.builder().
						email(email).
						pw(pw).
						nm(name).
						nickname(nickname).
						...
						build();
```

빌더 패턴은 메서드를 자신을 반환하기 때문에 연쇄적으로 호출이 가능해져서 일관성을 뛰게 된다.

그리고 빌더로 매개변수 따라 다른 객체를 만들 수 있기 때문에 유연해진다.

하지만. 객체를 만들려면 빌더를 만들어야 되는 성능문제로 매개 변수가 적다면 빌더 패턴을 한번 고려해볼 필요가 있다.