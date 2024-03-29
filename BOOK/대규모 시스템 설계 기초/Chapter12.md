# 12장. 채팅 시스템 설계

## 설계 범위 확정
- 응답지연이 낮은 일대일 채팅 기능
- 최대 100명까지 참여할 수 있는 그룹 채팅 기능
- 사용자의 접속상태 표시 기능
- 다양한 단말 지원, 하나의 계정으로 여러 단말에 동시 접속 지원
- 푸시 알림

## 기본 개념
- 클라이언트는 서로 직접 통신하지 않는다.-> 채팅 서비스와 통신한다.
- 채팅 서비스
  - 클라이언트들로부터 메시지 수신
  - 메시지 수신자 결정 및 전달
  - 수신자가 오프라인 사애일 경우 접속할 때까지 메시지 보관
- 서버가 연결을 만드는 것처럼 동작할 수 있도록 하기 위한 기법(HTTP 클라이언트가 연결을 만드는 프로토콜)
  - 폴림
    - 클라이언트가 주기적으로 서버에게 새 메시지가 있느냐고 물어보는 방법
    - 폴링을 자주 할수록 비용이 올라간다.
    - 메시지가 없을 경우 서버 장비가 불필요하게 낭비됨.
  - 롱 폴링
    - 클라이언트는 새 메시지가 반환되거나 타임아웃 될 때까지 연결을 유지한다.
    - 클라이언트는 새 메시지를 받으면 기존 연결을 종료하고 서버에 새로운 요청을 보냄
    - 서버가 무상태 서버일 경우 메시지 보내는 클라이언트와 수신 클라이언트가 다른 서버로 접속할 수 있음.
    - 서버는 클라이언트가 연결 해제했는지 알 방법이 없다.
    - 여전히 비효율적
  - 웹 소켓
    - 비동기 메시지를 보낼떄 가장 널리 사용하는 기술
    - 클라이언트가 연결 시작
    - 한번 맺어진 연결은 항구적이며 양방향
    - 서버는 클라이언트에게 비동기적으로 메시지를 전송할 수 있다.
    - 웹 소켓 연결은 항구적으로 유지되기 때문에 서버 측에서 연결 관리를 효율적으로 해야됨


## 개략적 설계안
- 고려사항
  - 무상태 서비스
  - 상태 유지 서비스
  - 제3자 서비스 연동
  - 규모 확장성

- 실시간 메시지를 주고받기 위해 클라이언트는 채팅 서버와 웹 소켓 연결을 끊지 않고 유지한다.
  - 채팅 서버는 클라이언트 사이에 메시지 중계 역할
  - 접속상태 서버는 사용자의 접속 여부를 관리
  - API 서버는 로그인, 회원가입, 프로파일 변경 등 그 외 전부 처리
  - 알림서버는 푸시 알림을 보낸다.
  - 키-값 저장소에는 채팅 이력을 보관한다.

- 저장소
  - 사용자 프로파일, 설정, 친구 목록등 일반적 데이터
    - 관계형 데이터베이스에 보관
  - 채팅이력
    - 읽기 쓰기 비율이 1:1 정도이다.
    - 키-값 저장소 추천
      - 수평적 규모확장이 쉽다.
      - 데이터 접근 지연시간이 낮다
      - 관계형 데이터베이스는 롱테일에 해당하는 부분을 잘 처리하지 못하는 경향이 있음. 무작위 접근 처리 비용이 늘어난다.

- 메시지 ID
  - 메시지 ID는 고유해야 한다.
  - 시간 순서와 일치해야 한다.
  - auto_increment
  - 스노플레이크
  - 지역적 순서 번호 생성기 
## 상세설계
- 서비스 탐색
  - 가장 적합한 채팅 서버를 추천하는 것
  - 클라이언트 위치, 서버 용량 고려
  - 아파치 주키퍼가 예
  - 사용 가능한 모든 서버를 등록하고, 접속시 기준에 따라 골라준다.

- 메시지 흐름
  - 1:1 채팅 메시지 처리 흐름
    1. 사용자가 A가 채팅 서버 1로 메시지 전송
    2. 채팅서버 1은 ID 생성기를 사용해 해당 메시지의 ID 결정
    3. 채팅 서버 1은 해당 메시지를 메시지 동기화 큐로 전송
    4. 메시지가 키-값 저장소에 보관됨
    5. (a) 사용자 B가 접속 중인 경우 메시지는 사용자 B가 접속 중인 패팅 서버로 전송됨. 
       (b)접속중이 아니라면 푸시 알림 메시지를 알림 서버로 보냄
    6. 채팅 서버 2는 메시지를 사용자 B에게 전송. 사용자 B와 채팅 서버 2사이에는 웹소켓 연결이 있는 상태이므로 그것을 이용
  - 여러 단말 사이의 메시지 동기화
    - 연결 단말마다 별도의 소켓 연동을 해야됨
    - 각 단말은 cur_max_message_id라는 변수를 유지함. 단말에서 관측된 최신 메시지를 추적한는 용도'
    - 새 메시지 감지 방법
      - 수신자 ID가 현재 로그인한 사용자 ID와 같다
      - 키-값 저장소에 보관되 메시지로 그 ID가 cur_max_message_id보다 크다.
  - 소규모 그룹 채팅에서의 메시지 흐름
    - 메시지 동기화 큐를 사용한다.
    - 메시지를 보내면 수신자의 메시지 동기화 큐에 복사도미
    - 큐를 사용자의 메시지 수신함으로 생각해도 됨
    - 한 수신자느 여러 사용자로부터 오는 메시지를 수신할 수 잇어야 함.
    - 메시지 동기화 큐는 여러 사용자로부터 오는 메시지를 수신한다.

- 접속상태 표시
  - 접속상태 서버는 클라이언트와 웹소켓으로 통신하는 실시간 서비스의 일부
  - 사용자 로그인
    - 접속상태 서버는 사용자의 상태와 last_active_at 타임스탬프 값을 키-값 저장소에 보관한다.
  - 로그아웃
    - 키-값 저장소에 상태가 offline으로 저장된다.
  - 접속 장애
    - 박동 검사
      - 클라이언트가 주기적으로 박동 이벤트를 접속상태 서버로 보낸다.
      - 마지막 이벤트를 받고 x초 이내에 다른 박동 메시지를 받으면 온라인 상태로 유지한다. 그렇지 않으면 오프라인.
  - 상태 정보의 전송
    - 상태정보 서버는 발행-구독 모델을 사용한다.
    - 각각의 친구관계마다 채널을 하나씩 두는 것
    - 크기가 작을 떄 효과적
    - 채팅에 입장하는 순간에만 상태 정보를 읽거나, 갱신을 원하면 수동으로 하도록 유도한다.

