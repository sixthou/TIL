# SOLID

이전에 SOLID에 대해 간단하게 정리했던적이 있으나 이후에도 동일한 똑같은 내용을 다시 검색하며 완벽한 숙지를 이뤄내지 못하였고, 어떤 개념을 공부하던 SOLID를 밑바탕으로 하고 있기 때문에 더이상 개념의 혼돈이 없도록 정리를 해보고자 한다.

---

S : Single responsibility / 단일책임

O : Open-closed / 개방 폐쇄

L : Liskov substitution / 리스코프 치환

I : interface segregation / 인터페이스 분리

D : Dependency Inversion / 의존성 역전

---

## 단일 책임 원칙 - Single Responsibility Principle

> 변화에 있어 하나의 이유만 있어야한다. 즉 하나의 역할만 수행해야한다.
- 한 클래스는 단 한 가지의 변경 이유만을 가져야 한다.
- 하나의 모듈은 하나의, 오직 하나의 액터에 대해서만 책임을 져야한다.
- 컴포넌트를 변경하는 이유는 오직 하나뿐이어야 한다.
> 

책임을 `변경을 위한 이유`로 정의한다. 어떤 클래스가 하나 이상의 책임을 가지고 있으면 변경의 이유도 하나 이상이 될 것이다. 이럴경우 책임들 간의 관계가 맺어져 변경 요구사항이 들어올때 수정의 어려움과 심지어는 불가능한 경우도 발생 할 수 있다.

### 예제를 통해서

Computation Geometry Application(CGA): Rectangle 클래스의 넓이를 계산 역할

Graphical Application(GA) : 화면상에 그리는 역할

![Untitled](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*7np8DmvPn0ZkP71g-WpzOA.png)

문제점 

- 두개의 서로 다른 Application이 Rectangle클래스를 사용하고 있다.
- Retangle 클래스는 두개의 책임을 갖고 있다.
    - 넓이 계산
    - 화면에 그리기
- CGA는 필요하지 않는 GUI 관련 모듈을 포함해야 한다.
- 하나의 Application에서 Rectangle을 변경했을시 다른 Application에 영향을 끼친다.

![Untitled](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*RFLhbZjo29SeEwOETTpqIA.png)

- 완전희 독립된 클래스에 각각의 책임을 분리함
- 넓이를 계산하는 책임은 Geometric Rectangle이 갖는다. GUI 관련 기능이 CGA에 영향을 미치지 않는다.

> ❕의미없이 책미을 분리하면 안되고 `결합도`와 `응집도` 를 생각하며 분리해야 한다.
> 

---

## 개방 폐쇄 원칙 - Open Closed Principle

> 확장에는 열려있고, 변경에는 닫혀 있어야 한다.
> 

OCP는 시스템을 기존의 코드를 변경하지 않으면서, 기능을 추가할 수 있도록 설계해 ***확장하기 쉬운 동시에 변경으로 인해 시스템이 많은 영향을 받지 않도록 하는 것***. 따라서 시스템을 컴포넌트 단위로 분리하고, 저수준 컴포넌트에서 발생한 변경에서 고수준 컴포넌트를 보호할 수 있는 형태의 의존성 계층 구조가 만들어져야 한다.

[확장]
- 모듈의 확장성 보장
- 변경 사항 발생시 유연하게 코드 추가
[변경]
- 객체의 직접적인 수정은 제한
- 객체를 직접 수정하지 않고도 변경사항을 적용할 수 있도록 설계

### 코드를 통해서

**OCP를 준수하지 못한경우**

계산기를 만들어 보자. Operation 인터페이스를 생성하고 각 연산자마다 기능을 구현할 생각이다.

```java
public interface Operation { }
```

```java
public class Add implements Operation {
    private double left;
    private double right;
    private double result = 0.0;
    //getter, setter
```

```java
public class Sub implements Operation {
    private double left;
    private double right;
    private double result = 0.0;
    //getter, setter
```

