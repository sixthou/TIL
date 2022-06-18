# 동작 파라미터화 코드 전달하기
- `동작 파라미터화` 란
  - 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다.
  - 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미
  - 코드블록은 나중에 프로그램에서 호출되며 실행은 나중으로 미뤄진다.
  - 나중에 실행될 메서드의 인수로 코드 블록을 전달할 수 있다.
  - 코드블록에 따라 메서드의 동작이 파라미터화된다.
  

<br>

# 동작 파라미터화 방법
- 선택 조건을 결정하는 인터페이스 정의
    ``` java
    public interface ApplePredicate {
        boolean test (Apple apple);
    }
    ```
- 클래스 구현
    ``` java
    public class AppleReadAndHeavyPredicate implements ApplePredicate {
        public boolean test(Apple apple){
            return RED.equals(apple.getColor()) && apple.getWeight() > 150;
        }
    }

    List<Apple> redAndHeabyApples = filterApples(inventory, new AppleReadAndHeavyPredicate());
    ```
    - 구현하는 여러 클래스를 정의한 다음에 인스턴스화 해야되는 번거로움이 있음.
    - 로직과 관련 없는 코드가 추가됨.
- 익명 클래스
  ```java
  List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple){
        return RED.equals(apple.getColor());
    }
  })
  ```
  - 지역 클래스와 비슷한 개념
  - 이름이 없는 클래스
  - 클래스 선언과 인스턴스화를 동시에 할 수 있다.
  - 반복되는 지저분한 코드 존재
  - 익명 클래스 사용에 익숙하지 않다.
- 람다 표현식 사용
  ``` java
  List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equlas(apple.getColor()));
  ```

