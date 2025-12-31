# Electronic Flight Strip (EFS) System
## 종합 기술 명세서 및 개발 가이드

**작성일:** 2025.12.31  
**버전:** 1.0  
**대상:** 원격관제탑(Remote Tower) 시스템 기반 EFS 소프트웨어 개발

---

## 1. Executive Summary

Electric Flight Strip(EFS)은 종이 비행편성표(Paper Flight Progress Strip)를 디지털화하여 관제사들이 실시간으로 항공기 정보를 관리할 수 있는 핵심 소프트웨어 시스템이다. 본 문서는 관제탑 운영 규정, ICAO 국제 기준, 그리고 최신 A-SMGCS(Advanced Surface Movement Guidance and Control Systems) 사양을 기반으로 EFS 시스템의 전체 요구사항을 정의한다.

### 1.1 시스템의 핵심 목표

- **실시간 항공기 추적**: 관제 영역 내 모든 항공기 및 차량의 위치, 상태, 계획 정보를 실시간 표시
- **관제사 워크로드 감소**: 종이 비행편성표 작성, 수정, 정렬의 수작업 제거
- **안전성 강화**: 충돌 감지(Conflict Detection), 활주로 침범 경보(Runway Incursion Alert)
- **운영 효율성 개선**: 자동화된 경로 계획(Automated Routing), 출발 관리(Departure Management)
- **다중공항 운영 지원**: 원격관제탑 환경에서 한 명의 관제사가 여러 공항 동시 운영 가능
- **국제 표준 준수**: ICAO Doc 4444(PANS-ATM), EUROCAE ED-87B(A-SMGCS MASPS), EUROCONTROL 사양 준수

---

## 2. 시스템 개요 및 아키텍처

### 2.1 EFS의 정의

**Electronic Flight Strip (전자 비행편성표)**
- 계획된 및 현재 항공기 비행계획 데이터를 포함하는 전자 문서
- 관제사가 항공기를 추적, 관리, 조종하는데 필요한 정보를 표시
- 실시간으로 갱신되며 다른 ATM 시스템(레이더, 감시 시스템)과 자동 연동

### 2.2 종이 비행편성표 대비 전자비행편성표의 장점

| 항목 | 종이 비행편성표 | 전자 비행편성표 (EFS) |
|------|----------------|----------------------|
| **정보 공유** | 수동 전달, 느림 | 자동 동기화, 실시간 |
| **업데이트** | 수기로 작성, 오류 가능 | 자동 입력, 검증 가능 |
| **데이터 정확도** | 필기체 판독 오류 | 디지털 정확도 100% |
| **검색 속도** | 수작업 정렬 필요 | 자동 분류, 필터링 |
| **예측 기능** | 없음 | 충돌감지, 경고 가능 |
| **물리적 소비** | 종이, 잉크, 보관 공간 | 디지털 저장 |
| **감사 기록** | 제한적 | 완전 감사 추적(Audit Trail) |

### 2.3 EFS 시스템 아키텍처

```
┌─────────────────────────────────────────────────────────┐
│            Controller HMI (관제사 인터페이스)              │
│  ┌──────────────────────────────────────────────────┐  │
│  │   Electronic Flight Strip Display                │  │
│  │   • Strip Bays (Pending, Active, Inbound, etc)  │  │
│  │   • Real-time data editing & visualization      │  │
│  └──────────────────────────────────────────────────┘  │
└──────────────┬──────────────────────────────────────────┘
               │
      ┌────────┴────────────────────────┬────────────┐
      │                                  │            │
      ▼                                  ▼            ▼
┌────────────────┐          ┌──────────────────┐  ┌─────────────┐
│  Surveillance  │          │  Routing/        │  │  Guidance   │
│   Function     │          │  Planning        │  │  Function   │
│                │          │  (DMAN, Taxi)    │  │  (CPDLC)    │
│ • SSR/PSR      │          │                  │  │             │
│ • MLAT         │          │ • Departure Mgmt │  │ • Data Link │
│ • ADS-B        │          │ • Route Planning │  │ • Pre-dep   │
│ • Data Fusion  │          │ • Sequencing     │  │   Clearance │
└────────────────┘          └──────────────────┘  └─────────────┘
      │                            │                    │
      └────────────────┬───────────┴────────────────────┘
                       │
              ┌────────▼───────────┐
              │  Control Function  │
              │                    │
              │ • Conflict Alert   │
              │ • Runway Incursion │
              │ • Separation Check │
              └────────────────────┘
```

### 2.4 핵심 구성 요소

1. **Surveillance Function (감시 기능)**
   - 항공기 위치 추적 (Primary, Secondary Radar, ADS-B)
   - 데이터 융합 (Data Fusion)
   - 목표물 보고 (Target Reporting)

2. **Routing/Planning Function (경로/계획 기능)**
   - 택시 경로 계획 (Taxi Route Planning)
   - 출발 관리 (Departure Management - DMAN)
   - 우선순위 결정 (Sequencing)

3. **Guidance Function (안내 기능)**
   - 항공기 안내 (Aircraft Guidance)
   - 데이터 링크 (Data Link - CPDLC)
   - 사전 출발 정보 (Pre-Departure Clearance)

4. **Control Function (통제 기능)**
   - 충돌 감지 (Conflict Detection)
   - 활주로 침범 경보 (Runway Incursion Alert)
   - 분리 확인 (Separation Assurance)

5. **Controller HMI (관제사 인터페이스)**
   - Electronic Flight Strip Display
   - 관제사 입력 장치
   - 경고 및 알람 시스템

---

## 3. EFS 기능 요구사항 상세 분석

### 3.1 비행편성표(Flight Strip) 데이터 구조

#### 3.1.1 최소 필수 데이터 필드

비행편성표는 다음의 필수 정보를 포함해야 한다:

```
┌─────────────────────────────────────────────────────┐
│  Flight Strip Data Structure                        │
├─────────────────────────────────────────────────────┤
│                                                     │
│  HEADER SECTION (헤더 섹션)                         │
│  ├─ Aircraft Call Sign (항공기 호출부호)           │
│  ├─ Aircraft Type (항공기 형식)                     │
│  ├─ Equipment Suffix (장비 접미사)                 │
│  └─ Transponder Code (트랜스폰더 코드)             │
│                                                     │
│  FLIGHT PLAN SECTION (비행계획 섹션)               │
│  ├─ Departure Aerodrome (출발공항)                │
│  ├─ Destination Aerodrome (도착공항)              │
│  ├─ Alternate Aerodromes (대체공항)               │
│  ├─ Proposed Departure Time (제안된 출발시각)     │
│  ├─ Estimated Time of Departure (예상출발시각)   │
│  ├─ Cruise Level (순항고도)                       │
│  ├─ Flight Plan Route (비행계획 경로)             │
│  └─ Special Handling (특수 처리 사항)             │
│                                                     │
│  CURRENT STATUS SECTION (현황 섹션)                │
│  ├─ Current Position (현재위치)                    │
│  ├─ Heading (침로)                                 │
│  ├─ Speed (속도)                                   │
│  ├─ Altitude/Flight Level (고도)                  │
│  ├─ Rate of Climb (상승률)                        │
│  └─ Squawk Code (응답기 코드)                     │
│                                                     │
│  CLEARANCE SECTION (허가 섹션)                     │
│  ├─ Assigned Heading (지정침로)                    │
│  ├─ Assigned Altitude (지정고도)                   │
│  ├─ Assigned Speed (지정속도)                      │
│  ├─ Assigned Route (지정경로)                      │
│  ├─ Clearance Limit (허가 한계)                   │
│  └─ Clearance Time (허가 시각)                    │
│                                                     │
│  HANDOVER/COORDINATION SECTION (이양/조정 섹션)   │
│  ├─ Previous Controller (이전 관제사)              │
│  ├─ Next Controller Position (다음 관제 포지션)   │
│  ├─ Coordination Status (조정 상태)               │
│  └─ QNH / Altimeter Setting (기압고도계 설정)    │
│                                                     │
│  SPECIAL NOTES SECTION (특수 주기 섹션)           │
│  ├─ Wake Turbulence Category (후류 난기류 범주)  │
│  ├─ RVR Conditions (활주로시정)                   │
│  ├─ ATIS Received (ATIS 수신 확인)               │
│  ├─ Ready to Push (출발 대기 상태)               │
│  └─ Actual Time of Departure (실제 출발시각)     │
│                                                     │
└─────────────────────────────────────────────────────┘
```

#### 3.1.2 표준화된 약자 및 기호 (ICAO Doc 4444 기준)

EFS는 관제사들이 인식할 수 있는 표준화된 약자를 사용해야 한다:

| 약자 | 의미 | 예시 |
|------|------|------|
| **EOBT** | Estimated Off-Block Time (예상 이륙 시각) | 1045 |
| **CTOT** | Calculated Time Over Threshold (계산된 임계값 통과 시각) | 1050 |
| **TSAT** | Target Stand Arrival Time (목표 스탠드 도착 시각) | 0930 |
| **TTOT** | Target Take-Off Time (목표 이륙 시각) | 1052 |
| **RVR** | Runway Visual Range (활주로시정) | 1200m |
| **CAVOK** | Ceiling And Visibility OK (운고와 시정 양호) | - |
| **NOSIG** | No Significant Change (유의한 변화 없음) | - |
| **WTC** | Wake Turbulence Category (후류 난기류 범주) | H/M/L |

### 3.2 비행편성표 유형 (Strip Types)

관제 업무에 따라 다양한 형태의 비행편성표가 필요하다:

#### 3.2.1 출발편성표 (Departure Strip)
- **용도**: 출발 항공기 관리
- **색상**: 노란색 또는 흰색 (관제 규정에 따라)
- **정렬 기준**: EOBT(예상 이륙 시각)
- **관제 단계**:
  1. 비행계획 제출 → 초기화 (Pending)
  2. 출발 대기 승인 → "Ready to Push"
  3. 스탠드 이탈 → ATO(Actual Time Off-Block) 기록
  4. 택시 중 → 조향, 속도 지시
  5. 활주로 대기 → Hold 또는 Line-up
  6. 이륙 승인 → ATD(Actual Time of Departure) 기록

#### 3.2.2 도착편성표 (Arrival Strip)
- **용도**: 도착 항공기 관리
- **색상**: 파란색 또는 흰색
- **정렬 기준**: ETA(예상 도착 시각)
- **관제 단계**:
  1. 진입 공역 이양 → "Expect landing"
  2. 최종 진입 → "Clear to land"
  3. 착륙 → ATA(Actual Time of Arrival) 기록
  4. 활주로 이탈 → Runway Exit 확인
  5. 택시 → "Contact Ground"

#### 3.2.3 통과편성표 (Overfly Strip)
- **용도**: 통과 항공기 관리
- **색상**: 녹색
- **정렬 기준**: ETA

#### 3.2.4 활주로 횡단편성표 (Runway Crossing Strip)
- **용도**: 활주로를 횡단하는 차량 또는 항공기
- **색상**: 빨간색
- **특징**: 활주로 사용 불가 상태 표시

#### 3.2.5 지역 교통편성표 (Local Traffic Strip)
- **용도**: 공역 내 회전 비행 항공기
- **색상**: 분홍색
- **특징**: 간단한 정보로 표시

### 3.3 비행편성표 베이(Strip Bay) 관리

관제사는 비행편성표를 논리적 그룹으로 정렬하여 관리한다:

```
┌─────────────────────────────────────────────────────────────┐
│  Tower Controller Display - Electronic Flight Strip Bays    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │ PENDING BAY  │  │  DEPARTURE   │  │  APPROACH    │    │
│  │              │  │  SEQUENCE    │  │  SEQUENCE    │    │
│  │ (신규 계획) │  │  (순서 대기) │  │  (진입 대기) │    │
│  │              │  │              │  │              │    │
│  │ • AAL100    │  │ • DLH456     │  │ • BAW777     │    │
│  │ • AFR123    │  │ • UAE002     │  │ • JAL425     │    │
│  │ • SWR450    │  │ • SVA110     │  │              │    │
│  │              │  │              │  │              │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │  ACTIVE ON   │  │  READY TO    │  │   TAXIING    │    │
│  │  RUNWAY      │  │   PUSH       │  │              │    │
│  │              │  │              │  │              │    │
│  │ • DLH456     │  │ • AAL100     │  │ • AFR123     │    │
│  │              │  │ • SWR450     │  │ • SWR450     │    │
│  │              │  │              │  │              │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                             │
│  [추가 베이]                                               │
│  • LOCAL (회전 비행)                                      │
│  • HOLDING (대기 중)                                      │
│  • MAINTENANCE (정비)                                     │
│  • RUNWAYS CLOSED (활주로 폐쇄)                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 3.4 비행편성표 자동 정렬 기준

EFS는 관제 상황에 따라 자동으로 편성표를 정렬해야 한다:

```python
# 출발편성표 정렬 로직 (예시)
def sort_departure_strips(strips):
    """
    출발 편성표를 다음 순서로 정렬:
    1. CTOT(Calculated Take-Off Time) 기준 (A-CDM)
    2. 없으면 EOBT(Estimated Off-Block Time)
    3. 우선순위 항공사 또는 항공기 형식
    """
    sorted_strips = sorted(strips, 
        key=lambda x: (
            x.get('ctot') or x.get('eobt'),
            priority_map.get(x['airline'], 999)
        )
    )
    return sorted_strips

# 도착편성표 정렬 로직 (예시)
def sort_arrival_strips(strips):
    """
    도착 편성표를 다음 순서로 정렬:
    1. ETA(Estimated Time of Arrival)
    2. 우선순위 (응급, VIP 등)
    """
    sorted_strips = sorted(strips, 
        key=lambda x: (
            x['eta'],
            priority_map.get(x['status'], 0)
        )
    )
    return sorted_strips
```

---

## 4. 관제 기능 요구사항

### 4.1 데이터 입력 및 편집 (Data Input & Editing)

관제사는 EFS를 통해 다음 정보를 입력/수정할 수 있어야 한다:

#### 4.1.1 필수 입력 필드

| 필드명 | 설명 | 입력 방식 | 유효성 검증 |
|--------|------|---------|-----------|
| **Heading** | 침로 (000-359°) | 키패드 | 0-359 범위 확인 |
| **Altitude** | 고도 (FL0-FL600) | 키패드 | FL100-FL600 또는 피트 |
| **Speed** | 속도 (Knots) | 키패드 | 100-500 범위 |
| **Route** | 비행경로 | 자동완성 또는 마우스 | 데이터베이스 확인 |
| **Clearance Limit** | 허가 한계 | 키패드 | 공항 코드 확인 |
| **QNH** | 기압고도계 설정 | 키패드 | 950-1050 hPa |
| **ATIS** | ATIS 수신 확인 | 터치/체크박스 | 확인 여부 |
| **Ready to Push** | 출발 준비 완료 | 터치 | 단일 탭 동작 |

#### 4.1.2 입력 인터페이스 유형

- **터치스크린**: 터치 좌표에 따른 직관적 입력
- **키보드/마우스**: 전통적인 키보드 + 마우스 입력
- **펜 입력 (Handwriting Recognition)**: 관제사 수기를 디지털 변환
- **음성 입력**: 추후 고급 기능

### 4.2 충돌 감지 및 경고 (Conflict Detection & Alerting)

#### 4.2.1 충돌 유형 및 감지

**1) 공중 충돌 (Mid-Air Collision)**
- **조건**: 두 항공기 간 거리 < 최소 분리 기준 AND 현재 고도 차이 < 300ft
- **감지 알고리즘**:
  ```
  distance = sqrt((lat1-lat2)² + (lon1-lon2)²)
  altitude_diff = abs(alt1 - alt2)
  
  if distance < MIN_SEPARATION and altitude_diff < 300:
      ALERT = "CONFLICT DETECTED"
  ```

**2) 활주로 침범 (Runway Incursion)**
- **조건**: 항공기/차량이 활주로 보호 영역 진입
- **경고 단계**:
  1. **주의 (Caution)**: 보호 영역 진입 예상
  2. **경고 (Alert)**: 활주로 진입 상태
  3. **비상 (Emergency)**: 활주로 충돌 위험

**3) 분리 위반 (Separation Breach)**
- **수평 분리 (Horizontal Separation)**:
  - IFR-IFR: 1000ft (저고도), 2000ft (고고도)
  - IFR-VFR: 500ft
- **수직 분리 (Vertical Separation)**:
  - 표준: 1000ft (FL290 이하), 2000ft (FL290 이상)

#### 4.2.2 경고 시스템

```
경고 수준별 표시:
┌──────────────────────────────────────────────────┐
│ CRITICAL (치명적)                                 │
│ • 색상: 빨간색 깜박임                             │
│ • 소리: 높은 음의 반복음                          │
│ • 조치: 즉시 대응 필요                            │
│ • 예: 활주로 침범, 공중 충돌 위험                │
├──────────────────────────────────────────────────┤
│ WARNING (경고)                                    │
│ • 색상: 주황색 깜박임                             │
│ • 소리: 중간 음의 경고음                          │
│ • 조치: 주의 깊은 모니터링                        │
│ • 예: 분리 감소, 편성표 지연                     │
├──────────────────────────────────────────────────┤
│ CAUTION (주의)                                    │
│ • 색상: 노란색 고정                               │
│ • 소리: 낮은 음의 신호음 (1회)                   │
│ • 조치: 정보 제공                                 │
│ • 예: 비정상 상태 변화, 이상 징후                │
├──────────────────────────────────────────────────┤
│ INFO (정보)                                       │
│ • 색상: 초록색 또는 파란색                        │
│ • 소리: 선택적 신호음                            │
│ • 조치: 운영 정보 전달                            │
│ • 예: 항공기 위치 업데이트, 시간 초과             │
└──────────────────────────────────────────────────┘
```

### 4.3 자동 시간 기록 (Automatic Time Stamping)

EFS는 다음 이벤트를 자동으로 시간 기록해야 한다:

| 이벤트 | 기록 항목 | 용도 |
|--------|---------|------|
| **첫 연락** | Contact Time | 항공기 진입 확인 |
| **출발 허가** | Clearance Delivery Time | A-CDM 시간선 |
| **Ready to Push** | RTP Time | 출발 준비 상태 |
| **스탠드 이탈** | Actual Off-Block Time (AOBT) | 통계, A-CDM |
| **활주로 대기** | Holding Point Time | 택시 시간 측정 |
| **이륙** | Actual Time of Departure (ATD) | 출발 기록 |
| **착륙** | Actual Time of Arrival (ATA) | 도착 기록 |
| **활주로 이탈** | Runway Exit Time | 활주로 점유 시간 |
| **이양** | Handover Time | 관제 이양 확인 |

### 4.4 조정 및 이양 (Coordination & Handover)

#### 4.4.1 관제 조정 (ATC Coordination)

관제사 간 조정 프로세스:
```
관제 순서: 간선 → 진입(접근) → 타워 → 지상 → 스탠드 제공자