계산기를 구현해보자

```java
public class Calculator {
    public void calculate(Operation operation) {
        if (operation instanceof Add) {
            Add add = (Add) operation;
            add.setResult(add.getLeft() + add.getRight());
        } else if (operation instanceof Sub) {
            Sub sub = (Sub) operation;
            sub.setResult(sub.getLeft() - sub.getRight());
        }
    }
```

위와 같이 구현했다고 하면 연산자가 추가될때마다 아래에 if-else 문이 계속 추가 될 것이다. 이거는 OCP를 고려한 설계가 아니다.

**OCP를 준수한 경우**

```java
public interface Operation { 
	void perform();
}
```

```java
public class Add implements Operation {
    private double left;
    private double right;
    private double result = 0.0;

    @Override
    public void perform() {
        result = left + right;
    }
```

```java
public class Sub implements Operation {
    private double left;
    private double right;
    private double result = 0.0;

    @Override
    public void perform() {
        if (right != 0) {
            result = left / right;
        }
    }
```

```java
public class Calculator {
    public void calculate(Operation operation) {
        operation.perform();
    }
```

Operation 인터페이스에 perform을 정의하고 각 구현체에서 이를 구현한다면 Calculator에서는 이전과는 다르게 operation.perform();만 수행하면된다. 연산자가 추가된다고 하면 Operation을 구현하는 새로운 구현체만 추가하면 되니 확장에는 열려있고, 기존의 코드는 수정하지 않아도 되기 때문에 변경에는 닫혀있는 설계가 완성 되었다.

---

## 리스코프 치환 원칙 - Liskov substitution

> ***Subtypes must be substitutable for their base types. 
- Robert C. Martin***
서브 타입은 기본 유형을 대치할 수 있어야 한다.
> 

> ***If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T, the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T. - Barbara Liskov***
> 
> 
> 각각의 S 타입 객체 o1에 대해 T 타입의 객체 o2가 있고, T를 기반으로 정의된 모든 프로그램 P에 대해 o1이 o2로 대체될 때 P의 동작이 변경되지 않는다면, S는 T의 하위 타입입니다.
> 

올바른 상속 관계릐 특징을 정의하는 원칙이다. 대체할 수 있다는 것은 **하위 클래스는 상위 클래스에서 가능한 행위의 수행을 보장**한다는 의미이다. `다형성`을 얘기하는 것이다. 

## 코드를 통해서

**LSP를 위반한 경우**

```java
public class Bird {
    public void fly() {
    }
}
```

새는 나니깐 위와 같은 클래스를 작성했다고 가정해보자.

```java
public class Eagle extends Bird {}
```

독수리 같은 경우는 하늘을 날기 때문에 아무런 문제가 발생하지 않는다.

```java
public class Ostrich extends Bird{}
```

하지만 타조의 경우 새지만 날지 못한다. Bird를 Ostrich로 대체하게 되면 fly메서드를 사용하는데 문제가 발생한다. 하위 타입이 상위 타입을 대체하지 못한것이다. 

**LSP를 준수한 경우**

```java
public class Bird {}
```

```java
public class FlyingBird extends Bird{
    public void fly(){}
}
```

Bird클래스와 이를 상속한 FlyingBird를 생성하였고,

```java
public class Eagle extends FlyingBird{}
```

```java
public class Ostrich extends Bird{} 
```

fly 기능을 FlyingBird 클래스에서 가져가면서 Ostrich가 Bird를 대체하면서 발생했던 문제는 해결했다. 

---

## 인터페이스 분리 - Interface segregation

> 클라이언트는 자신이 이용하지 않는 메서드에 의존하지 않아야 한다.
객체가 자신에게 필요한 기능만 가지도록 제한해야한다.
> 

큰 인터페이스는 작은 단위로 분리되어야 하고 그렇게 함으로 구현 클래스는 관심 있는 메서드에만 구현하도록 해야한다. 인터페이스를 구체적이고 작은 클래스로 나누고 꼭 필요한 메서드만 이용할 수 있게 하라는 의미이다. 

