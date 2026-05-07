# Arcana — FlutterFlow 화면 설계 의뢰서

> **플랫폼**: Web App (Responsive)
> **프레임워크**: FlutterFlow
> **디자인 테마**: 신비주의 (Dark base · Deep navy + Gold accent)
> **인증**: Firebase Auth (Google OAuth)
> **DB**: Firestore
> **AI 연동**: Arcana 에이전트 파이프라인 (Hermes → Chiron → Thoth → Minerva)

---

## 디자인 시스템

| 항목 | 값 |
|------|-----|
| Primary Background | `#0D0E1A` (Deep Midnight) |
| Surface | `#161828` |
| Card | `#1E2035` |
| Accent Gold | `#C9A84C` |
| Accent Gold Light | `#E8C97A` |
| Text Primary | `#F0EEE6` |
| Text Secondary | `#8A8FA8` |
| Border | `#2E3150` |
| Success | `#3DAA7D` |
| Warning | `#D4A020` |
| Error | `#C05050` |
| Font (Display) | `Cinzel` (제목·로고) |
| Font (Body) | `Inter` (본문·UI) |
| Border Radius | `12px` (카드), `8px` (버튼·입력) |

---

## Screen 1 — Google 로그인

**Route**: `/login`
**목적**: 신규·기존 사용자 구글 OAuth 인증

---

### 레이아웃 구조

```
┌─────────────────────────────────────┐
│                                     │
│         [✦ ARCANA 로고]             │  ← Cinzel / Gold, 32px
│    "지식의 신전에 오신 것을 환영합니다"  │  ← Inter / Secondary, 14px
│                                     │
│  ─────────────────────────────────  │  ← Divider (Gold 20% opacity)
│                                     │
│       [ G  Google로 계속하기  ]      │  ← Google Sign-In Button
│                                     │
│    아직 계정이 없으신가요? [가입하기]  │  ← TextButton
│                                     │
│  ─────────────────────────────────  │
│   © 2026 Arcana  ·  이용약관  ·  개인정보처리방침  │
└─────────────────────────────────────┘
```

---

### 위젯 명세

| 위젯 | 타입 | 속성 |
|------|------|------|
| 로고 텍스트 `✦ ARCANA` | `Text` | Font: Cinzel 32px, Color: `#C9A84C` |
| 서브타이틀 | `Text` | Font: Inter 14px, Color: `#8A8FA8` |
| 구분선 | `Divider` | Color: `#C9A84C` 20% opacity |
| Google 로그인 버튼 | `GoogleSignInButton` | Background: `#1E2035`, Border: `#2E3150`, TextColor: `#F0EEE6` |
| 가입하기 | `TextButton` | Color: `#C9A84C`, underline |
| 배경 | `Container` | Background: `#0D0E1A`, 별자리 패턴 SVG asset 오버레이 |

---

### State 변수

| 변수 | 타입 | 초기값 |
|------|------|--------|
| `isLoading` | `bool` | `false` |
| `errorMessage` | `String?` | `null` |

---

### Actions

```
[Google로 계속하기] 탭
  1. isLoading = true
  2. signInWithGoogle() → Firebase Auth
  3. 성공: Firestore /users/{uid} upsert (displayName, email, photoUrl, createdAt)
  4. navigate → /upload  (첫 방문) 또는 /home (기존 사용자)
  5. 실패: errorMessage 표시 → SnackBar (Red)
  6. finally: isLoading = false
```

---

### FlutterFlow 설정 메모

- Firebase Auth → Google provider 활성화
- `GoogleSignInButton` → Sign-In With Google 공식 컴포넌트 사용
- 배경 asset: `/assets/arcana_bg_stars.svg` (별자리 dot 패턴, 5% opacity)
- 로딩 상태: 버튼 위에 `CircularProgressIndicator` (Gold color) overlay

---

---

## Screen 2 — 논문 업로드

**Route**: `/upload`
**목적**: PDF 논문 파일을 업로드하고 Arcana 파이프라인 시작

---

### 레이아웃 구조

