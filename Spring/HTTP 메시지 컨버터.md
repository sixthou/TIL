# HTTP 메시지 컨버터
- 뷰 템플릿으로 HTMl을 생성해서 응답하는 것이 아니라, HTTP API 처럼 JSON 데이터를 HTTP 메시지 컨버터를 사용하면 편리.
- @ResponseBody를 사용
  - HTTP BODY에 문자 내용을 직접 반환
  - viewResolver 대신에 HttpMessageConverter가 동작 
  - 기본 문자처리 : StringHttpMessageConverter
  - 기본 객체처리 : MappingJackson2HttpMessageConverter
  - byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음.

__스프링 MVC는 다음의 경우 HTTP 메시지 컨버터를 적용한다.__
- HTTP 요청 : @RequestBody, HttpEntity(RequestEntity)
- HTTP 응답 : @ResponseBody, HttpEntity(RequestEntity)

### HTTP 메시지 컨버터 인터페이스 
```java
public interface httpMessageConverter<T> {
    boolean canRead(class<?> clazz, @nullable MediaType);
    boolean canWrite(class<?> clazz, @nullable MediaType);

    List<MediaType> getSupportMediaType();

    T read(Class<? extends T> clazz, HttpInputMessage inputMessage) throws IOException,HttpMessageNotReadableException;
    void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage) throws IOException,HttpMessageNotReadableException;
}
```

HTTP 메시지 컨버터는 HTTP 요청, HTTP 응답 둘 다 사용된다.
- canRead(), canWrite() : 메시지 컨버터가 해당 클래스, 미디어타입을 지원하는지 체크
- read(), write() : 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능

- 스프링 부트의 기본 메시지 컨버터
  - ByteArrayHttpMessageConverter : byte[]데이터를 처리한다. 
    - 클래스 타입 : byte[], 미디어 타입 : */*,
    - 요청 예) @RequestBody byte[] data
    - 응답 예) @ResponseBody return byte[] 쓰기 미디어타입 application/octet-stream
  - StringHttpMessageConverter : String 문자로 데이터를 처리한다.
    - 클래스 타입 : String, 미디어 타입 : */*,
    - 요청 예) @RequestBody String data
    - 응답 예) @ResponseBody return "ok" 쓰기 미디어타입 text/plain
  - MappingJackson2HttpMessageConverter : application/json
    - 클래스 타입 : 객체 또는 HashMap, 미디어 application/json 관련
    - 요청 예) @RequestBody HelloData data
    - 응답 예) @ResponseBody return helloData 쓰기 미디어타입 applicaion/json관련 

### HTTP 요청 데이터 읽기
- HTTP 요청이 오고, 컨트롤러에서 @RequestBody, HttpEntity 파라미터를 사용한다.
- 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 canRead()를 호출한다.
  - 대상 클래스 타입을 지원하는가.
    - 예) @Request의 대상 클래스(byte[], String, HelloData)
  - HTTP 요청의 Content-Type 미디어 타입을 지원하는가.
    - 예) text/plain, application/json, \*/* 
  - canRead() 조건을 만족하면 read()를 호출해서 객체 생성하고, 반환한다.

### HTTP 요청 데이터 읽기
- 컨트롤러에서 @RequestBody, HttpEntity로 값이 반환된다.
- 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 canWrite()를 호출한다.
  - 대상 클래스 타입을 지원하는가.
    - 예) return 의 대상 클래스(byte[], String, HelloData)
  - HTTP 요청의 Accept 미디어 타입을 지원하는가(더 정확히는 @RequestMapping의 produces)
    - 얘) test/plain, apllicaion/json, \*/*
  - canWrite() 조건을 만족하면 write()를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다.

### 요청 매핑 헨들러 어뎁터 구조
<img width="724" alt="RequestMappingHandlerAdapter 동작 방식" src="https://user-images.githubusercontent.com/20774279/162569505-cd0c0be0-28d4-4686-bc25-04bc9cd93398.png">

### Argument Resolver
- 파라미터를 유연하게 처리할 수 있는 이유.
- 애노테이션 기반 컨트롤러를 처리하는 RequestMappingHandlerAdapter는 ArgumentResolver를 호출해서 컨트롤러(핸들러)가 필요로 하는 다양한 파라미터의 값(객체)을 생성한다.
- 파라미터의 값이 준비되면 컨트롤러를 호출해서 값을 넘겨준다.
- HandlerMethodArgumentResolver가 ArgumentReolver이다.
- 동작방식 
  - ArgumentResolver의 supportParameter()를 호출해서 해당 파라미터를 지원하는지 체크하고
  - 지원하면 resolveArgument()를 호출해서 실제 객체를 생성한다. 
  - 생성된 객체가 컨트롤러 호출시 넘어간다.

### ReturnValueHandler
- HandlerMethodReturnValueHandler를 줄여서 ReturnValueHandler라 부른다.
- ArgumentResolver와 비슷하게 이것이 응답 값을 변환하고 처리한다.
  

### HTTP 메시지 컨버터
<img width="721" alt="HTTP 메시지 컨버터 위치" src="https://user-images.githubusercontent.com/20774279/162569805-65c43a17-e562-472b-8cdc-d37ec3582dfc.png">
- 요청의 경우 
  - @RequestBody를 처리하는 ArgumentResolver가 있고, HTTPEntity를 처리하는 ArgumentResolver가 있다. 이 ArgumentResolver들이 HTTP 메시지 컨버터를 사용해서 필요한 객체를 생성하는 것이다
- 응답의 걍우
  - @RequestBody와 HttpEntity를 처리하는 ReturnValueHandler가 있다. 그리고 여기에 HTTP 메시지 컨버터를 호출해서 응답 결과를 만든다.
- 스프링 MVC의 경우 
  - @RequestBody, @RequestBody가 있으면
    - RequestResponseBodyMethodProcess(ArgumentResolver)를 사용.
  - HttpEntity가 있으면
    - HttpEntityMethodProcessor(ArgumentResolver)를 사용.