### 코드를 통해서 확인

```java
public interface BearKeeper {
    void washTheBear();
    void feedTheBear();
    void petTheBear();
}
```

곰사육사를 위한 기능을 설계한다고 생각해보자. 사용사는 씻기거나 먹이를 주는 행위를 수행할 수 있지만 곰에게 애정을 주고 싶진 않을 수 있다. 하지만 위 인터페이스에서는 곰사육사 역할을 하기 위해서는 애정을 주는 행위도 필수적으로 수행하여야 한다.

```java
public interface BearCleaner {
    void washTheBear();
}

public interface BearFeeder {
    void feedTheBear();
}

public interface BearPetter {
    void petTheBear();
}
```

하나의 인터페이스로 선언되어 있던 곰 사육사의 기능을 세부 기능으로 분리시킨다 하면 각각 특정 역할만 전문적으로 수행할 수 있다. 기존의 사육사는

```java
public class BearCarer implements BearCleaner, BearFeeder {

    public void washTheBear() {
        //I think we missed a spot...
    }

    public void feedTheBear() {
        //Tuna Tuesdays...
    }
}
```

씻기기와 먹이주기 기능을 이전과 같이 수행할 수 있다. 기피했던 곰에게 애정을 주는 행위는 

```java
public class CrazyPerson implements BearPetter {

    public void petTheBear() {
        //Good luck with that!
    }
}
```

새로운 사람에게 양보할 수 있다.

---

# 의존성 역전 - Dependency Inversion

> 객체는 ***저수준 모듈보다 고수준 모듈에 의존***해야한다.
추상화는 세부 사항에 의존해서는 안된다.
→ 변하기 쉬운 것에 의존하기 보다는, 변화하지 않는 것에 의존하라
> 

모듈간 낮은 결합도와 테스트 용이성을 확보하는데 유요한 디자인 방법.

고수준 모듈 : 인터페이스와 같은 추상적인 개념

저수준 모듈 : 구현된 객체

시스템의 컴포넌트가 의존성을 갖을때 우리는 직접적으로 의존성을 주입하는것을 원하지 않는다. 대신에 그 사이에 추상레벨을 사용해야 한다.

**고수준 모듈은 저수준 모듈에 종속되어서는 안된다.**

→ 저수준 모듈은 고수준 모듈에 영향을 미치지 않아야 한다.

**세부 사항은 추상화에 따라 달라져야 한다.**

→ 구성 요소가 독립적으로 작동해야 한다.

의존성은 인터페이스나 추상 클래스를 대상으로 해야한다.

### 코드를 통해 알아봅시다.

![Untitled](https://www.baeldung.com/wp-content/uploads/sites/4/2023/06/class_diagram.png)

ClassA가 ClassB를 의존하고 있다고 가정해보자.

```java
class ClassB {
    // fields, constructor and methods
}

class ClassA {
    ClassB objectB;

    ClassA(ClassB objectB) {
        this.objectB = objectB;
    }
    // invoke classB methods
}
```

의존성을 역전 시켜보면

![Untitled](https://www.baeldung.com/wp-content/uploads/sites/4/2023/06/class_diagram_.png)

제어의 흐름은 그대로이지만 의존의 희름이 역전되는 것을 확인할 수 있다.

```java
interface InterfaceB {
    method()
}

class ClassB implements InterfaceB {
    // fields, constructor and methods
}

class ClassA {
    InterfaceB objectB;

    ClassA(InterfaceB objectB) {
        this.objectB = objectB;
    }
    ...
}
```

### DIP의 이점

- TDD를 통해 다른 컴포넌트를 개발하기 쉽다.
- 컴포넌트를 확장하고 OCP를 적용하기 쉽다.
- 시스템 관점에서는 독립적으로 배포하기 쉽다.
- 변화에 독립적으로 브랜치를 머지하ㅣ 쉽다.