Step 1: 간선 관제사 → 진입 관제사
        편성표 정보 제공, 고도/속도 지시
        
Step 2: 진입 관제사 → 타워 관제사
        착륙 시간 예상, 활주로 할당
        
Step 3: 타워 관제사 → 지상 관제사
        착륙 후 택시 경로 할당
        
Step 4: 지상 관제사 → 스탠드 제공자 (또는 주기장)
        스탠드 할당 완료
        
Step 5: 출발 시 반대 방향
        스탠드 → 지상 → 타워 → 진입 → 간선
```

#### 4.4.2 편성표 이양 (Strip Handover)

```
편성표 이양 프로세스:

1. 준비 단계 (Preparation)
   - 수령 관제사 확인
   - 편성표 정보 검토
   - 이양 위치 확인

2. 이양 단계 (Handover)
   - 송신 관제사: 구두 통보 ("Pass to Ground at...")
   - 수령 관제사: 편성표 수용 ("Received")
   
3. 확인 단계 (Confirmation)
   - 이양 시각 기록
   - 이양 완료 표시
```

---

## 5. 기술 명세 (Technical Specifications)

### 5.1 소프트웨어 요구사항

#### 5.1.1 개발 환경

| 항목 | 요구사항 |
|------|---------|
| **언어** | JavaScript/TypeScript (웹 기반), Java (백엔드), Python (분석) |
| **프레임워크** | React.js (프론트엔드), Node.js/Express (백엔드) |
| **데이터베이스** | PostgreSQL (관계형), Redis (캐시) |
| **통신 프로토콜** | TCP/IP, WebSocket (실시간 동기화) |
| **메시지 형식** | JSON, ASTERIX (표준 ATM 형식) |
| **운영체제** | Windows, Linux, macOS (크로스 플랫폼) |
| **웹 브라우저** | Chrome, Firefox, Safari (최신 버전) |

#### 5.1.2 성능 요구사항

| 지표 | 요구사항 | 설명 |
|------|---------|------|
| **Display Latency** | < 500ms | 데이터 수신 → 화면 표시 최대 시간 |
| **Update Rate** | 1초 | 위치 정보 갱신 주기 |
| **Response Time** | < 250ms | 사용자 입력 → 시스템 반응 |
| **Position Accuracy** | 1 pixel (최소 1m) | 화면 상 위치 정확도 |
| **Map Accuracy** | ± 1m | 항공편 위치 정확도 |
| **Resolution** | 1024 x 1280 pixels | 최소 화면 해상도 |
| **Availability** | 99.9% | 시스템 가용성 |
| **Max Users** | 50+ 동시 사용자 | 한 공항 내 관제사 수 |
| **Data Storage** | 30일 로그 | 감사 추적(Audit Trail) |

### 5.2 하드웨어 요구사항

#### 5.2.1 Controller Position 하드웨어

```
┌─────────────────────────────────────┐
│     Controller Working Position     │
├─────────────────────────────────────┤
│                                     │
│  ┌──────────────────────────────┐  │
│  │   Dual Monitor Setup         │  │
│  │  • Primary: 24" FHD 1920x1080│  │
│  │  • Secondary: 24" FHD        │  │
│  │  • Color Calibrated          │  │
│  │  • Anti-glare & adjustable   │  │
│  └──────────────────────────────┘  │
│           │          │              │
│    ┌──────┘          └──────┐      │
│    │                        │      │
│    ▼                        ▼      │
│  ┌──────────────┐  ┌──────────────┐
│  │  Main EFS    │  │  Radar/Map   │
│  │   Display    │  │   Display    │
│  └──────────────┘  └──────────────┘
│                                     │
│  ┌─────────────────────────────┐   │
│  │   Keyboard & Mouse Setup    │   │
│  │ • Mechanical Keyboard       │   │
│  │ • Precision Mouse           │   │
│  │ • Ergonomic layout          │   │
│  │ • Custom shortcuts          │   │
│  └─────────────────────────────┘   │
│                                     │
│  ┌─────────────────────────────┐   │
│  │   Audio System              │   │
│  │ • Speaker (Alert)           │   │
│  │ • Headset (Comms)           │   │
│  │ • Volume Control            │   │
│  └─────────────────────────────┘   │
│                                     │
│  ┌─────────────────────────────┐   │
│  │   Server/Computer           │   │
│  │ • CPU: Intel i7/i9          │   │
│  │ • RAM: 16-32GB              │   │
│  │ • Storage: 1TB SSD          │   │
│  │ • Network: Gigabit Ethernet │   │
│  └─────────────────────────────┘   │
│                                     │
└─────────────────────────────────────┘
```

#### 5.2.2 최소 시스템 사양

| 구성요소 | 권장 사양 |
|---------|---------|
| **CPU** | Intel Core i7/i9 또는 AMD Ryzen 7+ |
| **RAM** | 16GB 이상 |
| **SSD** | 1TB 이상 (7200RPM 이상) |
| **모니터** | 24" FHD (1920x1080) x 2 권장 |
| **네트워크** | Gigabit Ethernet (최소 100Mbps) |
| **UPS** | 무정전 전원 공급 (30분 이상) |
| **해상도** | 최소 1024x1280 |

### 5.3 데이터 통신 인터페이스

#### 5.3.1 외부 시스템 연동

```
┌──────────────────────────────────────────────────────┐
│              EFS System Integration                  │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌─────────────────────────────────────────────┐   │
│  │   EFS Core System                           │   │
│  │  • Flight Data Management                   │   │
│  │  • Controller HMI                           │   │
│  │  • Conflict Detection                       │   │
│  └─────────────────────────────────────────────┘   │
│              │         │         │         │        │
│     ┌────────┘  ┌──────┘  ┌──────┘  ┌─────┘       │
│     │           │         │          │             │
│     ▼           ▼         ▼          ▼             │
│  ┌──────┐  ┌──────┐  ┌──────┐  ┌─────────┐      │
│  │ FDIO │  │STARS │  │ADS-B │  │ DMAN    │      │
│  │ (기 │  │(터   │  │(자동 │  │(출발   │      │
│  │ 존) │  │미널  │  │의존 │  │관리)   │      │
│  │ 데이│  │자동  │  │감시 │  │         │      │
│  │터   │  │화)   │  │)     │  │         │      │
│  └──────┘  └──────┘  └──────┘  └─────────┘      │
│                                                      │
│  통신 프로토콜:                                    │
│  • TCP/IP (기본)                                   │
│  • WebSocket (실시간)                             │
│  • ASTERIX CAT011 (감시)                          │
│  • ASTERIX CAT062 (추적)                          │
│                                                      │
└──────────────────────────────────────────────────────┘
```

#### 5.3.2 데이터 교환 메시지 형식 (JSON 예시)

```json
{
  "strip_id": "AAL100-20251231-0845",
  "aircraft": {
    "call_sign": "AAL100",
    "icao_code": "AAL",
    "aircraft_type": "B738",
    "equipment_suffix": "/L",
    "transponder_code": "2543"
  },
  "flight_plan": {
    "departure": {
      "airport_code": "ICAO:KJFK",
      "scheduled_time": "2025-12-31T08:45:00Z"
    },
    "destination": {
      "airport_code": "ICAO:KDFW",
      "scheduled_time": "2025-12-31T11:30:00Z"
    },
    "cruise_level": "FL350",
    "route": "MARCH1 DCT ORC DCT BZZ"
  },
  "current_status": {
    "position": {
      "latitude": 40.6413,
      "longitude": -73.7781,
      "altitude_ft": 1500,
      "timestamp": "2025-12-31T08:52:30Z"
    },
    "movement": {
      "heading": 270,
      "speed_knots": 120,
      "vertical_speed_fpm": 500
    }
  },
  "clearance": {
    "assigned_heading": 265,
    "assigned_altitude": "FL350",
    "assigned_speed": "250",
    "runway": "04L",
    "clearance_time": "2025-12-31T08:50:00Z"
  },
  "status": {
    "strip_type": "DEPARTURE",
    "current_bay": "TAXIING",
    "priority": "NORMAL",
    "last_update": "2025-12-31T08:52:30Z"
  }
}
```

---

## 6. 규정 준수 (Regulatory Compliance)

### 6.1 국제 표준 및 규정

#### 6.1.1 ICAO 문서

| 문서 | 제목 | 관련 내용 |
|------|------|---------|
| **ICAO Annex 11** | Air Traffic Services | ATS 서비스 표준 |
| **ICAO Doc 4444** | PANS-ATM | 항공 교통 관리 절차 |
| **ICAO Annex 14** | Aerodrome Design and Operations | 공항 설계 및 운영 |
| **ICAO Doc 9830** | Advanced Surface Movement Guidance & Control Systems | A-SMGCS 가이드 |

#### 6.1.2 EUROCAE 표준

| 표준 | 제목 | 관련 내용 |
|------|------|---------|
| **ED-87B** | Minimum Aviation System Performance Specification for Advanced Surface Movement Guidance and Control System | A-SMGCS 최소 성능 사양 |
| **ED-102** | Guidance material on the implementation of the remote tower systems | 원격 관제탑 구현 지침 |

#### 6.1.3 EUROCONTROL 명세

| 명세 | 제목 | 관련 내용 |
|------|------|---------|
| **SPEC-154** | Specification for Aeronautical Data Quality | 항공 데이터 품질 |
| **SPEC-165** | Airport Collaborative Decision Making | A-CDM 사양 |

### 6.2 한국 관제 규정

#### 6.2.1 항공규칙 (기준)

- **항공사업법**: 항공사업자의 안전 의무
- **항공안전법**: 항공안전 기준
- **공역 관리 규정**: 한국 공역 운영 절차
- **관제사 자격 기준**: 비행편성표 사용 규정

#### 6.2.2 공항별 운영 절차

- **인천국제공항** (ICAO RKSI)
- **김포국제공항** (ICAO RKSS)
- **부산신공항** (ICAO RKPK)

각 공항의 Station Standing Instruction (SSI)에 따른 색상, 약자, 절차 준수

---

## 7. EFS 개발 로드맵

### 7.1 Phase 1: 기초 플랫폼 구축 (3-4개월)

**목표**: 기본 EFS 화면 및 데이터 관리 기능 구현

```
Week 1-4: 요구사항 분석 및 설계
├── 상세 요구사항 정의
├── UI/UX 디자인
├── 데이터베이스 스키마 설계
└── API 명세 정의

