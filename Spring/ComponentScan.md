# 컴포넌트 스캔
```
1. 컴포넌트 스캔과 의존 관계 자동 주입
2. 탐색 위치와 기본 스캔 대상
3. 중복 등록과 충돌
```
---
## 1. 컴포넌트 스캔과 의존 관계 자동 주입
- 등록될 빈이 많을 경우 `@Bean`, `XML`을 통한 등록은 생산성 하락, 설정 정보 증대, 누락 발생
- `@ComponentScan`을 통한 자동 스프링 빈 등록 기능 사용.
- `@Autowired`를 통한 의존관계 자동 주입 기능 사용.

#### `@ComponentScan`
- 컴포넌트 스캔을 사용하려면 `@ComponentScan`어노테이션을 설정 정보에 추가한다.
- `@Component`애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다.
- 스프링 빈의 기본 이름은 클래스명으로 설정되며 앞글자만 소문자로 사용된다.
    - MemeberService -> memberService
- `@Component("beanName")`으로 지정도 가능하다.

#### `@Autowired`
- 의존관계를 자동 주입해준다.
- 생성자에 지정하면 스프링 컨테이너가 해당 빈을 찾아서 주입한다.
- 기본 조회 전략은 타입이 같은 빈을 주입.
- 생성자의 파라미터 갯수에 관계없이 주입.


### 컴포넌트 스캔과 자동 의존 관계 주입 동작
1. `@ComponentScan`
<img width="410" alt="컴포넌트 스캔" src="https://user-images.githubusercontent.com/20774279/152462055-644223e4-4d7c-400f-b2fa-331f6845238b.png">
2.`@Autowired`
<img width="408" alt="의존관계 자동 주입" src="https://user-images.githubusercontent.com/20774279/152462062-0b5aa8b3-1956-46e5-be1b-e7b450d56f4f.png">
---
## 2. 탐색 위치와 기본 스캔 대상
```java
@ComponentScan(
    basePackages={"com.hello","com.world"}
)
```
- basePackages를 통해 탐색할 패키지의 시작 위치를 지정하고, 이 패키지를 포함해서 하위 패키지를 모두 탐색한다.
- 위 코드 처럼 여러 위치를 지정할 수 있다.
- 지정하지 않으면 `@ComponentScan`이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.
> 패키지 위치를 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것을 권장. 스프링 부트도 이 방법을 기본으로 재공.
>> `@SpringBootApplication` 안에 `@ComponentScan`이 들어 있다.


### 컴포넌트 스캔 기본 대상
- `@Component` : 컴포넌트 스캔에서 사용
- `@Controller` : 스프링 MVC 컨틀롤러에서 사용
- `@Service` : 스프링 비즈니스 로직에서 사용
- `@Repository` : 스프링 데이터 접근 계층에서 사용, 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 반환해주다.
- `@Configuration` : 스프링 설정 정보에서 사용, 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤으로 유지하도록 추가 처리한다. 
---
## 3. 중복 등록과 충돌
- 같은 빈 이림으 등록되면 충돌이 발생한다.
  1. 자동 빈 등록 VS 자동 빈 등록
    - 스프링이 `ConflictingBeanDefinitionException`예외 발생 
  2. 수동 빈 등록 VS 자동 빈 등록
    - 수동 등록된 빈과 자동 드록된 빈이 충돌하면 수동 등록된 빈이 우선권을 자는다.(수동 등록 빈이 자동 등록 빈을 오버라이드 한다.)











