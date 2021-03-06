# IOC, DI, Container

## 제어의 역전 IoC(Inversion of Control)
- 내가 호출 하는게 아니라, 프레임워크 같은게 대신 호출해 주는 것 => 제어권의 역전
- 기존 프로그램 : 클라이언트 구현 객체가 필요한 서버 구현 객체를 생성, 연결, 실행한다. => 구현 객체가 프로그램 제어 흐름 조종.
- 컨테이너 사용 : 구현 객체는 로직 실행 역할만 담당, 컨테이너가 제어흐름을 가져간다. 구현 객체는 필요한 인테페이스를 호출하지만, 어떤 객체들이 실행될지 모른다. 
- 제어 흐름 권한은 모두 컨테이너가 가져간다.
- `제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것이 제어의 역전`

### 프레임워크 vs 라이브러리
- 프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것이 프레임워크다.
- 필요한 부분만 작성하면 적절한 타이밍에 호출을 하는게 프레임워크
  - Ex.) Junit - 실행하고 제어를 Junit이 담당하고 자체 라이프 사이클이 잇음(beforeeach 같은..)
- 작성한 코드가 직접 제어의 흐름을 담당하면 라이브러리
  - Ex.) 자바 객체를 XML, Json으로 변경하는 경우
---
## 의존 관계 주입 DI(Dependency Injection)
- 의존관계는 정적인 클래스 의존 관계와 실행 시점에 결정되는 동적인 객체 의존 관계를 분리해서 생각해야됨.

### 정적인 클래스 의존 관계
- 클래스가 사용하는 import 코드만 보고 의존 관계를 쉽게 판단, 정적인 의존 관계는 애플리케이션을 실행하지 않아도 분석 가능하다.
- 클래스 의존관계만으로는 실제 어떤 객체가 주입될지 알 수 없다.

### 동적인 객체 인스턴스 의존 관계
- 애플리케이션 실행 시점에 외부에서 실제 구현 객체를 생성하고, 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 `의존관계 주입`이라 한다.
- 객체 인스턴스를 생성하고, 그 찹조값을 전달하여 연결
- 의존 관계 주입을 사용하면 클라이언트 코든 병경없이 클라이언트가 호출하는 대상 타입 인스턴스를 변경할 수 있다.
- 의존관계 주입을 사용하면 정적클래스 의존관계 변경하지 않고, 동적객체 인스턴스 의존객체 쉽게 변경 가능하다.
---
## IoC 컨테이너, DI 컨테이너
- 객체를 생성하고, 관리하며 의존 관계를 연결해준다 -> IoC컨테이너 또는 DI컨테이너라고 하다.