Week 5-8: 핵심 기능 개발
├── 비행편성표 데이터 모델 구현
├── EFS 화면 UI 개발
├── 기본 CRUD 기능 (Create, Read, Update, Delete)
└── 데이터베이스 구축

Week 9-12: 통합 및 테스트
├── 컴포넌트 통합
├── 단위 테스트 (Unit Test)
├── 통합 테스트 (Integration Test)
└── 버그 수정 및 성능 최적화

산출물:
• EFS v0.1 (알파 버전)
• API 문서
• 테스트 리포트
```

### 7.2 Phase 2: 실시간 통신 및 감시 통합 (3-4개월)

**목표**: 감시 시스템(레이더, ADS-B) 연동 및 실시간 항공기 추적

```
Week 1-4: 외부 시스템 통신
├── FDIO 연동 (기존 데이터)
├── STARS 연동 (터미널 자동화)
├── ADS-B 수신 및 파싱
└── WebSocket 기반 실시간 동기화

Week 5-8: 데이터 융합 (Data Fusion)
├── 다중 소스 데이터 병합
├── 중복 제거 및 검증
├── 위치 예측 및 트래킹
└── 레이더 데이터 시각화

Week 9-12: 통합 및 성능 개선
├── 대량 데이터 처리 테스트
├── 지연 시간(Latency) 최소화
├── 네트워크 안정성 개선
└── 사용자 피드백 반영

