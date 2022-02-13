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
    |--|
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

