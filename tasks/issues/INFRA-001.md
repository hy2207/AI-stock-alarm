---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] INFRA-001: Next.js App Router 프로젝트 초기 생성 및 UI 프레임워크 설정"
labels: 'infra, scaffolding, priority:critical'
assignees: ''
---

## :dart: Summary
- 기능명: [INFRA-001] Next.js App Router 프로젝트 초기 생성 — Tailwind CSS + shadcn/ui 설정, 디렉토리 구조 수립
- 목적: 전체 시스템의 기반이 되는 Next.js App Router 프로젝트를 생성하고, UI 프레임워크(Tailwind CSS + shadcn/ui)를 설정하여 모든 후속 개발 태스크가 즉시 착수 가능한 프로젝트 스캐폴딩을 완성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/tasks/SRS-v1.md#1.5.2`](/tasks/SRS-v1.md) — C-TEC-001 (Next.js App Router 단일 풀스택), C-TEC-004 (Tailwind CSS + shadcn/ui)
- 기술 스택 제약: [`/tasks/SRS-v1.md#1.5.2`](/tasks/SRS-v1.md) — C-TEC-001~007 전체
- 클라이언트 명세: [`/tasks/SRS-v1.md#3.2`](/tasks/SRS-v1.md) — Web App 및 Mobile Web (반응형)
- 태스크 리스트: [`/tasks/task-list-v1.md`](/tasks/task-list-v1.md) — Phase 0: Scaffolding

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] **1. Next.js 프로젝트 생성** — `npx -y create-next-app@latest ./` (App Router, TypeScript, ESLint, Tailwind CSS 옵션 활성화)
- [ ] **2. TypeScript 엄격 모드 확인** — `tsconfig.json`에서 `"strict": true` 활성화 상태 확인
- [ ] **3. Tailwind CSS 설정 검증** — `tailwind.config.ts` 생성 확인, `globals.css`에 Tailwind 디렉티브(@tailwind base/components/utilities) 포함 확인
- [ ] **4. shadcn/ui 초기화** — `npx -y shadcn-ui@latest init` 실행, `components.json` 설정(New York 스타일, CSS Variables 사용)
- [ ] **5. shadcn/ui 기본 컴포넌트 설치** — Button, Card, Input, Select, Toggle, Badge, Dialog, Toast 등 MVP에 필요한 핵심 컴포넌트 사전 설치
- [ ] **6. 디렉토리 구조 수립** — 아래 표준 구조 생성:
  ```
  src/
  ├── app/                  # App Router pages & layouts
  │   ├── (auth)/           # 인증 관련 라우트 그룹
  │   ├── (main)/           # 메인 보호 라우트 그룹
  │   ├── api/              # Route Handlers
  │   │   ├── auth/         # NextAuth.js
  │   │   ├── cron/         # Vercel Cron
  │   │   ├── recommendations/ # 추천 카드 API
  │   │   └── admin/        # 운영 엔드포인트
  │   ├── layout.tsx        # Root Layout
  │   └── page.tsx          # 홈 페이지
  ├── components/           # 공유 UI 컴포넌트
  │   └── ui/               # shadcn/ui 컴포넌트
  ├── lib/                  # 유틸리티, 헬퍼 함수
  │   ├── db/               # Prisma 클라이언트 (DB-001에서 구현)
  │   ├── schemas/          # Zod 스키마 (DTO-* 에서 구현)
  │   └── analytics/        # PostHog 헬퍼 (EVT-* 에서 구현)
  ├── actions/              # Server Actions
  └── types/                # 공유 TypeScript 타입
  ```