```
┌─────────────────────────────────────┐
│  ← 뒤로   ✦ ARCANA        [아바타]  │  ← AppBar
├─────────────────────────────────────┤
│                                     │
│  "논문을 업로드하세요"               │  ← Cinzel 22px / Gold
│  "Hermes가 문서를 분석합니다"        │  ← Inter 13px / Secondary
│                                     │
│  ┌─── - - - - - - - - - - - ───┐   │
│  │                              │   │
│  │        ↑ 아이콘              │   │
│  │  PDF, DOC 파일을 드래그하거나 │   │  ← DragDrop Zone (Dashed border Gold)
│  │    [파일 선택] 버튼을 누르세요 │   │
│  │    최대 50MB                 │   │
│  └─── - - - - - - - - - - - ───┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │ 📄 paper_name.pdf    [✕]    │   │  ← 선택된 파일 미리보기 카드
│  │ 2.4 MB · PDF                 │   │    (파일 선택 후 표시)
│  └──────────────────────────────┘   │
│                                     │
│  에이전트 파이프라인 선택             │  ← Section Label
│  [✦ Hermes] [Chiron] [Thoth] [Minerva] │  ← Chip Multi-select
│  (기본: 전체 선택)                   │
│                                     │
│  출력 형식                           │
│  ○ Markdown   ● JSON   ○ JSONL     │  ← RadioGroup
│                                     │
│  [ ✦  파이프라인 실행하기  ]         │  ← Primary CTA Button (Gold)
│                                     │
└─────────────────────────────────────┘
```

---

### 위젯 명세

| 위젯 | 타입 | 속성 |
|------|------|------|
| AppBar | `AppBar` | Background: `#0D0E1A`, Title: Cinzel |
| 제목 | `Text` | Cinzel 22px, `#C9A84C` |
| 부제목 | `Text` | Inter 13px, `#8A8FA8` |
| 드래그 드롭 영역 | `FilePicker` + `Container` | Dashed border `#C9A84C`, radius 12px, height 160px |
| 파일 미리보기 카드 | `Container` | Background `#1E2035`, visible = `selectedFile != null` |
| 파일명 | `Text` | `selectedFile.name` |
| 파일 삭제 | `IconButton` | Icon: close, onTap → clearFile() |
| 에이전트 Chip | `ChoiceChips` (Multi) | Selected: Gold border + Gold text, Default: `#2E3150` border |
| 출력 형식 | `RadioGroup` | Active color: `#C9A84C` |
| 실행 버튼 | `ElevatedButton` | Background: `#C9A84C`, TextColor: `#0D0E1A`, Cinzel 16px |

---

### State 변수

| 변수 | 타입 | 초기값 |
|------|------|--------|
| `selectedFile` | `FFUploadedFile?` | `null` |
| `selectedAgents` | `List<String>` | `['hermes','chiron','thoth','minerva']` |
| `outputFormat` | `String` | `'json'` |
| `isUploading` | `bool` | `false` |
| `uploadProgress` | `double` | `0.0` |

---

### Actions

```
[파일 선택] 탭
  1. FilePicker → allowedExtensions: [pdf, doc, docx, txt]
  2. 파일 크기 검증 (> 50MB → 에러 SnackBar)
  3. selectedFile 업데이트 → 미리보기 카드 표시

[파이프라인 실행하기] 탭
  1. 유효성 검사: selectedFile != null
  2. isUploading = true
  3. Firebase Storage 업로드: /papers/{uid}/{timestamp}_{filename}
  4. Firestore /jobs 컬렉션 도큐먼트 생성:
     { uid, fileUrl, fileName, agents: selectedAgents,
       outputFormat, status: 'processing', createdAt }
  5. jobId 획득
  6. navigate → /summary/{jobId}
```

---

### FlutterFlow 설정 메모

- `FilePicker` → multipleFiles: false, mediaType: custom, extensions: pdf/doc/docx/txt
- Upload Progress: `LinearProgressIndicator` (Gold), visible = `isUploading`
- 실행 버튼 비활성화 조건: `selectedFile == null || isUploading`

---

---

## Screen 3 — 논문 요약 (Summary)

**Route**: `/summary/:jobId`
**목적**: Hermes + Thoth가 생성한 논문 전체 요약 제공
**에이전트**: Hermes (파싱) → Thoth (요약 생성) → Minerva (검토)

---

### 레이아웃 구조

