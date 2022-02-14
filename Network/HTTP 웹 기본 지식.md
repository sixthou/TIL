# HTTP 웹 기본 지식
> 모든 개발자를 위한 HTTP 웹 기본 지식 강의를 정리

## 인터넷 네트워크
- 인터넷 통신
- IP(Internet Protocol)
  - 지정한 IP에 데이터 전달
  - 패킷이라는 통신 단위로 데이터 전달
  - 한계
    - 비연결성 : 패킷 받을 대상이 없거나 서비스 불능 상태여도 패킷 전송
    - 비신뢰성 : 패킷 분실, 순서보장 x
    - 프로그램 구분 : 같은 IP를 사용하는 애플리케이션이 둘 이상일떄
- TCP
  - 연결지향 : 논리적인 연결
    - TCP 3 way hadnshake
    - 데이터 전달 보증
    - 순서 보장
    - 신뢰할 수 있는 프로토콜
    - 대부분의 연결이 TCP 사용
- UDP
  - 기능이 거의 없다. IP와 비슷하며 port, checksum 정도만 추가
  - 데이터 전달 및 순서가 보장되지 않지만, 단순하고 빠르다 
- PORT
  - 같은 IP 내에서 프로세스 구분
  - 0 ~ 65535 : 할당가능 
  - 0 ~ 1023 : well known port, 사용하지 않는 것이 좋다.
- DNS
  - 도메인 명, IP 를 매핑시켜 가지고 있음
  - 도메인 명을 Ip 주소로 반환

---

## URL와 웹 브라우저 요청 흐름
- URL
- URL - Locator : 리소스가 있는 위치를 지덩
  - 위치는 변할 수 있다.
- URN - Name : 리소스에 이름을 부여
  - 이름은 변하지 않는다.
  - URI ()
### URL
```
scheme://[userinfo@]host[:port][/path][?query][#fragment]
https://www.google.com:443/search?q=hello&hl=ko
```
- scheme
  - 주로 프로토콜에 사용(http, https, ftp 등등)
- userinfo
  - URL에 사용자정보를 포함해서 인증
  - 거의 사용하지 않음
- host
  - 호스트명
  - 도메인명 또는 IP 주소를 직접 사용가능
- port
  - 포트
  - 접속 포트
  - 일반적으로는 생략 https - 443, http - 80
- path
  - 리소스 경로, 계층적 구조
- query
  - key=value형태
  - ?로 시작, &로 추가가능 ?q=hello&hl=ko
  - query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태
- fragment
  - htrml 내부 북마크 등에 사용
  - 서버에 전송한느 정보 아님

### 웹 브라우져 흐름
1. 웹 프라우저가 http 메시지 생성
2. socket 라이브러리를 통해 전달
   - A : TCP/IP 연결(IP, PORT)
   - B : 데이터 전달
3. TCP/IP 패킷 생성, HTTP 메시지 포함
4. 웹 브라우저 요청 패킷 전달
5. 서버 요청 패킷 도착
6. 서버 응답 패킷 전달
7. 웹 브라우저 응답 패킷 도착
8. 웹 브라우저 HTML 렌더링

---

## HTTP 기본
- HTTP
  - HTTP 메시지에 거의 모든 형태의 데이터를 전송할 수 있다.
  - HTML, TEXT, IMAGE, 음성, 영상, 파일, JSON, XML
  - 서버간의 데이터를 주고 받을 때도 대부분 HTTP 사용
  - TCP를 직접 연결하는 경우는 거의 없다.
  - HTTP/1.1, HTTP.2 - TCP
  - HTTP.3 - UDP