- [ ] **7. 기본 레이아웃 및 메타데이터 설정** — `app/layout.tsx`에 기본 메타데이터(title, description), 한국어 lang 설정, 기본 폰트(Inter/Pretendard) 적용
- [ ] **8. `.env.example` 파일 생성** — 필요한 환경 변수 목록 정의(값 없이 키만):
  ```
  DATABASE_URL=
  NEXTAUTH_SECRET=
  NEXTAUTH_URL=
  GOOGLE_CLIENT_ID=
  GOOGLE_CLIENT_SECRET=
  GEMINI_API_KEY=
  GEMINI_MODEL=
  POSTHOG_KEY=
  POSTHOG_HOST=
  ONESIGNAL_APP_ID=
  ONESIGNAL_REST_API_KEY=
  CRON_SECRET=
  ```
- [ ] **9. 로컬 개발 서버 기동 확인** — `npm run dev` 실행 후 `http://localhost:3000` 접속, 기본 페이지 렌더링 확인
- [ ] **10. `.gitignore` 정리** — `node_modules/`, `.env.local`, `.next/`, `prisma/*.db` 등 포함 확인

## :test_tube: Acceptance Criteria (BDD/GWT)

**Scenario 1: 프로젝트 빌드 성공**
- Given: 프로젝트가 생성되고 모든 의존성이 설치된 상태
- When: `npm run build`를 실행하면
- Then: 빌드 오류 없이 성공하고, `.next/` 디렉토리가 생성되어야 한다.

**Scenario 2: 개발 서버 기동**
- Given: 프로젝트가 빌드 가능한 상태
- When: `npm run dev`를 실행하면
- Then: `http://localhost:3000`에서 Next.js 기본 페이지가 정상 렌더링되어야 한다.

**Scenario 3: Tailwind CSS 동작 확인**
- Given: `globals.css`에 Tailwind 디렉티브가 포함된 상태
- When: 임의의 컴포넌트에 Tailwind 유틸리티 클래스(예: `bg-blue-500 text-white`)를 적용하면
- Then: 브라우저에서 해당 스타일이 정상 적용되어 렌더링되어야 한다.

**Scenario 4: shadcn/ui 컴포넌트 사용 가능**
- Given: `npx shadcn-ui init`이 완료되고 Button 컴포넌트가 설치된 상태
- When: 페이지에서 `<Button>` 컴포넌트를 import하여 사용하면
- Then: shadcn/ui 스타일이 적용된 버튼이 정상 렌더링되어야 한다.

**Scenario 5: App Router 라우팅 동작**
- Given: `app/` 디렉토리에 표준 구조가 생성된 상태
- When: `app/(main)/page.tsx`를 생성하고 접근하면
- Then: Root Layout이 적용된 상태로 해당 페이지가 렌더링되어야 한다.

## :gear: Technical & Non-Functional Constraints
- **프레임워크:** Next.js 14+ (App Router 필수, Pages Router 사용 금지) — SRS C-TEC-001
- **스타일링:** Tailwind CSS + shadcn/ui 필수 — SRS C-TEC-004
- **TypeScript:** Strict mode 필수
- **배포 호환:** Vercel Platform에서 구동 가능해야 함 — SRS C-TEC-007
- **번들 사이즈:** First Load JS 150KB 이하 목표 — SRS REQ-NF-073
- **반응형:** PC 및 모바일 브라우저 양쪽에서 동작 — SRS §3.2

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria(Scenario 1~5)를 충족하는가?
- [ ] `npm run build`가 경고 없이 성공하는가?
- [ ] 표준 디렉토리 구조(`app/`, `components/`, `lib/`, `actions/`, `types/`)가 생성되었는가?
- [ ] `.env.example`에 모든 필요 환경 변수 키가 정의되었는가?
- [ ] ESLint/TypeScript 컴파일 에러가 0건인가?
- [ ] `.gitignore`에 민감 파일이 모두 포함되었는가?
- [ ] README.md에 로컬 개발 환경 구성 방법이 간략히 기술되었는가?

## :construction: Dependencies & Blockers
- **Depends on:** None (루트 태스크)
- **Blocks:** DB-001 (Prisma 초기화), INFRA-002 (Vercel 배포 연결), LLM-C01 (Vercel AI SDK 설정) 및 이후 모든 개발 태스크