```
┌─────────────────────────────────────┐
│  ← 뒤로   ✦ ARCANA        [아바타]  │  ← AppBar
├─────────────────────────────────────┤
│                                     │
│  "논문 요약"                         │  ← Cinzel 22px / Gold
│  paper_name.pdf  ·  처리 완료 ✓     │  ← Inter 12px / Secondary
│                                     │
│  [ 요약 ] [ 분석·비평 ] [ 관련연구 ] │  ← TabBar (현재: 요약 탭 active)
│ ─────────────────────────────────── │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  📋 핵심 정보                 │   │  ← MetaCard
│  │  제목: _____________________ │   │
│  │  저자: _____________________ │   │
│  │  연도: ____  저널: _________ │   │
│  │  키워드: [tag] [tag] [tag]   │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  🔍 연구 배경 및 목적         │   │  ← SectionCard
│  │  __________________________ │   │
│  │  __________________________ │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  ⚗ 방법론 요약               │   │  ← SectionCard
│  │  __________________________ │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  📊 주요 결과                 │   │  ← SectionCard
│  │  __________________________ │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  💡 결론 및 기여              │   │  ← SectionCard
│  │  __________________________ │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  ⚠ Minerva 검토 의견         │   │  ← ReviewCard (Gold border)
│  │  [APPROVED ✓] 신뢰도: 4.8/5 │   │
│  │  __________________________ │   │
│  └──────────────────────────────┘   │
│                                     │
│  [📋 복사]  [⬇ 내보내기]  [▶ 분석으로]│  ← Action Row
└─────────────────────────────────────┘
```

---

### 위젯 명세

| 위젯 | 타입 | 속성 |
|------|------|------|
| TabBar | `TabBar` | 탭 3개, IndicatorColor: `#C9A84C`, LabelColor: Gold |
| MetaCard | `Container` | Background: `#1E2035`, 키워드 → `Wrap + Chip` |
| SectionCard × 4 | `Container` | Background: `#1E2035`, 아이콘 + 섹션 제목 + 본문 |
| ReviewCard | `Container` | Border: `#C9A84C` 1px, Status Badge |
| Status Badge `APPROVED` | `Container` | Background: `#3DAA7D` 20%, TextColor: `#3DAA7D` |
| Status Badge `RETURNED` | `Container` | Background: `#C05050` 20%, TextColor: `#C05050` |
| 신뢰도 점수 | `Text` | Gold, 14px |
| 복사 버튼 | `IconButton` | Icon: content_copy |
| 내보내기 버튼 | `OutlinedButton` | Border: Gold, Text: Gold |
| 분석으로 버튼 | `ElevatedButton` | Background: Gold |

---

### Firestore 데이터 구조

```
/jobs/{jobId}
  ├── status: 'processing' | 'summarized' | 'analyzed' | 'done' | 'error'
  └── summary (Map)
        ├── title: String
        ├── authors: List<String>
        ├── year: String
        ├── journal: String
        ├── keywords: List<String>
        ├── background: String
        ├── methodology: String
        ├── results: String
        ├── conclusion: String
        └── minervaReview (Map)
              ├── status: 'APPROVED' | 'RETURNED'
              ├── score: double (1.0~5.0)
              └── comment: String
```

---

### Actions

```
화면 진입 시 (initState)
  1. Firestore /jobs/{jobId} 실시간 스트림 구독
  2. status == 'processing' → LoadingOverlay 표시 (Lottie: 별자리 애니메이션)
  3. status == 'summarized' → 데이터 바인딩 후 화면 렌더링
  4. status == 'error' → ErrorCard 표시

[복사] 탭
  → Clipboard.setData(전체 요약 텍스트)
  → SnackBar "클립보드에 복사되었습니다"

[내보내기] 탭
  → Markdown / JSON 파일 다운로드

[분석으로] 탭
  → navigate → /analysis/{jobId}
```

---

### FlutterFlow 설정 메모

- Firestore realtime stream binding: `/jobs/{jobId}` → document variable `jobData`
- 로딩 오버레이: `Stack` + `Lottie` (별자리 파티클 JSON asset `/assets/lottie_arcana_loading.json`)
- 각 SectionCard는 `Column` + 섹션 제목 `Text (Cinzel 14px Gold)` + 본문 `Text (Inter 14px)`

---

---

## Screen 4 — 분석 및 비평 (Analysis & Critique)

