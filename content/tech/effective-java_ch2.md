---
title: "Effective Java - 챕터 2 Item 7-8"
date: 2021-06-16T23:15:55-07:00
categories: ["tech"]
tags:
  [
    자바,
    자바 책 추천,
    공부,
    개발,
    코딩 독학,
    java,
    joshua-bloch,
    book,
    programming language,
  ]
cover: ""
draft: false
---

### \*Eliminate obsolete object references\*\*

자바는 garbage-collected language 이다. 편리할 수 있으나 unintentioanl object retention 는 언제든 생길 수 있다. 의도하지 않게 더이상 쓰지 않을 오브젝트들이 메모리를 차지하고 있는 것이다.

예를 들어 Stack 클래스의 pop method 가 다음과 같이 짜여져 있다고 해보자.

```java
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    reteurn elements[--size];
}
```

우리는 pop으로 집은 오브젝트가 더이상 스택에 있다고 간주하지 않는다. 이 스택을 이용하는 사람은 이 점을 잘 알고 있고, 따라서 stack 이 해야하는 모든 동작은 잘 수행한다.

elements 의 현재 size (index) 보다 큰 자리에 있는 오브젝트는 더이상 쓰이지 않는다는 것을 사용자는 인지하고 있지만, 우리 자바는 이를 알 수가 없다.

이에 대한 간단한 해결책은 더 이상 쓰지 않을 오브젝트를 *null*로 만드는 것이다.

```java
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;
}
```

하지만 오브젝트를 직접 null 로 지정해서 메모리에서 지우는 일은 흔한 상황은 아니다. 아니어야만 하도록 디자인된다면 더욱 좋다.

대부분의 경우 reference를 가리키는 variable이 사용되고 해당 variable이 속한 scope를 벗어나면 자연스럽게 가비지 콜렉터가 해결한다.

일반적으로, 앞서 스택의 예시와 같이 클래스가 자신만의 메모리를 관리할 때, 개발자는 메모리 최적화에 신경써야 한다.

&nbsp;

### **Avoid finalizers and cleaners**

finalizers 와 cleaners 의 가장 큰 단점은 그들이 즉시 수행된다는 보장이 없다는 것이다. Garbage collection algorithm implementation 는 다양하기 때문에, 로컬에서의 기능이 클라이언트에게도 똑같이 기능한다고 기대할 수 없다.

이들이 유용한 몇가지 상황이 있다. close method 를 호출하지 않았을 때 보험처럼 작용하여 늦게라도 자원을 확보할 수 있다.
