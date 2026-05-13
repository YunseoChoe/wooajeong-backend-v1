# 블록체인·MCP 기반 안심 중고 거래 통합 플랫폼  

> **우아정** | **우**리 **아**무 걱정없이 **정**확하게 거래해요 <br>
> 개인 간 중고 거래의 사기·허위 광고·시세 불투명 문제를 해결하기 위해 기획한 서비스

<p align="center">
  <img width="240" alt="Image" src="https://github.com/user-attachments/assets/84024279-e487-458e-88db-a8fa9515299f" />

</p>




## 목차

1. [서비스 개요](#1-서비스-개요)
2. [기술 스택](#2-기술-스택)
3. [API 명세](#3-api-명세)
4. [주요 개발 내용](#4-주요-개발-내용)
5. [시스템 아키텍처](#5-시스템-아키텍처)
6. [ERD](#6-erd)
7. [트러블 슈팅](#7-트러블-슈팅)
8. [Demo](#8-demo)
9. [성과 및 결과](#9-성과-및-결과)
10. [역할 분담](#10-역할-분담)

<br>

## 1. 서비스 개요
개인 간 중고 거래에서는 사기, 허위 광고, 상품 미수령 등으로 인한 피해가 빈번하게 발생하고, 거래 상대방의 신뢰성을 확인하기 어렵습니다. <br>
또한 시세 정보가 불투명하여 합리적인 가격 판단이 어렵고, 대부분 사용자가 현장 직거래만 선호하면서 거래 편의성이 제한됩니다. <br>
따라서 해당 플랫폼으로 안전하고 신뢰할 수 있으며 편리한 개인 간 중고 거래 환경을 제공하고자 합니다.

<br>

## 2. 기술 스택

| Backend | Security | Database | Deployment | Other |
|:------:|:------:|:------:|:------:|:------:|
|<img src="https://smartcart-s3-bucket.s3.ap-northeast-2.amazonaws.com/badge_SpringBoot.svg" alt="[ Spring Boot ]"/><br><img src="https://img.shields.io/badge/Java-007396?style=flat-square&logo=Java&logoColor=white"/><br><img src="https://img.shields.io/badge/modelcontextprotocol-000000?style=flat-square&logo=modelcontextprotocol&logoColor=white"/><br><img src="https://smartcart-s3-bucket.s3.ap-northeast-2.amazonaws.com/badge_STOMP.svg" alt="STOMP"/><br><img src="https://img.shields.io/badge/RabbitMQ-FF6600?style=flat-square&logo=RabbitMQ&logoColor=white"/><br><img src="https://img.shields.io/badge/Gradle-02303A?style=flat-square&logo=Gradle&logoColor=white"/>|<img src="https://smartcart-s3-bucket.s3.ap-northeast-2.amazonaws.com/badge_SpringSecurity.svg" alt="[ Spring Security ]"/><br><img src="https://smartcart-s3-bucket.s3.ap-northeast-2.amazonaws.com/badge_JSONWebToken.svg" alt="[ JSON Web Token ]"/><br><img src="https://img.shields.io/badge/OAuth2-3423A6?style=flat-square&logo=Authelia&logoColor=white"/>|<img src="https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=MySQL&logoColor=white"/><br><img src="https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=MongoDB&logoColor=white"/>|<img src="https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=AmazonAWS&logoColor=white"/><br><img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=Docker&logoColor=white"/><br><img src="https://smartcart-s3-bucket.s3.ap-northeast-2.amazonaws.com/badge_GithubActions.svg" alt="[ Github Actions ]"/>|<img src="https://img.shields.io/badge/OpenAI-412991?style=flat-square&logo=OpenAI&logoColor=white"/><br><img src="https://img.shields.io/badge/Kakao-FFCD00?style=flat-square&logo=KakaoTalk&logoColor=black"/>|
```
- Backend : Spring Boot(3.4.3), Java(17), MCP
- Security : Spring Security, JWT, OAuth2
- Database : MySQL, MongoDB
- Deployment : AWS, Docker, Github Actions
- External API : OpenAI API
```
<!--
| 분류 | 기술 |
|------|------|
| **Language** | Java 17 |
| **Framework** | Spring Boot 3.4.3 |
| **실시간 통신** | STOMP over WebSocket (SockJS), RabbitMQ |
| **보안** | Spring Security, JWT (jjwt 0.11.2), OAuth2 (Kakao) |
| **DB (관계형)** | MySQL 8 (AWS EC2) |
| **DB (문서형)** | MongoDB Atlas |
| **ORM** | Spring Data JPA (MySQL), Spring Data MongoDB |
| **배포** | AWS EC2, Docker, GitHub Actions (CI/CD) |
| **빌드** | Gradle |
| **외부 연동** | Kakao OAuth2 API, OpenAI API, MCP(Model Context Protocol) |
-->

<br>

## 3. API 명세

> **Base URL**: `https://yunseo.store`

### 인증 (Auth)

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `GET` | `/api/oauth/kakao` | 카카오 소셜 로그인 |
| `GET` | `/api/user` | 내 프로필 조회 |
| `DELETE` | `/api/user/delete` | 회원 탈퇴 |

<!--

**`GET /api/oauth/kakao`** — 카카오 Authorization Code로 JWT 발급

| Query | 타입 | 설명 |
|-------|------|------|
| `code` | String | 카카오 OAuth2 인가 코드 |

```json
// Response 200
{
  "accessToken": "eyJhbGciOiJIUzUxMiJ9...",
  "refreshToken": "eyJhbGciOiJIUzUxMiJ9..."
}
```

**`GET /api/user`** — 로그인한 사용자 정보 반환

```json
// Response 200
{
  "user_id": 1,
  "email": "user@kakao.com",
  "nickname": "홍길동"
}
```
-->

### 채팅방 (Room)

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `POST` | `/room/make` | 채팅방 생성 |
| `GET` | `/room/join` | 링크로 채팅방 조회 |
| `GET` | `/room/my` | 내 채팅방 목록 조회 |

<!--
**`POST /room/make?productId={productId}`** — 상품 ID 기반 채팅방 생성, RabbitMQ Queue 동적 등록

```json
// Response 200
{
  "roomId": 2,
  "roomLink": "550e8400-e29b-41d4-a716-446655440000",
  "creatorId": 1,
  "opponentId": null,
  "createdAt": "2025-05-13T10:00:00"
}
```

**`GET /room/join?link={roomLink}`** — UUID 링크로 채팅방 정보 조회 (인증 불필요)

**`GET /room/my`** — creatorId 또는 opponentId가 본인인 채팅방 전체 반환

-->


### 채팅 메시지 (Chat)

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `GET` | `/chat/messages` | 채팅 내역 조회 |

<!--
**`GET /chat/messages?roomId={roomId}`** — 해당 채팅방의 메시지를 시간 오름차순으로 반환 (MongoDB)

```json
// Response 200
[
  {
    "id": "683228a1f4e2a1b2c3d4e5f6",
    "roomId": 2,
    "senderId": 1,
    "receiverId": 3,
    "content": "안녕하세요, 아직 판매 중인가요?",
    "sendTime": "2025-05-13T10:05:00",
    "isRead": false
  }
]
```
-->

### 관심 매물 (Search)

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `POST` | `/searches/save` | 관심 매물 등록 |
| `GET` | `/searches/list` | 유저별 관심 매물 조회 |
| `DELETE` | `/searches/delete` | 관심 매물 삭제 |

<!--
**`POST /searches/save`** — Request Body

```json
{
  "user_id": "1",
  "keyword": "아이폰 15",
  "location": "서울 강남구",
  "min_price": "500000",
  "max_price": "1000000"
}
```

**`GET /searches/list?user_id={userId}`** — 해당 유저의 관심 매물 목록 반환  
**`DELETE /searches/delete?id={id}`** — Search 테이블 기본키로 삭제

-->

### 실시간 채팅 — STOMP WebSocket



| 구분 | 경로 | 설명 |
|------|------|------|
| **연결** | `wss://yunseo.store/ws` | SockJS WebSocket 연결 |
| **구독** | `/exchange/chat.exchange/room.{roomId}` | 채팅방 메시지 수신 |
| **발행** | `/app/chat.send` | 채팅 메시지 전송 |

<!--

**발행 Payload**

```json
{
  "roomId": 2,
  "senderId": 1,
  "receiverId": 3,
  "content": "안녕하세요!",
  "sendTime": "2025-05-13T10:05:00"
}
```

**전체 흐름**

```
① CONNECT    wss://.../ws
② SUBSCRIBE  /exchange/chat.exchange/room.{roomId}
③ SEND       /app/chat.send  →  서버: MongoDB 저장 + RabbitMQ 발행
④ BROADCAST  구독 중인 클라이언트에게 실시간 전달
```
-->

<br>

## 4. 주요 개발 내용

### 4-1. 카카오 소셜 로그인 & JWT 인증

[//]: # (- Kakao OAuth2 Authorization Code Flow를 직접 구현 &#40;`AuthController`&#41;)
- 카카오 access token으로 사용자 정보 조회 후 DB에 신규 회원 자동 저장
- 로그인 성공 시 Access Token(7일)과 Refresh Token(14일) 발급
- 이후 모든 API 요청은 `JwtAuthenticationFilter`가 헤더의 Bearer 토큰을 검증하여 `SecurityContext`에 인증 정보 저장

### 4-2. 실시간 1:1 채팅

- STOMP + RabbitMQ + MongoDB를 결합한 실시간 메시지 처리 구조
- 채팅방 생성 시 RabbitMQ Queue를 동적으로 생성 (`RoomQueueManager`)
- 메시지 수신 → MongoDB 영구 저장 + RabbitMQ 발행 → STOMP 구독자에게 전달
- 채팅방별 라우팅 키(`room.{roomId}`) 기반 Topic Exchange 설계

### 4-3. 관심 매물 등록 및 조회

- 사용자가 키워드·지역·가격 범위를 지정해 관심 매물 조건 저장
- MCP(Model Context Protocol) 기반 AI 분석으로 적절한 시세 알림 제공

[//]: # (- CRUD API 제공 &#40;`SearchController`&#41;: 저장 / 유저별 조회 / 삭제)

### 4-4. 채팅방 관리

- 상품 ID 기반 채팅방 생성 및 고유 링크(UUID) 발급
- 링크 공유로 상대방 입장 가능 (인증 불필요 엔드포인트로 분리)

[//]: # (- 로그인 사용자의 참여 채팅방 전체 조회 &#40;`/room/my`&#41;)

### 4-5. CI/CD 파이프라인

- GitHub Actions: `main` 브랜치 push 시 Docker 이미지 자동 빌드 → DockerHub 푸시
- AWS EC2 Self-Hosted Runner가 최신 이미지를 Pull 후 컨테이너 교체 배포

[//]: # (- DB 접속 정보 등 민감 값은 GitHub Secrets → Docker `--build-arg`로 주입)

<br>

## 5. 시스템 아키텍처

<img width="2880" height="1619" alt="시스템아키텍처" src="https://github.com/user-attachments/assets/629c365e-065b-4ebf-9022-fbcb6e42f648" />

<!--
```
[Client (SockJS/STOMP)]
        │  STOMP /app/chat.send
        ▼
[StompChatController]
        │  ① MongoDB 영구 저장
        │  ② RabbitMQ chat.exchange에 발행 (routingKey: room.{roomId})
        ▼
[RabbitMQ - Topic Exchange: chat.exchange]
        │  queue: chat.room-{roomId}
        ▼
[ChatMessageConsumer (SimpleMessageListenerContainer)]
        │  STOMP /exchange/chat.exchange/room.{roomId}
        ▼
[구독 중인 Client에게 메시지 전달]
```


**채팅방 생성 흐름**

```
POST /room/make?productId={id}
        │
        ├─ MySQL: ChatRoom 저장 (roomId, roomLink, creatorId)
        ├─ RoomQueueManager: RabbitMQ Queue + Binding 동적 생성
        └─ ChatMessageConsumer: 해당 Queue 리스너 동적 등록 및 시작
```
-->

<br>

## 6. ERD

<img width="1090" height="376" alt="image" src="https://github.com/user-attachments/assets/f1ca38de-59ed-475e-a8fe-63a3a9d873f4" />

---

## 7. 트러블 슈팅

> 📝 [상세 내용 블로그 참고](https://velog.io/@choeyunseo/프로젝트)

### 7-1. 순수 WebSocket → STOMP 도입

**문제**: `TextWebSocketHandler`로 세션 맵을 직접 관리하면서 Pub/Sub 불가, 수평 확장 불가, 메시지 형식 미표준화 문제 발생

**해결**: STOMP + SockJS 도입으로 세션 관리 코드 제거, 채팅방 단위 구독 구조 확립

<br>

### 7-2. 정적 Queue → RabbitMQ 동적 Queue 생성

**문제**: 정적 Queue 선언 방식은 채팅방 증가 시 코드 수정 필요, 사용자 단위 큐 구조로 다중 채팅방 라우팅 불가

**해결**: 채팅방 생성 시점에 Queue 동적 생성, `TopicExchange` + 라우팅 키 패턴으로 Exchange 단일 유지

<br>

### 7-3. 채팅 메시지 DB 분리 — MySQL + MongoDB

**문제**: 채팅 메시지와 정형 데이터(회원, 채팅방)를 MySQL 단일 DB로 관리 시 구조적 비효율 발생

**해결**:
- **MySQL**: 회원, 채팅방 등 관계형 정형 데이터
- **MongoDB**: 채팅 메시지 등 비정형 데이터

<br>

## 8. Demo
<details>
  <summary>Click!</summary>

- **매물 검색, 관심 매물 등록 및 알림**

  ![search_gif](https://github.com/user-attachments/assets/0c25591a-f192-4db0-a934-69413e501fb5)

- **구매자 & 판매자 실시간 채팅**

  ![chatting_gif](https://github.com/user-attachments/assets/8b7701d8-e6bb-415e-9997-47b76ca9f0fb)

- **USDT 충전**

  ![search_notice_gif](https://github.com/user-attachments/assets/0b58e940-9df4-4d55-9e1d-3c3fcadbde1b)

</details>

<br>

## 9. 성과 및 결과

### 기술적 성과
- STOMP → RabbitMQ → STOMP 구조로 실시간 양방향 채팅 파이프라인 구현
- 채팅방 생성 시 Queue/Binding 동적 생성으로 코드 변경 없는 확장 구조 완성
- MySQL + MongoDB 용도별 분리로 데이터 특성에 맞는 저장소 선택
- Kakao OAuth2 + Access/Refresh Token 이중 구조로 보안성과 UX 확보
- GitHub Actions → DockerHub → EC2 Self-Hosted Runner 자동 빌드·배포 파이프라인 구축

### 경험 및 학습
- Pub/Sub 아키텍처 직접 설계·구현으로 Message Broker 패턴 실무 이해
- WebSocket → STOMP 전환
- 데이터 모델링 단계에서 RDB / NoSQL 특성 비교 및 최적화
- Docker + GitHub Actions 기반 CI/CD 파이프라인 구축 경험

<br>

## 10. 역할 분담

| 이름 | 역할 | 
| --- | --- | 
| 장성원 (팀장) | AI, 블록체인 |
| 권인우 | 프론트엔드 |
| 최윤서 | 백엔드 | 