**Route**: `/analysis/:jobId`
**목적**: 논문 방법론·결과를 관련 논문들과 비교 분석·비평
**에이전트**: Thoth (비교 추론) → Minerva (비평 확정)

---

### 레이아웃 구조

```
┌─────────────────────────────────────┐
│  ← 뒤로   ✦ ARCANA        [아바타]  │  ← AppBar
├─────────────────────────────────────┤
│                                     │
│  "분석 및 비평"                       │  ← Cinzel 22px / Gold
│  paper_name.pdf  ·  Thoth & Minerva │
│                                     │
│  [ 요약 ] [ 분석·비평 ] [ 관련연구 ] │  ← TabBar (현재: 분석·비평 active)
│ ─────────────────────────────────── │
│                                     │
│  ▼ 방법론 분석                        │  ← CollapsibleSection Header
│  ┌──────────────────────────────┐   │
│  │ 본 논문 방법론:               │   │
│  │  __________________________ │   │
│  │                              │   │
│  │ 비교 논문들과의 차이점:        │   │
│  │  ┌────────┬────────────────┐ │   │
│  │  │ 논문   │ 방법론 요약    │ │   │  ← ComparisonTable
│  │  ├────────┼────────────────┤ │   │
│  │  │ 본 논문│ ______________ │ │   │
│  │  │ Ref A  │ ______________ │ │   │
│  │  │ Ref B  │ ______________ │ │   │
│  │  └────────┴────────────────┘ │   │
│  └──────────────────────────────┘   │
│                                     │
│  ▼ 결과 비교                          │  ← CollapsibleSection Header
│  ┌──────────────────────────────┐   │
│  │  [BarChart: 성능 지표 비교]   │   │  ← Chart (fl_chart)
│  │  본논문 vs Ref A vs Ref B    │   │
│  └──────────────────────────────┘   │
│                                     │
│  ▼ Thoth 심층 비평                    │  ← CollapsibleSection Header
│  ┌──────────────────────────────┐   │
│  │ 강점 ✓                       │   │
│  │  · _________________________│   │
│  │  · _________________________│   │
│  │                              │   │
│  │ 한계 및 약점 ✗                │   │
│  │  · _________________________│   │
│  │  · _________________________│   │
│  │                              │   │
│  │ 개선 제언 ↑                  │   │
│  │  · _________________________│   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │  ⚠ Minerva 최종 판정         │   │  ← ReviewCard (Gold border)
│  │  [APPROVED ✓]  신뢰도: 4.6/5 │   │
│  │  __________________________ │   │
│  └──────────────────────────────┘   │
│                                     │
│  [📋 복사]  [⬇ 내보내기]  [▶ 관련연구]│  ← Action Row
└─────────────────────────────────────┘
```

---

### 위젯 명세

| 위젯 | 타입 | 속성 |
|------|------|------|
| CollapsibleSection | `ExpansionTile` | 아이콘: Gold chevron, 헤더: Cinzel 16px |
| ComparisonTable | `DataTable` | Header background: `#2E3150`, row alt: `#1E2035` |
| BarChart | `fl_chart BarChart` | Bar colors: Gold (본논문) + `#3B8BD4`/`#1D9E75` (비교) |
| 강점 리스트 | `ListView` | Bullet color: `#3DAA7D` |
| 한계 리스트 | `ListView` | Bullet color: `#C05050` |
| 개선 제언 리스트 | `ListView` | Bullet color: `#C9A84C` |
| ReviewCard | `Container` | Gold 1px border |

---

### Firestore 데이터 구조

```
/jobs/{jobId}
  └── analysis (Map)
        ├── methodology (Map)
        │     ├── thisPaper: String
        │     └── comparisons: List<Map>
        │           ├── paperId: String
        │           ├── title: String
        │           └── methodologySummary: String
        ├── results (Map)
        │     ├── metrics: List<Map> { label, thisPaper, refA, refB }
        │     └── chartData: List<Map>
        ├── critique (Map)
        │     ├── strengths: List<String>
        │     ├── weaknesses: List<String>
        │     └── suggestions: List<String>
        └── minervaReview (Map)
              ├── status: 'APPROVED' | 'RETURNED'
              ├── score: double
              └── comment: String
```

---

### Actions

```
화면 진입 시
  1. Firestore /jobs/{jobId} 스트림 구독
  2. analysis 서브맵 데이터 바인딩
  3. chartData → fl_chart BarChart 렌더링

[관련연구로] 탭
  → navigate → /related/{jobId}
```

