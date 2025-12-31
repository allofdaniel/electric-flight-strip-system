# Electronic Flight Strip (EFS) System 🛬

## 프로젝트 개요

Electronic Flight Strip (전자 비행 진행표) 시스템은 항공교통관제탑(ATCT)에서 관제사들이 사용하는 디지털 비행 진행표 시스템입니다.

전통적인 종이 형태의 비행 진행표를 대체하여 실시간 데이터 표시, 업데이트, 공유 기능을 제공합니다.

### 핵심 기능

- ✈️ **실시간 비행 데이터 표시** - 항공편 정보, 경로, 고도, 속도 실시간 업데이트
- 📊 **감시 데이터 처리** (Surveillance Data Processing) - 레이더 데이터 실시간 통합
- ⚠️ **안전망 기능** (Safety Nets)
  - STCA (Short Term Conflict Alert) - 충돌 경보
  - MTCD (Medium Term Conflict Detection) - 충돌 감지
  - MSAW (Minimum Safe Altitude Warning) - 최소 안전 고도 경고
  - RAM (Route Adherence Monitoring) - 경로 일탈 감시
- 🔄 **관제사 간 데이터 공유** - 관제 위치 간 비행 정보 인계
- 🎙️ **음성 및 데이터 링크 통합** - CPDLC, ADS-C 지원
- 📹 **운영 기록 및 재생** - Recording & Playback
- 🔒 **높은 가용성 및 보안** - 99.9975% 가용성, 다중 중복화

## 프로젝트 폴더 구조

```
electric-flight-strip-system/
├── docs/                          # 설계 및 사양 문서
│   ├── EFS_System_Specifications.md     # 전체 시스템 사양 (종합 문서)
│   ├── ICAO_Standards_Reference.md      # ICAO 표준 참조
│   ├── Architecture_Design.md           # 시스템 아키텍처
│   └── API_Specifications.md            # REST API 명세
│
├── backend/                       # 백엔드 서버
│   ├── core/                      # 핵심 모듈
│   │   ├── flight-data-processor/       # 비행 데이터 처리
│   │   ├── surveillance-processor/      # 감시 데이터 처리 (ASTERIX)
│   │   ├── safety-nets/                 # 안전망 기능
│   │   └── conflict-detection/          # 충돌 감지 엔진
│   │
│   ├── communication/             # 통신 모듈
│   │   ├── asterix-handler/       # ASTERIX 레이더 데이터 처리
│   │   ├── aftn-handler/          # AFTN 메시지 처리
│   │   ├── cpdlc-handler/         # CPDLC 조종사 데이터링크
│   │   └── websocket-server/      # WebSocket 실시간 통신
│   │
│   ├── database/                  # 데이터베이스 레이어
│   │   ├── models/                # 데이터 모델
│   │   ├── migrations/            # DB 마이그레이션
│   │   └── queries/               # SQL 쿼리
│   │
│   ├── api/                       # REST API
│   │   ├── routes/                # API 라우트
│   │   ├── middleware/            # 미들웨어
│   │   └── controllers/           # 컨트롤러
│   │
│   ├── services/                  # 비즈니스 로직
│   │   ├── flight-service/        # 비행 데이터 서비스
│   │   ├── alert-service/         # 경고 서비스
│   │   └── notification-service/  # 알림 서비스
│   │
│   └── tests/                     # 테스트
│       ├── unit/                  # 단위 테스트
│       ├── integration/           # 통합 테스트
│       └── performance/           # 성능 테스트
│
├── frontend/                      # 프론트엔드 (React/Vue)
│   ├── public/
│   ├── src/
│   │   ├── components/            # UI 컴포넌트
│   │   │   ├── FlightStrip/       # 비행 진행표
│   │   │   ├── AlertPanel/        # 경고 패널
│   │   │   ├── StatusBar/         # 상태 바
│   │   │   └── ControlPanel/      # 제어 패널
│   │   │
│   │   ├── pages/                 # 페이지
│   │   │   ├── DepartureBoard/    # 출발 보드
│   │   │   ├── ArrivalBoard/      # 도착 보드
│   │   │   └── Monitor/           # 모니터링 화면
│   │   │
│   │   ├── services/              # API 클라이언트
│   │   │   ├── flight-service/
│   │   │   └── websocket-service/
│   │   │
│   │   ├── store/                 # 상태 관리 (Redux/Pinia)
│   │   │   ├── actions/
│   │   │   ├── mutations/
│   │   │   └── getters/
│   │   │
│   │   ├── styles/                # CSS/SCSS
│   │   │   ├── components/
│   │   │   └── variables.scss
│   │   │
│   │   └── utils/                 # 유틸리티
│   │       ├── formatters/
│   │       └── validators/
│   │
│   ├── tests/
│   └── package.json
│
├── deployment/                    # 배포 설정
│   ├── docker/
│   │   ├── Dockerfile.backend
│   │   ├── Dockerfile.frontend
│   │   └── docker-compose.yml
│   │
│   ├── kubernetes/
│   │   ├── backend-deployment.yaml
│   │   ├── frontend-deployment.yaml
│   │   └── persistent-volumes.yaml
│   │
│   └── nginx/
│       └── nginx.conf
│
├── tools/                         # 유틸리티 및 스크립트
│   ├── data-generators/           # 테스트 데이터 생성
│   ├── simulators/                # 항공기 시뮬레이터
│   └── monitoring/                # 모니터링 도구
│
├── config/                        # 설정 파일
│   ├── development.env
│   ├── production.env
│   └── application.yml
│
├── .github/                       # GitHub 설정
│   ├── workflows/                 # CI/CD 파이프라인
│   │   ├── build.yml
│   │   ├── test.yml
│   │   └── deploy.yml
│   └── ISSUE_TEMPLATE/
│
├── LICENSE
├── CONTRIBUTING.md
└── docker-compose.yml
```

