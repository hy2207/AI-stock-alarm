# SRS v0.2 → v0.3 (이 후 1.0으로 저장) 변경사항 검토 보고

아래는 **SRS v0.2 대비 SRS v0.3 변경사항 검토 결과**이다.  
검토 관점은 다음 3가지이다.

1. 기술 스택의 명확성
2. MVP 목표의 조정 내용
3. 기타 차이점

---

## 1) 기술 스택의 명확성

| 항목 | SRS v0.2 | SRS v0.3 | 검토 의견 |
|---|---|---|---|
| 시장/뉴스 데이터 소스 | `Market Data Provider`, `News Data Provider` 등 추상적 표현 사용 | `Yahoo Finance API` 또는 `Finnhub`로 구체화 | 실제 연동 대상을 명시하여 구현 혼선을 줄였음 |
| 푸시 알림 스택 | `Push Notification Provider`, Web Push 직접 구현 전제 | `OneSignal` 명시 | 브라우저 푸시 직접 구현 복잡도를 줄이고 관리형 서비스 활용으로 전환 |
| 분석/이벤트 수집 | `/api/events` + Prisma `EventLog` 직접 적재 | `posthog-js` + `PostHog` 직접 적재 | 이벤트 파이프라인이 더 단순하고 명확해짐 |
| KPI 대시보드 방식 | 내부 DB 조회 + Cron 집계 | `PostHog` 자체 대시보드 활용 | 별도 KPI 배치/집계 개발 필요성이 줄어듦 |
| LLM 출력 처리 방식 | `generateObject()` 중심, 스트리밍은 선택적 | `streamObject()` 사용 강제 | Vercel Hobby timeout 대응을 더 구체적으로 명시 |
| 외부 시스템 정의 수준 | 일부 외부 시스템은 일반화된 계층 수준 | SaaS/API 이름까지 구체화 | Cursor 등 AI 코딩 환경에서 프롬프트 해상도가 높아짐 |
| 데이터 저장 책임 범위 | 비즈니스 데이터 + 운영 로그를 Prisma DB가 모두 담당 | 핵심 비즈니스 데이터만 Prisma DB 담당 | DB 책임 범위가 더 명확해짐 |
| 프론트엔드 연동 라이브러리 | Next.js 중심 | Next.js + OneSignal + PostHog | 구현 시 필요한 실제 스택이 더 분명해짐 |

---

## 2) MVP 목표의 조정 내용

| 항목 | SRS v0.2의 방향 | SRS v0.3의 방향 | 검토 의견 |
|---|---|---|---|
| MVP 구현 철학 | 기능 구현 범위가 비교적 넓고 내부 개발 비중이 큼 | 1인 MVP / 바이브코딩 성공률 최적화 중심 | 구현 완성도보다 실현 가능성과 속도를 우선하는 방향으로 조정됨 |
| 이벤트 수집 목표 | 이벤트 저장 API와 적재 구조까지 직접 구축 | 이벤트 수집은 하되 저장/분석은 PostHog에 위임 | MVP에서 불필요한 백엔드 공수를 줄임 |
| KPI 기능 목표 | KPI 집계 로직과 대시보드까지 내부 구현 | KPI는 PostHog 대시보드로 대체 | 내부 배치 개발을 제거하여 범위를 축소함 |
| 푸시 기능 목표 | Cron + DB + Web Push 직접 발송 | OneSignal API 호출 기반 발송 | 사용자 가치 유지, 구현 복잡도 감소 |
| DB 역할 | 운영 로그까지 포함한 광범위한 저장소 | 핵심 비즈니스 상태 저장소 | MVP용 데이터베이스 역할이 더 선명해짐 |
| 운영 목표 | 내부 모니터링/집계 요소 포함 | SaaS 대시보드 활용으로 운영 단순화 | 검증 속도에 더 적합한 구조로 조정됨 |
| 실패 대응 목표 | 실패 시 내부 로그 적재 중심 | No Call + SaaS 기반 관측 가능성 확보 | 실패 기록은 유지하되 구현 복잡도는 줄임 |
| 범위 축소 방식 | 사용자 기능 외 운영 기능도 일부 직접 구현 | 사용자 핵심 기능 유지, 운영 구현 범위는 축소 | 제품 가치는 유지하면서 MVP 범위를 다이어트한 형태 |

