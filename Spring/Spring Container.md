# 스프링 컨테이너
- ApplicationContext를 스프링 컨테이너라 한다.
- 스프링 컨테이너가 객체를 생성하고 DI한다.
- 스프링 컨테이너에 등록된 객체를 스프링 빈이라고 한다.
- 스프링 빈은 @Bean이 붙은 메서드 명을 스프링 빈의 이름으로 사용.(@Bean(name ="aa") 이름 변경 가능) 
- 스프링 컨테이너를 통해 객체를 찾는다. applicationContext.getBean()