산출물:
• EFS v0.2 (베타 버전)
• 감시 시스템 통합 가이드
• 성능 테스트 결과
```

### 7.3 Phase 3: 충돌 감지 및 경고 시스템 (2-3개월)

**목표**: 자동 충돌 감지 및 관제사 경고

```
Week 1-4: 충돌 감지 알고리즘 개발
├── 분리 기준 정의
├── 충돌 탐지 알고리즘 구현
├── 활주로 침범 감지
└── 시뮬레이션 및 검증

Week 5-8: 경고 시스템 구현
├── 경고 수준별 표시 (Critical, Warning, Caution)
├── 음향 경고 (Aural Alert)
├── 시각적 경고 (Visual Alert)
└── 경고 로직 테스트

Week 9-10: 통합 및 최적화
├── 오경고(False Alert) 최소화
├── 응답 시간 최적화
├── 사용성 개선
└── 최종 테스트

산출물:
• EFS v0.3 (기능 완성 버전)
• 충돌 감지 명세서
• 경고 시스템 테스트 결과
```

### 7.4 Phase 4: 고급 기능 및 최적화 (2-3개월)

**목표**: 출발 관리, 택시 계획, 이양 기능

```
Week 1-3: 출발 관리 (DMAN)
├── 자동 비행편성표 정렬
├── CTOT 계산 및 적용
├── 출발 순서 시각화
└── 관제사 수동 조정 기능

Week 4-6: 택시 경로 계획
├── 자동 택시 경로 계산
├── 경로 시각화
├── 관제사 수동 경로 지정
└── 경로 충돌 감지

Week 7-10: 편성표 이양 및 조정
├── 관제 포지션 간 편성표 이양
├── 음성/문자 조정 메시지
├── 조정 상태 시각화
└── 이양 확인 및 기록

산출물:
• EFS v1.0 (정식 버전)
• 운영 매뉴얼
• 유저 트레이닝 자료
```

### 7.5 Phase 5: 배포 및 운영 (지속)

**목표**: 실제 공항 도입 및 지속적 개선

```
Pre-Deployment:
├── 감항성 인증(Airworthiness Certification)
├── 보안 감사 (Security Audit)
├── 성능 검증 (Validation)
└── 운영자 교육 (Operator Training)

Deployment:
├── 파일럿 공항 도입 (1-2개 공항)
├── 실제 운영 데이터 수집
├── 문제 해결 (Troubleshooting)
└── 정식 배포

Post-Deployment:
├── 지속적 모니터링
├── 버그 수정 및 패치
├── 사용자 피드백 수집
└── 기능 개선 (v1.1, v1.2, ...)

