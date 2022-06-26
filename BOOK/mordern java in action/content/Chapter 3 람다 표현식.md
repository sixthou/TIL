# 랍다 표현식
- 람다 표햔식은 익명 클래스처럼 이름이 없는 함수면서 메서드를 인수로 전달할 수 있다.
- 이번 장에서 배울 것.
  - 람다 표현식을
    - 어떻게 만드는지
    - 어떻게 사용하는지
    - 어떻게 코드를 간결하게 만들 수 있는지.
  - 자바 8에 추가된 중요한 인터페이스와 형식 추론
  - 메서드 참조
---
## 람다란 무엇인가?
- 메서드로 전달할 수 있는 익명 함수를 단순화한 것.
- 람다 특징
  - 익명 - 보통의 메서드와 달리 이름이 없다
  - 함수 - 특정 클래스에 종속되지 않는다. 
  - 전달 - 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
  - 간결성 - 익명 클래스처럼 자질구레한 코드를 구현할 필요가 없다.
- 람다 표현식
  ![람다 표현식](https://user-images.githubusercontent.com/20774279/175805636-e128df27-2d3d-4101-835a-dff2dfaf6db7.png)
    - 람다 파라미터
    - 화살표 - 람다의 파라미터 리스트와 바디를 구분한다.
    - 람다 바디 - 람다의 반환값에 해당하는 표현식
## 람다를 어디에, 어떻게?
-  함수형 인터페이스
   - `@FunctionalInterface`
   - 하나의 추상 메서드를 지정하는 인터페이스
   - 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있다.
   - 함수형 인터페이스를 인수로 받는 메서드만 람다 표현식을 사용할 수 있다.
- 함수 디스크립터

## 실행 어라운드 패턴
- 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태
- 실행 어라운드 패턴 적용
  - 1단계
  - ``` java
    public String processFile() throws IOException {
        try (BufferdReader br = new BufferedReader(new Filereader("data.txt"))){
            return br.readLine();
        }
    }
    ```
  - 2단계
  - ``` java
    public interface BufferedReaderProcessor{
        String process(BufferedReader b) throw IOException;
    }

    public String processFile(BufferedReaderProcessor p) throws IOException {
        ...
    }
    ```
  - 3단계
  - ``` java
    public interface BufferedReaderProcessor{
        String process(BufferedReader b) throw IOException;
    }

    public String processFile(BufferedReaderProcessor p) throws IOException {
        try (BufferdReader br = new BufferedReader(new Filereader("data.txt"))){
            return p.process(br);
        }
    }
    ```
  - 4단계
  - ```java
    String oneLine = processFile((BufferedReader br) -> br.readLine());
    String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
    ```

---
## 함수형 인터페이스 사용
### Predicate
- T의 객체를 인수로 받아 불리언을 반환한다.
```java
    @FunctionalInterface
    public interface Predicate<T> {
        boolean test(T t);
    }
```
### Consumer
- T의 객체를 받아서 void를 반환하는 accept 추상 메서드를 정의한다.
```java
    @FunctionalInterface
    public interface Consumer<T> {
        void accept(T t);
    }
```
### Function
- T를 인수로 받아서 R을 반환하는 추상 메서드 apply를 정의한다.
```java
    @FunctionalInterface
    public interface Function<T, R> {
        R apply(T t)
    }
```
### 기본형 특화
- 제너릭 파라미터에는 참조형만 사용할 수 있다. 기본형을 참조형으로 변환하는 기능을 제공(박싱, 언박싱, 오토박싱)제공하지만 비용이 소모된다. 오토박싱을 피할 수 있도록 특화 인터페이스를 제공한다.
- 함수형 인터페이스 이름 앞에 기본형을 붙인다.
  - DoublePredicate, IntConsumer, LongBinaryOperator
- Unary - 파라미터 타입과 반환 타입이 같은 경우
  - IntUnaryOperator, LongUnaryOperator
- Bi - 파라미터 인자가 두개인 경우
  - BiPredicate, BiConsumer

---
## 형식 검사, 형식 추론, 제약
### 형식 검사
- 람다가 사용되는 콘텍스트를 이용해서 람다의 형식을 추론할 수 있다.
  1. 람다가 사용된 콘텍스트가 무엇인지 메서드의 선언을 확인한다.
  2. 메서드의의 대상 형식으로 어떤 대상형식을 기대하는지 확인한다.
  3. 대상형식이 어떤 함수형 인터페이스인지 확인한다.
  4. 함수 디스크립터를 묘사한다.
  5. 전달된 인수는 요구사항을 만족해야 한다.
### 형식 추론
- 자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다.
- 형식을 추론하지 않음
  - ```java
    Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
    ```
- 형식을 추론함
- - ```java
    Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
    ```
### 지역 변수 사용
- 람다 표현식에서는 자유변수(외부에서 정의된 변수)를 활용할 수 있다.(람다 캡처링)
- 지역변수는 명시적으로 final로 선언되거나 실질적으로 final로 선언된 변수와 똑같이 사용된 경우만 사용할 수 있다.
- 인스턴스 변수는 입에 지역 변수는 스택에 위치한다.
- 변수할당이 해제 되었는데 람다에서 해당 변수를 접근하는 경우가 있을 수 있다.
- 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다.
---
## 메서드 참조
- 람다 표현식을 축약한 것.
    ```java
        inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
    ```
    ```java
        inventory.sort(comparing(Apple::getWeiht))
    ```
