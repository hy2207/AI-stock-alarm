# SRS v0.3 개정 계획서 (바이브코딩 및 시스템 효율성 최적화)

**문서 ID:** PLAN-SRS-003  
**작성일:** 2026-04-16  
**대상 문서:** SRS v0.2 → v0.3  
**개정 목표:** AI 에이전트(Cursor 등)를 활용한 1인 MVP 개발(바이브코딩)의 속도와 성공률을 극대화하기 위해, 자체 구현(오버엔지니어링) 요소를 제거하고 구체적인 SaaS/API 스택을 명시함.

---

## 1. 주요 개정 방향

1.  **자체 구현 최소화 (SaaS 적극 도입):** AI가 구현 시 헤매기 쉬운 복잡한 백엔드 로직(이벤트 DB 적재, 브라우저 푸시 직접 구현)을 검증된 3rd-party 솔루션으로 대체.
2.  **모호성 제거 (API 명시):** 추상적으로 정의된 외부 데이터 소스를 구체적인 서비스명으로 특정하여 프롬프트 작성 시 혼선을 방지.
3.  **런타임 제약 대응:** Vercel Hobby 플랜의 타임아웃(10초) 한계를 인정하고, 이를 우회하거나 대응하는 아키텍처를 명시.

---

## 2. 세부 개정 대상 및 내용 (Action Items)

### 2.1. 인프라 및 아키텍처 (Section 3 & 4.2)
| 대상 항목 | 기존 내용 (v0.2) | 변경 내용 (v0.3) | 개정 사유 |
| :--- | :--- | :--- | :--- |
| **3.1 External Systems** | Market/News Data Provider | **Yahoo Finance API (또는 Finnhub 무료 티어)** 로 구체화 | LLM 프롬프트에 넣을 데이터 JSON 스키마를 특정하기 위함 |
| **3.1 External Systems** | Push Notification Provider | **OneSignal** 로 구체화 | 자체 Service Worker 구현의 난이도 및 AI 할루시네이션 회피 |
| **3.1 External Systems** | Analytics Provider (신설) | (없음) | **PostHog** 신규 추가 (이벤트 수집 및 KPI 대시보드 대체용) |
| **REQ-NF-070** (타임아웃) | 10초 이내 처리 설계 | Vercel AI SDK **`streamObject` 사용 강제** 명시 | LLM 호출 지연 시 504 에러 방지 및 사용자 UX 개선 |

### 2.2. 기능 요구사항 (Section 4.1)
| 대상 REQ ID | 기존 내용 (v0.2) | 변경 내용 (v0.3) | 개정 사유 |
| :--- | :--- | :--- | :--- |
| **REQ-FUNC-050** (푸시 발송) | Vercel Cron + DB 조회 후 Web Push 직접 발송 | **OneSignal REST API** 호출로 푸시 발송 | 백엔드 복잡도 및 브라우저 호환성 이슈 제거 |
| **REQ-FUNC-060** (이벤트 수집) | `/api/events` 통해 Prisma DB에 직접 `EventLog` 적재 | 클라이언트에서 **posthog-js**를 통해 이벤트를 PostHog로 직접 전송 | 무거운 이벤트 적재 API 개발 및 DB 용량 관리 공수 제거 |
| **REQ-FUNC-061** (KPI 대시보드) | DB 조회하여 Vercel Cron으로 집계 | **PostHog 자체 대시보드** 기능으로 완벽히 대체 (기능 삭제) | 데이터 엔지니어링 리소스(배치 로직 구현) 완전 제거 |

### 2.3. 데이터 모델 (Section 6.2)
이벤트 및 알림 로그를 외부 SaaS로 위임함에 따라, Prisma 스키마를 대폭 경량화합니다.

* **6.2.7 NotificationLog (알림 로그):** 모델 **삭제**. (발송 성공 여부는 OneSignal 대시보드에서 확인)
* **6.2.8 EventLog (행동 로그):** 모델 **삭제**. (모든 행동 데이터는 PostHog에 적재)
* *효과:* DB는 오직 User, Watchlist, RiskProfile, RecommendationCard(핵심 비즈니스 로직)만 관리하게 되어 성능과 유지보수성이 극대화됩니다.