산출물:
• 정식 배포 버전
• 운영 가이드
• 지속적 개선 로드맵
```

---

## 8. 프로젝트 구조 (Project Structure)

```
electric-flight-strip-system/
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── docs/
│   ├── EFS_COMPREHENSIVE_TECHNICAL_SPECIFICATION.md (본 문서)
│   ├── ARCHITECTURE.md
│   ├── API_SPECIFICATION.md
│   ├── DATABASE_SCHEMA.md
│   └── DEPLOYMENT_GUIDE.md
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── FlightStripDisplay.tsx
│   │   │   ├── StripBay.tsx
│   │   │   ├── AlertPanel.tsx
│   │   │   └── ...
│   │   ├── pages/
│   │   │   ├── ControllerWorkstation.tsx
│   │   │   └── AdministrationPanel.tsx
│   │   ├── services/
│   │   │   ├── flightDataService.ts
│   │   │   ├── surveillanceService.ts
│   │   │   └── conflictDetectionService.ts
│   │   ├── utils/
│   │   ├── styles/
│   │   └── App.tsx
│   ├── public/
│   ├── package.json
│   └── tsconfig.json
├── backend/
│   ├── src/
│   │   ├── controllers/
│   │   │   ├── flightStripController.ts
│   │   │   ├── surveillanceController.ts
│   │   │   └── alertController.ts
│   │   ├── services/
│   │   │   ├── flightDataService.ts
│   │   │   ├── conflictDetectionService.ts
│   │   │   ├── routingService.ts
│   │   │   └── departureManagementService.ts
│   │   ├── models/
│   │   │   ├── FlightStrip.ts
│   │   │   ├── Aircraft.ts
│   │   │   ├── Alert.ts
│   │   │   └── ...
│   │   ├── middleware/
│   │   ├── routes/
│   │   ├── config/
│   │   ├── database/
│   │   │   ├── migrations/
│   │   │   └── schema.sql
│   │   └── index.ts
│   ├── tests/
│   │   ├── unit/
│   │   ├── integration/
│   │   └── e2e/
│   ├── package.json
│   └── .env.example
├── simulation/
│   ├── src/
│   │   ├── simulator.ts
│   │   ├── trafficGenerator.ts
│   │   ├── conflictScenarios.ts
│   │   └── ...
│   └── test-cases/
├── infrastructure/
│   ├── docker-compose.yml
│   ├── kubernetes/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── nginx/
│       └── nginx.conf
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── test.yml
│   │   └── deploy.yml
│   └── ISSUE_TEMPLATE/
├── .gitignore
└── package.json
```

---

## 9. 운영 및 유지보수

### 9.1 시스템 모니터링

```
실시간 모니터링 항목:
├── 성능 지표 (Performance Metrics)
│   ├── CPU 사용률
│   ├── 메모리 사용률
│   ├── 네트워크 대역폭
│   └── 디스크 I/O
├── 가용성 (Availability)
│   ├── 서버 가동 시간
│   ├── 데이터베이스 응답 시간
│   └── API 응답 시간
├── 에러 추적 (Error Tracking)
│   ├── 애플리케이션 에러
│   ├── 시스템 에러
│   └── 네트워크 에러
└── 사용자 활동 (User Activity)
    ├── 동시 접속자 수
    ├── 트랜잭션 수
    └── 경고 생성 수
```

### 9.2 데이터 백업 및 복구

```
백업 정책:
├── 실시간 백업
│   ├── 주기: 1시간
│   ├── 보관: 최근 72시간
│   └── 위치: 로컬 + 클라우드 (이중화)
├── 일일 백업
│   ├── 주기: 매일 02:00 (UTC+9)
│   ├── 보관: 30일
│   └── 위치: 외부 스토리지
└── 월별 백업
    ├── 주기: 매달 1일
    ├── 보관: 12개월
    └── 위치: 오프사이트 아카이브
```

### 9.3 로그 관리

```
로깅 레벨:
├── CRITICAL: 시스템 장애, 안전 위험
├── ERROR: 기능 오류, 예외 상황
├── WARNING: 비정상 상태, 주의 필요
├── INFO: 주요 이벤트, 운영 정보
└── DEBUG: 개발 디버깅 정보

로그 저장소:
├── 로컬 파일: 실시간 로그 (1주일)
├── 로그 서버: 중앙 집중식 로깅 (1개월)
└── 아카이브: 장기 보관 (12개월)
```

---

## 10. 보안 요구사항

### 10.1 접근 제어 (Access Control)

```
사용자 역할 기반 접근 제어 (Role-Based Access Control, RBAC):

1. 시스템 관리자 (System Administrator)
   ├── 권한: 모든 시스템 기능 접근
   ├── 사용자 관리, 권한 설정
   └── 시스템 설정 및 구성

2. 관제사 (Air Traffic Controller)
   ├── 권한: EFS 조작, 데이터 입력
   ├── 편성표 조회, 편집, 이양
   └── 경고 확인 및 조치

3. 감시자 (Supervisor)
   ├── 권한: 모니터링 및 감독
   ├── 관제사 활동 조회
   └── 리포트 생성

4. 정비사 (Maintenance)
   ├── 권한: 시스템 로그 조회
   ├── 성능 데이터 분석
   └── 버그 리포팅

5. 게스트 (Guest)
   ├── 권한: 제한된 조회만 가능
   └── 데이터 편집 불가
