# Product Requirements Document: SHOP

> **Project Vision**
> 애플의 디자인 철학을 계승하여, 사용자에게는 간결한 경험을, 식당 관리자에게는 명확한 통계를 제공하는 미니멀리즘 식수 관리 시스템입니다.

---

## 1. Core Experience & Identity
- **Project Identity:** `application.yml`의 `app.project.name` 프로퍼티를 통해 동적 관리.
- **Design Principle:** - San Francisco 서체 계열의 정갈한 타이포그래피 활용.
  - 불필요한 테두리를 줄이고 여백(Padding)과 그림자(Elevation)를 이용한 레이어 구분.
  - 시스템 상태 변화(성공/실패)에 따른 부드러운 컬러 피드백.

## 2. Technical Stack
- **Backend:** Java 21 / Spring Boot 3.3 / Spring Data JPA
- **Database:** PostgreSQL 16 (Containerized)
- **Real-time:** Spring WebSocket (STOMP)
- **Security:** Spring Security (Phone-based Auth) + AES256 Encryption
- **Frontend:** Thymeleaf + Bootstrap 5 (Customized for Apple-like UI)

## 3. Domain Model

### 3.1 Organization & People
- **Company:** 기업 정보 (ID, 명칭, 사업자번호)
- **User:** 직원 정보 (ID, 전화번호, 성함, 소속 회사, 권한)
  - *Relation: Company (1) : User (N)*

### 3.2 Menu & Service
- **DailyMenu:** 날짜별 식단 (날짜, 메인 메뉴, 서브 메뉴 6개, 식단 이미지)
- **Order:** 식수 기록 (유저 정보, 회사 정보, 식사 일시)
  - *Relation: User (1) : Order (N) / Company (1) : Order (N)*

## 4. Key Implementation Rules

### 4.1 Conditional UI (2-Hour Rule)
- 사용자가 메인 페이지 접속 시, 최근 2시간 이내의 주문 기록을 확인합니다.
- **Active State:** 2시간 이내 기록 존재 시, QR 코드를 숨기고 **"식사 완료 정보"**를 정중하게 표시합니다.
- **Idle State:** 기록이 없거나 2시간 경과 시, **"보안 암호화 QR 코드"**를 즉시 생성하여 노출합니다.

### 4.2 Seamless Scanning (Tablet Side)
- 스캔 전용 태블릿은 URL 인식 즉시 백그라운드에서 주문을 처리합니다.
- 결과 화면은 다음과 같은 시각적 위계를 가집니다:
  1. **회사명:** 가장 크고 굵은 텍스트 (Bold Header)
  2. **금일 누적 인원:** 명확한 강조 (Primary Color)
  3. **사용자 정보:** 가독성 있는 보조 텍스트 (Secondary Text)

### 4.3 Real-time Sync (WebSocket)
- 주문이 발생하면 서버는 사용자별 전용 채널로 즉시 신호를 보냅니다.
- 사용자의 모바일 페이지는 별도의 수동 조작 없이도 **자동 새로고침**되어 화면 상태가 업데이트됩니다.

### 4.4 Resource Management
- **File System:** 공통 `FileService`를 통해 이미지를 관리하며, 설정 파일에 정의된 로컬 경로를 참조합니다.
- **Aggregation:** `OrderRepository`에서 당일 회사별 누적 식수를 실시간 집계하는 최적화된 쿼리를 사용합니다.

---

## 5. Development Roadmap (AI Scaffold Instruction)

1. **Phase 1 (Environment):** 프로젝트 명칭 관리용 Config 구성 및 Docker 기반 DB 연결.
2. **Phase 2 (Security):** 전화번호 기반 인증 시스템 및 URL 파라미터 AES256 암호화 모듈 구현.
3. **Phase 3 (Core Logic):** 주문 저장 시 WebSocket 알림 전송 및 2시간 이내 식사 여부 판단 로직 개발.
4. **Phase 4 (UI/UX):** Apple 디자인 시스템 스타일을 적용한 Thymeleaf 템플릿 제작 (Main, Scan Result).

## 6. 시큐어코딩 적용
