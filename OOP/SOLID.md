# 좋은 객체지향 설계의 4가지 원칙(SOLID)
```
SRP : 단일 책임 원칙 (Single responsibility principle)
OCP : 개방-폐쇄의 법칙 (Open/closed principle)
LSP : 리스코프 치환 원칙 (Liskov substitution principle)
ISP : 인터페이스 분리 원칙 (Interface segregation principle)
DIP : 의존관계 역전 원친 (Dependency inversion principle)
```
---
## SRP : 단일 책임 원칙 (Single responsibility principle)
- 하나의 클래스는 하나의 책임
  - 책임은 클수도, 작을수도 있다.
  - 문맥, 상황에 따라서 다르다.
- 책임의 주요 기준은 변경이다.
  - 변경이 있을때 파급효과가 적으면 좋다.
---
## `OCP` : 개방-폐쇄의 법칙 (Open/closed principle)
- 확장에는 열려있고, 변경에는 닫혀있어야 한다.
- 다형성을 활용
---
## LSP : 리스코프 치환 원칙 (Liskov substitution principle)
- 인터페이스 규약을 지켜야한다.
- 기능적인 보장이 되어야 한다.(ex. 자동차 엑셀을 구현하면 자동차가 앞으로 가야된다.)
---
## ISP : 인터페이스 분리 원칙 (Interface segregation principle)
- 인터페이스를 여러개 두는것이 하나를 범용적으로 사용하는것보다 낫다.
- 인터페이스가 명확할수록 대체 가능성이 높아진다. (ex. 자동IF -> 운전IF, 정비IF)
---
## `DIP` : 의존관계 역전 원친 (Dependency inversion principle)
- 추상화에 의존해야하며, 구체화에는 의존하면 안된다. 
- 클라이언트는 인터페이스를 바라본다. -> 구현 의존X, 인터페이스에 의존O
---

객체 지향의 핵심 => 다형성