## 주요 모듈 설명

### 1️⃣ Flight Data Processing Module
**비행 계획 데이터 처리**
- FPL (Flight Plan) 메시지 수신 (AFTN 형식)
- 데이터 검증 (ICAO Doc 4444 준수)
- 비행 진행표(Flight Strip) 자동 생성
- 실시간 데이터 업데이트 추적

**입력**: FPL 메시지 → **출력**: Flight Strip Data Structure

### 2️⃣ Surveillance Data Processor
**레이더 및 감시 데이터 처리**
- ASTERIX CAT 021 (Air Target Report) 처리
- 항공기 위치, 고도, 속도 추출
- 다중 레이더 소스 통합
- 추적 데이터 업데이트 (1-2초 주기)

**입력**: ASTERIX 메시지 → **출력**: Track Data with Position & Altitude

### 3️⃣ Safety Nets Module
**충돌 감지 및 경고 시스템**

| 기능 | 예측 시간 | 목적 |
|------|----------|------|
| **STCA** | 2-5분 | 즉각적 충돌 경고 |
| **MTCD** | 5-20분 | 조기 충돌 감지 |
| **MSAW** | 실시간 | 최소 안전 고도 확인 |
| **RAM** | 실시간 | 경로 이탈 감시 |

### 4️⃣ WebSocket Server
**실시간 양방향 통신**
- 관제사 워크스테이션과 서버 간 실시간 데이터 동기화
- 비행 진행표 자동 업데이트
- 알림 및 경고 즉시 전달

### 5️⃣ Frontend UI Components
**관제사 인터페이스**
- 비행 진행표 드래그 앤 드롭
- 색상 코딩 (GREEN=정상, YELLOW=경고, RED=위험)
- 실시간 상태 표시
- 검색 및 필터링 기능

## 기술 스택

### Backend
```
├── Language: Java 17+ / Python 3.10+ / Go
├── Framework: Spring Boot 3.0+ / FastAPI / Gin
├── Database: PostgreSQL (Real-time) + Redis (Cache)
├── Message Broker: RabbitMQ / Apache Kafka
├── Real-time Comm: Spring WebSocket / Socket.io
└── API: REST (Spring Web) / FastAPI / Gin
```

### Frontend
```
├── Framework: React 18+ or Vue.js 3
├── State Management: Redux / Pinia
├── Real-time: Socket.io Client
├── UI Library: Material-UI / Ant Design
└── Visualization: D3.js / Canvas API
```

### Infrastructure
```
├── Containerization: Docker
├── Orchestration: Kubernetes
├── Monitoring: Prometheus + Grafana
├── Logging: ELK Stack
└── CI/CD: GitHub Actions
```

## 개발 시작하기

### 필수 요구사항
- Java 17+ (Backend)
- Node.js 16+ (Frontend)
- Docker & Docker Compose
- PostgreSQL 13+
- Redis 6+

### 로컬 개발 환경 설정

```bash
# 저장소 클론
git clone https://github.com/allofdaniel/electric-flight-strip-system.git
cd electric-flight-strip-system

# 환경 설정
cp config/development.env .env

# Docker Compose로 시작
docker-compose up -d

# 백엔드 실행 (별도 터미널)
cd backend
mvn install
mvn spring-boot:run

# 프론트엔드 실행 (별도 터미널)
cd frontend
npm install
npm start
```

## 설계 및 사양 문서