- HTTP 특징
  - 클라이언트 서버 구조
    - Request Response 구조
    - 클라이언트는 서버에 요청을 보내고, 응답을 대기
    - 서버가 요청에 대한 결과를 만들어서 응답 -> 응답 결과를 얻어서 클라이언트가 동작한다.
    - 클라이언트 서버를 분리하는 이유 => 독립적으로 진화
      - 클라이언트 : UI, 사용성에 집중
      - 서버 : 비즈니스 로직, 테이터 저장
  - 무상태 프로토콜
    - 서버가 클라이언트 상태 보존 x
    - 장점 : 서버 확장성이 높음(스케일 아웃), 응답 서버를 바꿀 수 있다.
    - 단점 : 클라이언트가 추가 데이터 전송
    - sateful 
      - 항상 같은 서버가 유지되어야 한다.
      - 중간에 서버에 장애가 나면 처음부터 다시해야됨.
    - sateless
      - 아무 서버나 호출해도 된다
      - 중간에 장애가 나면 다른 서버가 처리 가능하다
      - 스케일 아웃 가능
    - 실무에서 최대한 무상태로 설계하고 정말 어쩔 수 없는 경우에만 상태유지를 한다. 알번적으로 브라우저 쿠키와 서버 세션등을 사용해서 상태 유지
  - 비 연결성
    - 연결을 유지하는 모델 : 서버가 연결을 계속 유지하면서 자원이 소모된다.
    - 연결이 유지되지 않는 모델 : 서버는 연결을 유지하니 않고, 최소한의 자원을 유지한다.
    - HTTP는 기본이 연결을 유지하지 않는 모델
    - 섭버 자원을 효율적으로 사용가능
    - 단점
      - TCP/IP 연결을 새로 맺어야 함
      - 수 많은 자원이 함계 다운로드 되는 경우 연결을 요청마다 새로 맺어야됨
      - 극복 
        - HTTP 지속 연결로 문제 해결
  - HTTP 메시지
    ```
    HTTP-message   = start-line(request-line or status-line)
                          *( header-field CRLF )
                          CRLF
                          [ message-body ]
    ```
    | HTTP 메시지 구조 |
    |:--:|
    |start-line 시작 라인|
    |header 헤더|
    |empty line 공백 라인(CRLF)|
    |message body 
    - start-line 시작 라인
      - 요청 메시지
        ``` 
          GET /search?q=hello&hl=ko HTTP/1.1)
        ```
        - start-line = request-line
        - request-line = method SP(공백)request-target SP HTTP-version CRLF(엔터)
          - method
            - HTTP 메서드
            - GET, POST, PUT, DELETE...
            - 서버가 수행해야 할 동작 지정
          - request-target
            - absolute-path\[?query\]\(절대경로\[?쿼리\]\)
          - HTTP version
      - 응답 메시지 
        ```
        HTTP/1.1 200 OK
        ```
        - start-line = status-line
          - status-line = HTTP-version SP status-cde SP reason-phrase CRLF
            - HTTP-version
              - HTTP 버전
            - status-cde
              - HTTP 상태코드
            - reason-phrase
              - 이유 문구 
    - header 헤더
      ```
      요청
      Host: www.google.com
      응답
      Content-Type:text/html;charset=UTF-8

      ```
      - header-field = field-name":"OWS(띄어쓰기 허용)field-value OWS
      - HTTP 전송에 필요한 모든 부가정보 
        - 메시지 바디 내용, 메시지 바디 크기, 압축, 인증...
      - 표준 헤더
      - 임의의 헤더 추가가능
    - message body 메시지 바디
      ```
      <html>
        <body>...</body>
      </html>
      ```
      - 실제 전송할 데이터
      - HTML 문서, 이미지, 영상, JSON 등 Byte로 표현할 수 있는 모든 데이터 전송 가능 
---
## HTTP 메서드
- API URI
  - 리소스 식별이 가장 중요
  - 리소스를 식별하고, uri계층 구조를 활용한다.
  - 리소스와 해당 리소스를 대상으로 하는 행위를 분리한다.
    - 리소스 : 회원
    - 행위  : 조회, 등록, 삭제, 변경
    - 리소스는 명사 행위는 동사
  - HTTP 메소드
    - 클라이언트가 서버에 요청할때 기대하는 행동
    - GET 
      - 리소스 조회
      - 서버에 전달하고 싶은 데이터는 query를 통해서 전다
      - 메시지 바디를 사용해서 데이터를 전달할 수 있지만, 지원하지 않는 곳이 많아 쿼리파람 권장
    - POST
      - 요청 데이터 처리, 주로 등록에 사용
      - 메시지 바디를 통해 서버로 요청 데이터 전달
        - 메시지 바디를 통해 들어온 데이터를 처리하는 모든 기능을 수행
      - 주로 전달된 데이터로 신규 리소스 등록, 프로세스 처리에 사용
      - 요청 데이터를 어떻게 처리할지는 리소스마다 따로 정해야한다.
      - 다른 메서드로 처리가 애매한 경우에도 사용된다.
    - PUT : 리소스를 대체, 해당 리소스가 없으면 생성
      - 리소스를 대체
        - 리소스가 있으면 대체
        - 리소스가 없으면 생성
        - 완전히 대체한다 -> 덮어 버린다.
      - 클라이언트가 리소스를 식별
        - 클라이언트가 리로스 위치를 알고 uri지정
    - PATCH 
      - 리소스 부분 변경
      - PUT과 다르게 부분 변경한다. 
    - DELETE 
      - 리소스 삭제
    - 비주류
      - HEAD : GET과 동일하지만 메시지 부분을 제외하고, 상태 줄과 헤더만 변형
      - OPTIONS : 대상 리소스에 대한 통신 가능 옵션을 주로 설명
      - CONNECT : 대상 자원으로 식별되는 서버에 괸한 터널을 설정
      - TRACE : 대상 리소스에 대한 결오를 따라 메시지 루프백 테스트를 수행