---

## 3) 기타 차이점

| 항목 | SRS v0.2 | SRS v0.3 | 검토 의견 |
|---|---|---|---|
| In-Scope 표현 | 행동 이벤트 추적, KPI 데이터 파이프라인 구현 포함 | PostHog 수집 및 대시보드 활용 중심으로 조정 | 범위 설명이 구현 친화적으로 재정리됨 |
| Definitions/용어집 | PostHog, OneSignal, Yahoo Finance, Finnhub 관련 정의 없음 | 관련 용어 추가 | 문서 자체만 읽어도 기술 맥락 이해가 쉬워짐 |
| References 구성 | KPI Dashboard Spec 등 내부 집계 문서 전제 | 개정 레시피 및 SaaS 활용 구조 중심으로 재정렬 | 참고 문서 체계가 v0.3 방향에 더 잘 맞음 |
| Stakeholder 관점 | `Data Engineer` 중심 해석 가능 | `Analytics Owner` 또는 운영 관점 비중 증가 | 직접 집계 개발보다 분석 운영 중심으로 이동 |
| REQ-FUNC-050 | DB 조회 후 Web Push 직접 발송 | OneSignal REST API 호출 | 구현 방식이 명확히 전환된 핵심 차이 |
| REQ-FUNC-060 | `/api/events` 통해 Prisma `EventLog` 적재 | PostHog 직접 전송 | API/DB 직접 적재 구조 제거 |
| REQ-FUNC-061 | DB 조회 + Cron 기반 KPI 집계 | PostHog 대시보드로 대체 | 사실상 기능 삭제 또는 외부 위임 |
| REQ-NF-070 | timeout 고려 설계 수준 | `streamObject()` 사용 강제 | 비기능 요구사항이 더 구체적 제약으로 강화됨 |
| 데이터 모델 | `NotificationLog`, `EventLog` 존재 | 두 모델 삭제 | DB 경량화와 SaaS 위임 방향이 일관되게 반영됨 |
| 탈퇴 처리 범위 | watchlist, riskProfile, eventLog 삭제/익명화 | PostHog / OneSignal까지 고려 필요 | 외부 SaaS 사용에 따른 개인정보 처리 범위가 확장됨 |
| 시퀀스 다이어그램 | Event API, NotificationLog 중심 흐름 | PostHog, OneSignal 중심 흐름 | 문장뿐 아니라 시스템 흐름도 함께 변경됨 |
| 운영 모니터링 소스 | Vercel Analytics + 내부/커스텀 대시보드 | PostHog Dashboard + OneSignal Dashboard | 운영 관측 체계가 더 단순해지고 명확해짐 |

---

## 종합 의견

| 항목 | 평가 |
|---|---|
| 핵심 변화 성격 | 제품 요구사항 자체의 대전환이라기보다, **MVP 실현 방식의 현실화**에 가까움 |
| 가장 큰 개선점 | 추상적 외부 시스템 정의를 실제 SaaS/API 이름으로 구체화한 점 |
| 가장 큰 범위 조정 | 이벤트 수집, KPI 집계, 푸시 발송 로그 저장을 내부 구현에서 외부 SaaS 위임으로 전환한 점 |
| 기대 효과 | 구현 속도 향상, AI 코딩 혼선 감소, DB 경량화, 운영 복잡도 감소 |
| 주의할 점 | SaaS 의존성이 커지므로 개인정보 처리, 외부 서비스 장애 영향, 벤더 종속성 검토 필요 |

---

## 한 줄 결론

**SRS v0.3는 사용자에게 보이는 핵심 기능은 거의 유지하면서, 내부 구현 책임을 직접 개발에서 SaaS 활용으로 재배치한 개정판이다.**  
즉, 본질은 기능 확대가 아니라 **기술 명세의 구체화와 MVP 범위의 현실화**이다.