### 📖 종합 문서
- **[EFS_System_Specifications.md](docs/EFS_System_Specifications.md)** - 전체 시스템 설계 및 사양
  - 시스템 아키텍처
  - 기능 요구사항
  - 데이터 구조
  - UI/UX 설계 원칙
  - 안전성 및 보안
  - 성능 요구사항
  - 통신 프로토콜
  - 관제사 워크플로우
  - 구현 참고사항

### 📚 추가 문서
- [아키텍처 설계](docs/Architecture_Design.md) - 시스템 아키텍처 상세 설명
- [ICAO 표준 참조](docs/ICAO_Standards_Reference.md) - 항공 표준 및 규정
- [API 명세](docs/API_Specifications.md) - REST API 문서

## 국제 표준 및 규정

### ICAO (국제민간항공기구)
- **ICAO Doc 4444** - Procedures for Air Navigation Services (Air Traffic Management)
- **ICAO Annex 11** - Air Traffic Services
- **ICAO Annex 14** - Aerodromes

### EUROCONTROL (유럽항공항법통제기구)
- **ATM Surveillance Specification**
- **Flexible Use of Airspace (FUA) Specification**
- **Data Assurance Levels (DAL) Specification**

### FAA (미국 연방항공청)
- **Terminal Flight Data Manager (TFDM)** - 현대화 프로젝트
- **NextGen Architecture**

### 기술 표준
- **ASTERIX** - All Purpose Surveillance Information Exchange
  - CAT 001: 1차 레이더 데이터
  - CAT 021: 2차 레이더/Mode-S/ADS-B 데이터
  - CAT 034: 레이더 시스템 설정

- **AFTN** - Aeronautical Fixed Telecommunication Network
  - FPL (Flight Plan) 메시지
  - CHG (Change) 메시지
  - ARR (Arrival) 메시지

- **CPDLC** - Controller-Pilot Data Link Communication
  - ATN 기반 양방향 데이터 통신

- **ADS-C** - Automatic Dependent Surveillance - Contract
  - 항공기 자동 위치 보고

### 안전성 표준
- **DO-178C** - Software Processes and Assurance
- **SAE ARP4754** - Certification Considerations for Airplane Systems
- **SAE ARP4761** - Reliability and Maintainability Guidelines

## 시스템 요구사항

### 성능 요구사항
| 항목 | 목표 | 최대 허용 | 우선순위 |
|------|------|----------|----------|
| 비행 진행표 표시 | < 100 ms | 200 ms | Critical |
| 데이터 업데이트 | < 1 s | 2 s | Critical |
| 경고 표시 | < 500 ms | 1 s | Critical |
| 사용자 입력 반응 | < 200 ms | 500 ms | High |

### 가용성 요구사항
- **목표 가용성**: 99.9975% (MTBF: 20,000 시간)
- **복구 시간**: MTTR ≤ 30분
- **다중 중복화**: 이중 LAN, 다중 데이터 입력, 워크스테이션 이중화

### 동시 처리 용량
- **동시 항공기**: 최소 500개
- **동시 비행 계획**: 최소 1,000개
- **동시 관제사**: 최소 8명 (기본), 최대 16명

## 보안

### 접근 제어
- 역할 기반 접근 제어 (RBAC)
- 다중 인증 (2FA)
- 생체 인증 지원

### 통신 보안
- **암호화**: TLS 1.3+, AES-256
- **무결성**: 디지털 서명, 메시지 인증 코드
- **감시**: 실시간 로깅, 감사 추적

### 보안 취약점 보고

보안 취약점을 발견하신 경우, 공개하지 마시고 다음으로 알려주세요:
- 📧 Email: allofdaniel1@gmail.com

## 기여하기

이 프로젝트에 기여하고 싶으신가요? [CONTRIBUTING.md](CONTRIBUTING.md)를 참조하세요.

### 기여 프로세스
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 라이센스

MIT License - [LICENSE](LICENSE) 파일 참조

## 문의 및 연락

- 📧 **Email**: allofdaniel1@gmail.com
- 🔗 **Blog**: https://lab201.tistory.com/
- 💼 **LinkedIn**: https://linkedin.com/in/kdaniel
- 🌐 **GitHub**: https://github.com/allofdaniel

## 감사의 말

이 프로젝트는 다음 표준 및 문헌을 참고하여 개발되었습니다:
- ICAO 국제민간항공기구
- EUROCONTROL 유럽항공항법통제기구
- FAA 미국 연방항공청
- 각국의 항공 관제 기관

---

**프로젝트 상태**: 🚧 개발 중 (Development)
**최종 수정**: 2024-12-25
**버전**: 0.1.0

<p align="center">
  Made with ❤️ for Air Traffic Control
</p>