- HTTP 메서드의 속성
  <img width="642" alt="HTTP요약" src="https://user-images.githubusercontent.com/20774279/153815460-da4d3284-6743-45aa-abf9-0b9c16ffd8f1.png">
  - 안전 
    - 호출해도 리소스를 변경하지 않는다.
  - 멱등
    - 몇 번을 호출하든 결과과 동일하다.
    - 자동 복구 메커니즘에 활용
    - 서버가 정상 응답을 주지 못했을때, 같은 요청을 다시해도 되는가? -> 멱등이면 가능하다.
    - 중간에 외부 요청에 따라 리소스가 변경되는 것 까지는 고려하지 않는다.
  - 캐시가능
    - 응답 결과 리소스를 캐시해서 사용해도 되는가
    - GET, HEAD, POST, PATCH 캐시가능
    - 실제로는 GET, HEAD 정도만 사용
      - key가 맞아야 하는데 POST, PATCH는 본문 내용까지 캐싱해야하기 때문에 어렵다.
---
## HTTP 메소드 활용
- 클라이언트에서 서버로 데이터 전송
  - 전달 방식
    - 쿼리 파라미터를 이용한 전송
      - GET
      - 주로 정렬 필터
    - 메시지 바디를 이용한 전송
      - POST, PUT, PATCH
      - 회원가입, 상품 주문, 리소스 등록, 리소스 변경
  - 4가지 상황
    - 정적 데이터 조회
      - 이미지, 정적 텍스트 문서
      - 조회는 GET 사용
      - 정적 데이터는 일반적으로 쿼리 파라미터 없이 리소스 경로로 단순하게 조회 가능
    - 동적 데이터 조회
      - 주로 검색, 게시판 목록에서 정렬 필터
      - 조회 조건을 줄요주는 필터, 조회 결과를 정렬하는 정렬 조건에 주로 사용
      - 조회는 GET사용
      - GET은 쿼리 파라미터 사용해서 데이터 전달
    - HTML FORM을 통한 데이터 전송
      - POST 전송
        - 회원 가입, 상품 주문, 데이터 변경
        - Content-Type : application/x-www-form-urlencoded 사용
          - form의 내용을 메시지 바디를 통해 전송(key=value, 쿼리 파라미터 형식)
          - 전송 데이터를 url encoding 처리
        - Content-Type : multipart/form-data
          - 파일 업로드 같은 바이너리 데이터 전송시 사용
          - 다른 종류의 여러 파일과 폼의 내용 함꼐 전송 가능
      - GET 전송 
        - 쿼리 파람으로 전송 됨
        - 저장에는 사용하면 안됨.
    - HTTP API를 통한 데이터 전송
      - 서버 to 서버
      - 앱 클라이언트
      - 웹 클라이언트
      - POST, PUT, PATCH : 메시지 바디를 통해 데이터 전송
      - GET : 조회, 쿼리 파라미터로 데이터 전송
      - Content-Type: application/json을 주로 사용(사실상 표준)
---
## HTTP API 설계 예시
- HTTP API - 컬렉션
  - POST 기반 등록 (ex. /member)
    - 클라이언트는 등록될 리소스의 URI를 모른다.
    - 서버가 새로 등록된 리소스 URI를 생성해준다.
    - 컬렉션 
      - 서버가 관리하는 리소스 디렉토리
      - 서버가 리소스의 URI를 생성하고 관리
      - 여기 컬렉션은 /member
- HTTP API - 스토어
  - PUT 기반 등록 (ex. /files/{filename})
    - 클라이언트가 리소스 URI를 알고 있어야 한다.
    - 클라이언트가 직접 리소스의 URI를 지정한다.
    - 스토어
      - 클라이언트가 관리하는 리소스 저장소
      - 클라이언트가 리소스의 URI를 알고 관리
      - 여기서 스토어는 /files
- HTML FORM 사용
  - GET, POST만 지원 -> ajax와 같은 기술을 사용해서 해결
  - 컨트롤 URI
    - GET, POST만 지원하므로 제약이 잇다. 
    - 제약을 해결하기 위해 동사로 된 리소스 경로 사용
    - POST의 /new, /edit, /delete가 컨트롤 URI
    - 최대한 HTTP 메서드를 활용하고 해결하기 애매한 경우에 사용.
- URI 설계 개념
  - 문서
    - 단일 개념 - 파일하나, 객체 인스턴스, 데이터베이스 row
  - 컬렉션
    - 서버가 관리하는 리소스 디렉터리 - 서버가 리소스 URI 생성하고 관리
  - 스토어
    - 클라이언트가 관리하는 자원 저장소
    - 클라이언트가 리소스 URI를 알고 관리
  - 컨트롤러, 컨트롤 URI
    - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
    - 동사를 직접 사용