---

### FlutterFlow 설정 메모

- `fl_chart` 패키지 pubspec 추가
- BarChart 색상: `thisPaper` → `#C9A84C`, 비교 논문들 → Blue/Green 계열
- ComparisonTable 헤더 행: `#2E3150` shading, TextColor: Gold
- ExpansionTile 기본 상태: 방법론 분석만 expanded, 나머지 collapsed

---

---

## Screen 5 — 최신 관련 연구 (Related Studies)

**Route**: `/related/:jobId`
**목적**: 논문 키워드 기반 최신 related study 탐색 및 제공
**에이전트**: Hermes (키워드 추출) → Thoth (관련성 평가·요약) → Minerva (최종 선별)

---

### 레이아웃 구조

```
┌─────────────────────────────────────┐
│  ← 뒤로   ✦ ARCANA        [아바타]  │  ← AppBar
├─────────────────────────────────────┤
│                                     │
│  "최신 관련 연구"                     │  ← Cinzel 22px / Gold
│  Thoth가 선별한 연구 · 최신순         │
│                                     │
│  [ 요약 ] [ 분석·비평 ] [ 관련연구 ] │  ← TabBar (현재: 관련연구 active)
│ ─────────────────────────────────── │
│                                     │
│  검색 키워드                          │  ← Section Label
│  [transformer] [attention] [NLP]    │  ← Keyword Chips (from Hermes)
│  [+ 키워드 추가]                      │  ← AddChip
│                                     │
│  [최신순 ↓]  [관련도순]  [연도 필터▾] │  ← Sort/Filter Row
│                                     │
│  ┌──────────────────────────────┐   │
│  │ ★★★★★  관련도 98%           │   │  ← RelatedPaperCard
│  │ "Attention Is All You Need"  │   │
│  │ Vaswani et al. · 2017 · NeurIPS│  │
│  │                              │   │
│  │ Thoth 요약:                  │   │
│  │ __________________________ │   │
│  │                              │   │
│  │ [원문 보기 ↗]  [저장]        │   │
│  └──────────────────────────────┘   │
│                                     │
│  ┌──────────────────────────────┐   │
│  │ ★★★★☆  관련도 85%           │   │  ← RelatedPaperCard
│  │ "BERT: Pre-training of..."   │   │
│  │ Devlin et al. · 2019 · NAACL │   │
│  │                              │   │
│  │ Thoth 요약:                  │   │
│  │ __________________________ │   │
│  │                              │   │
│  │ [원문 보기 ↗]  [저장]        │   │
│  └──────────────────────────────┘   │
│                                     │
│  (리스트 계속...)                     │
│                                     │
│  [ ↻ 더 불러오기 ]                   │  ← Load More Button
│                                     │
└─────────────────────────────────────┘
```

---

### 위젯 명세

| 위젯 | 타입 | 속성 |
|------|------|------|
| 키워드 Chip | `Wrap + InputChip` | Background: `#2E3150`, Selected: Gold bg, deletable |
| 키워드 추가 | `InputChip` | 점선 테두리 Gold, leading icon: add |
| 정렬 버튼 | `ChoiceChips` | 최신순 / 관련도순 |
| 연도 필터 | `DropdownButton` | 2020 / 2021 / 2022 / 2023 / 2024 / 전체 |
| RelatedPaperCard | `Container` | Background: `#1E2035`, border `#2E3150` |
| 관련도 별점 | `RatingBar (read-only)` | Active: Gold, Inactive: `#2E3150` |
| 관련도 % 뱃지 | `Container` | Background: Gold 20%, TextColor: Gold |
| 제목 | `Text` | Inter 15px, Bold, `#F0EEE6` |
| 저자/연도 | `Text` | Inter 12px, `#8A8FA8` |
| Thoth 요약 | `Text` | Inter 13px, `#C8C6BE`, 3줄 clamp |
| 원문 보기 | `TextButton` | Color: `#C9A84C`, trailing icon: open_in_new |
| 저장 버튼 | `OutlinedButton` | Border: Gold, Icon: bookmark |
| 더 불러오기 | `OutlinedButton` | Border: `#2E3150`, full width |

---

### Firestore 데이터 구조

