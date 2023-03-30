---
layout: post
title: private 생성자나 열거 타입으로 싱글턴임을 보증하라
subtitle: private 생성자나 열거 타입으로 싱글턴임을 보증하라
categories: java
tags: [java]
---

## 싱글턴이란?

인스턴스를 오직 하나만 생성할 수 있는 클래스입니다.

하지만 무 상태 객체 방식으로 하면 클라이언트를 테스트하기가 어려워 질 수도 있습니다.

인터페이스로 정의 한 다음 인터페이스를 구현해서 만든 싱글턴이 아니라면 MOCK을 이용하여 가짜 구현이 힘들어집니다. 

 싱글턴을 만드는 방식은 세가지 방법이 있습니다.

### Public static final 필드 방식의 싱글턴

```jsx
public class Chan {
	public static final Chan INSTANCE = new Chan();
	private Chan() {...}
}
```

private 생성자는 INSTANCE를 초기화 할 때 한번만 호출됩니다.

→ public, protected와 같은 생성자가 없기 때문에 클래스가 초기화 될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임을 보장합니다.

하지만 클라이언트에서는 손 쓸 방법이 없습니다. 

장점은 싱글턴 API의 명백함과 간결함이며

단점은 클라이언트가 사용하지 않더라도 항상 생성되므로 메모리 낭비가 됩니다.

### 정적 팩토리 방식의 싱글턴

```java
public class Chan{
	private static final Chan INSTANCE = new Chan();
	private Chan() { ... }
	public static Chan getInstance() { return INSTANCE; }
}
```

INSTANCE를 직접 참조 하지 않고 getinstance를 통해서 전달하는 방법입니다.

물론 예외도 똑같이 일어나지만 장점이 더욱 많습니다. 

1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있습니다.
2. 원한다면 정적 팩토리를 제네릭 싱글턴 팩토리로 변경이 가능해집니다.
3. 정적 팩토리의 메서드 참조를 공급자로 사용할 수 있습니다. 
ex) Chan::getInstance → Supplier<Chan>

→ 이러한 방식이 필요없다면 Public static final 방식으로 사용해도 무방합니다!

단점은 여전히 사용하지 않더라도 인스턴스가 생성됩니다.

### 그러면 직렬화 하는건 어떨까?

```java
private Object readResolve() {
	return INSTANCE;
}
```

모든 인스턴스 필드를 일시적이라고 선언하고 readResolve 메서드를 제공해야합니다. 이렇게 하지 않는다면 직렬화 된 인스턴스를 역직렬화 할 때마다 새로운 인스턴스가 만들어 집니다. 

### 열거형 타입을 선언

```java
public enum Chan{
	INSTANCE;

	public void chanBuilding() {}
}
```

원소가 하나인 열거 타입을 선언을 하면서 추가 노력없이 직렬화, 간결함, 리플렉션 공격 전부 막아줍니다. 조금 생소 할 수 있으나 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법입니다.

하지만. 상속이 필요하다면.. enum이라서 사용할 수 없습니다.