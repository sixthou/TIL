# 스프링 MVC
## 스프링 MVC 구조
<img width="970" alt="스프링 MVC 구조" src="https://user-images.githubusercontent.com/20774279/161529996-9e44c185-3d90-4273-a6f5-8b311f2eaaa6.png">

### Dispatcher Servlet
- 스프링 MVC의 프론트 컨트롤러
- 부모 클래스에서 HttpServlet 상속, 서블릿으로 동작한다.
- 스프링 부트는 DispatcherServlet을 서블릿으로 자동으로 등라하면서 모든 경로에 대해서 매핑한다`(urlPatterns="/")`
- 요청 흐름
  - FrameworkServlet.service를 시작으로 여러 메서드가 실행됨.
  - DispatcherServlet.doDispatch()
  - DispatcherServlet.processDispatchResult
  - DispatcherServlet.render()
- 동작 순서
  1. 핸들러 조회
    - 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
  2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
    - 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.    
  3. 핸들러 어댑터 실행 
    - 핸들러 어댑터를 실핸한다. 
  4. 핸들러 실행
    - 핸들러 어댑터가 실제 핸들러를 실핸한다.
  5. ModelAndView 반환
    - 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
  6. viewResolver 호출 
    - 뷰 리졸버를 찾고 실행한다.  
  7. View 반환
    - 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
  8.  View 랜더링
    - 뷰를 통해서 뷰를 렌더링 한다. 

## 기본 어노테이션
- `@Controller`
  - 스프링이 자동으로 스프링 빈으로 등록한다
  - 스프링 MVC에서 에노테이션 기반 컨트롤러로 인식한다.
  - 반환 값이 String이면 뷰 이름으로 인식한다 -> 뷰를 찾고 뷰가 랜더링 됨.
- `@RequestMapping`
  - 요청 정보를 매핑한다. 해당 URL이 호출되면 이 메서드가 호출된다. 애노테이션을 기반으로 동작하기 떄문에, 메서드의 이름은 임으리로 지으면 된다.
- `@RestController`
  - 반환 값으로 뷰를 찾는 것이 아니라 HTTP 바디에 바로 입력한다.