```
/jobs/{jobId}
  └── relatedStudies: List<Map>
        ├── title: String
        ├── authors: String
        ├── year: int
        ├── venue: String
        ├── url: String
        ├── relevanceScore: double (0.0~1.0)
        ├── thotSummary: String
        └── savedBy: List<String> (uid list)

/users/{uid}/savedPapers (서브컬렉션)
  └── { paperId, title, url, savedAt }
```

---

### Actions

```
화면 진입 시
  1. Firestore /jobs/{jobId}.relatedStudies 로드
  2. 관련도순 기본 정렬 (relevanceScore desc)
  3. keywords Chip 리스트 렌더링 (summary.keywords 참조)

[정렬 변경] 탭
  → relatedStudies 리스트 재정렬 (클라이언트 사이드)

[연도 필터 변경]
  → 해당 연도 이상 필터링

[키워드 추가]
  → TextField 팝업 → 키워드 추가 → Firestore /jobs/{jobId} keywords 업데이트
  → re-trigger Thoth 검색 (Cloud Function 호출)

[원문 보기] 탭
  → url_launcher → 새 탭에서 열기

[저장] 탭
  → Firestore /users/{uid}/savedPapers 추가
  → 버튼 상태 → "저장됨 ✓" (Gold filled)

[더 불러오기] 탭
  → Firestore pagination (lastDocument 기준 10개씩)
```

---

### FlutterFlow 설정 메모

- `url_launcher` 패키지 pubspec 추가
- RelatedPaperCard → `ListView.builder` with `shrinkWrap: true`
- 저장 버튼 상태: `isSaved` local variable → Conditional Icon (`bookmark` / `bookmark_added`)
- 더 불러오기: Firestore `startAfterDocument` pagination 적용

---

---

## 화면 전환 흐름 (Navigation Flow)

```
/login
  └─→ [로그인 성공]
        ├─→ /upload          (신규 사용자 또는 직접 접근)
        └─→ /home            (기존 사용자 - 향후 구현)

/upload
  └─→ [파이프라인 실행]
        └─→ /summary/:jobId  (처리 완료 대기)

/summary/:jobId
  ├─→ Tab: 요약 (현재)
  ├─→ Tab → /analysis/:jobId
  └─→ Tab → /related/:jobId

/analysis/:jobId
  ├─→ Tab → /summary/:jobId
  ├─→ Tab: 분석·비평 (현재)
  └─→ Tab → /related/:jobId

/related/:jobId
  ├─→ Tab → /summary/:jobId
  ├─→ Tab → /analysis/:jobId
  └─→ Tab: 관련연구 (현재)
```

---

## Firebase 구조 요약

```
Firestore
├── users/{uid}
│     ├── displayName, email, photoUrl, createdAt
│     └── savedPapers/ (서브컬렉션)
│           └── {paperId}: { title, url, savedAt }
│
├── jobs/{jobId}
│     ├── uid, fileName, fileUrl
│     ├── agents: List<String>
│     ├── outputFormat: String
│     ├── status: 'processing' | 'summarized' | 'analyzed' | 'done' | 'error'
│     ├── createdAt: Timestamp
│     ├── summary: Map (Screen 3 데이터)
│     ├── analysis: Map (Screen 4 데이터)
│     └── relatedStudies: List<Map> (Screen 5 데이터)

Firebase Storage
└── papers/{uid}/{timestamp}_{filename}

Firebase Auth
└── Google OAuth Provider
```

---

## 패키지 목록

| 패키지 | 용도 |
|--------|------|
| `firebase_auth` | Google 인증 |
| `cloud_firestore` | DB 실시간 연동 |
| `firebase_storage` | 파일 업로드 |
| `file_picker` | 로컬 파일 선택 |
| `fl_chart` | 분석 화면 Bar/Line 차트 |
| `url_launcher` | 논문 원문 외부 링크 |
| `lottie` | 로딩 애니메이션 |
| `google_fonts` | Cinzel + Inter |
| `flutter_rating_bar` | 관련도 별점 표시 |
| `share_plus` | 내보내기 공유 기능 |

---

## 연관 노트

- [[Arcana 에이전트 시스템]]
- [[Arcana_Agent_System]]
- [[Firebase 프로젝트 설정]]
- [[Arcana API 연동 명세]]