```

### 10.2 데이터 보안

- **암호화**: 모든 네트워크 통신은 TLS 1.3 이상
- **데이터베이스 암호화**: AES-256 암호화
- **비밀번호 정책**: 최소 12자, 대소문자/숫자/특수문자 혼합
- **감사 추적**: 모든 사용자 활동 기록

### 10.3 시스템 보안

- **방화벽**: 필수 포트만 개방
- **IDS/IPS**: 침입 탐지 및 방지 시스템 배치
- **정기 보안 감사**: 분기별 보안 검사
- **취약점 관리**: 즉시 패치 적용

---

## 11. 참고 문서 및 자료

### 11.1 국제 규정 및 표준

1. **ICAO 문서**
   - [ICAO Annex 11](https://www.icao.int) - Air Traffic Services
   - [ICAO Doc 4444](https://www.icao.int) - Procedures for Air Navigation Services - Air Traffic Management
   - [ICAO Doc 9830](https://www.icao.int) - Advanced Surface Movement Guidance and Control Systems (A-SMGCS)

2. **EUROCAE 표준**
   - [ED-87B](https://www.eurocae.net) - Minimum Aviation System Performance Specification for A-SMGCS
   - [ED-102](https://www.eurocae.net) - Guidance Material on Remote Tower Systems

3. **EUROCONTROL 명세**
   - [SPEC-154](https://www.eurocontrol.int) - Aeronautical Data Quality Specification
   - [SPEC-165](https://www.eurocontrol.int) - Airport Collaborative Decision Making (A-CDM)

### 11.2 한국 관제 규정

1. **국토교통부 항공정책과**
   - 항공사업법 시행규칙
   - 항공안전법

2. **한국공항공사 & 인천국제공항공사**
   - Station Standing Instruction (SSI)
   - 관제 운영 절차서

3. **공항별 운영 가이드**
   - [인천국제공항](https://www.airport.kr)
   - [김포국제공항](https://www.gimpo.go.kr)

### 11.3 기술 참고 자료

1. **웹 기술**
   - [React Documentation](https://react.dev)
   - [TypeScript Handbook](https://www.typescriptlang.org/docs)
   - [Node.js Documentation](https://nodejs.org/docs)

2. **데이터 형식**
   - [ASTERIX 사양](https://www.eurocontrol.int/articles/asterix) - ATM 표준 메시지 형식
   - [JSON Schema](https://json-schema.org)

3. **보안**
   - [OWASP Top 10](https://owasp.org/www-project-top-ten/)
   - [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)

---

## 12. FAQ 및 문제 해결

### 12.1 자주 묻는 질문 (FAQ)

**Q1: EFS와 종이 비행편성표의 주요 차이점은?**
- A: EFS는 실시간 데이터 동기화, 자동 경고, 충돌 감지 기능을 제공하여 관제사의 워크로드를 크게 감소시킵니다.

**Q2: 시스템 장애 시 대응 방안은?**
- A: EFS는 페일세이프(Fail-Safe) 설계로 하나의 컴포넌트 장애가 전체 시스템에 미치지 않도록 설계되었습니다.

**Q3: 다른 공항과의 데이터 호환성은?**
- A: ICAO 표준을 준수하여 국제적 호환성을 보장합니다.

### 12.2 일반적인 문제 해결

| 문제 | 원인 | 해결 방법 |
|------|------|---------|
| 비행편성표가 안 보임 | 네트워크 연결 끊김 | 네트워크 연결 확인, 서버 상태 점검 |
| 실시간 업데이트 지연 | 서버 과부하 | 시스템 재시작, 데이터베이스 최적화 |
| 경고음이 나오지 않음 | 오디오 설정 | 음량 설정 확인, 오디오 드라이버 업데이트 |
| 데이터 손실 | 백업 실패 | 백업 로그 확인, 복구 절차 실행 |

---

## 13. 결론 및 다음 단계

### 13.1 주요 성과 기대효과

EFS 시스템의 도입으로 다음을 기대할 수 있습니다:

1. **안전성 향상**
   - 자동 충돌 감지로 인한 사고 예방
   - 활주로 침범 경보로 인한 안전 강화

2. **운영 효율성 개선**
   - 관제사 워크로드 50% 이상 감소
   - 항공기 처리량 20-30% 증가
   - 지연 시간 감소

3. **비용 절감**
   - 종이, 잉크 등 소비재 제거
   - 직원 교육 시간 감소
   - 시스템 유지보수 비용 절감

4. **환경 친화성**
   - 종이 사용 제거
   - 에너지 효율적 운영

### 13.2 향후 개선 방향

- **AI/ML 적용**: 패턴 인식 기반 최적 경로 추천
- **음성 인식**: 관제 통신 자동 기록
- **증강현실 (AR)**: 3D 비행편성표 시각화
- **모바일 플랫폼**: 스마트폰/태블릿 지원

---

## Appendix A: 용어 사전

| 용어 | 약자 | 정의 |
|------|------|------|
| Advanced Surface Movement Guidance and Control System | A-SMGCS | 항공기 지상 이동을 제어하는 고급 시스템 |
| Air Traffic Control | ATC | 항공 교통 관제 |
| Air Traffic Management | ATM | 항공 교통 관리 |
| Actual Time of Arrival | ATA | 실제 도착 시각 |
| Actual Time of Departure | ATD | 실제 출발 시각 |
| Automatic Dependent Surveillance – Broadcast | ADS-B | 자동 의존 감시 방송 |
| Calculated Time Over Threshold | CTOT | 계산된 임계값 통과 시각 |
| Controller Pilot Data Link Communications | CPDLC | 관제사-조종사 데이터링크 |
| Departure Management | DMAN | 출발 관리 시스템 |
| Electronic Flight Strip | EFS | 전자 비행편성표 |
| Estimated Off-Block Time | EOBT | 예상 이륙 시각 |
| Estimated Time of Arrival | ETA | 예상 도착 시각 |
| Estimated Time of Departure | ETD | 예상 출발 시각 |
| Flight Data Input/Output | FDIO | 비행 데이터 입출력 |
| Flight Plan | FPL | 비행 계획 |
| Heading | HDG | 침로 (도 단위) |
| Instrument Flight Rules | IFR | 계기 비행 규칙 |
| Minimum Vectoring Altitude | MVA | 최소 벡터링 고도 |
| Multi-Lateration | MLAT | 다중 측위 |
| Predicted Wind | PRD-WIND | 예측 바람 |
| Primary Surveillance Radar | PSR | 1차 감시 레이더 |
| Runway Incursion | RI | 활주로 침범 |
| Runway Visual Range | RVR | 활주로 시정 |
| Secondary Surveillance Radar | SSR | 2차 감시 레이더 |
| Standard Terminal Automation Replacement System | STARS | 표준 터미널 자동화 시스템 |
| Surface Movement Radar | SMR | 지상 이동 레이더 |
| Target Stand Arrival Time | TSAT | 목표 스탠드 도착 시각 |
| Target Take-Off Time | TTOT | 목표 이륙 시각 |
| Visual Flight Rules | VFR | 시정 비행 규칙 |
| Wake Turbulence Category | WTC | 후류 난기류 범주 |

---

**문서 버전 이력**

| 버전 | 작성일 | 변경사항 |
|------|--------|---------|
| 1.0 | 2025-12-31 | 초안 작성 |

---

**승인자**

| 역할 | 이름 | 서명 | 날짜 |
|------|------|------|------|
| 프로젝트 리더 | - | - | - |
| 기술 리뷰어 | - | - | - |
| 관제 전문가 | - | - | - |

