# Academic Results Writer v3.0 — Overview

> RAG 기반 Dual-Layer 아키텍처를 활용한 학술 논문 Results 섹션 자동 작성 시스템

**버전**: 3.0 | **업데이트**: 2026-03-11 | **스킬 호출**: `/academic-results-writer`

---

## 목차

1. [시스템 개요](#1-시스템-개요)
2. [아키텍처](#2-아키텍처)
3. [에이전트 상세](#3-에이전트-상세)
4. [RAG 연동](#4-rag-연동)
5. [파이프라인 상세 (Phase -1 ~ 4)](#5-파이프라인-상세)
6. [사용법](#6-사용법)
7. [Fallback 체계](#7-fallback-체계)
8. [파일 구조](#8-파일-구조)
9. [지원 저널 및 가이드라인](#9-지원-저널-및-가이드라인)
10. [지속적 학습](#10-지속적-학습)

---

## 1. 시스템 개요

### 무엇을 하는가?

사용자의 실험 데이터(CSV, Figure, Table)를 입력받아 **출판 수준의 Results 섹션**을 자동 생성하는 멀티에이전트 시스템이다. 레퍼런스 논문의 구조와 사용자 논문의 문체를 각각 학습하여, 구조적으로 올바르고 문체적으로 일관된 Results를 작성한다.

### v3.0 핵심 변경사항 (vs v2.0)

| 항목 | v2.0 | v3.0 |
|------|------|------|
| 레퍼런스 입력 | PDF 직접 읽기 | **RAG 검색** (langconnect-rag) |
| 스타일 학습 | 단일 레이어 | **Dual-Layer** (Structure + Target Voice) |
| 새 에이전트 | — | **Style Extractor** (문체 추출) |
| Paper Preprocessor | Primary | **Fallback** (RAG 실패 시만 작동) |
| 작성 전 인터뷰 | 없음 | **Writing Intent Interview** (Q1-Q5 필수) |
| 아웃라인 승인 | 선택적 | **단계별 유저 승인 필수** (2a→2b→2c→2d) |
| 실시간 참조 | 없음 | **RAG few-shot** (서브섹션별 실시간 검색) |
| 컬렉션 선택 | 없음 | **Phase -1** (유저가 컬렉션 선택) |
| Priority Rules | 없음 | **Structure/Voice 충돌 해소 규칙** |

---

## 2. 아키텍처

### 전체 파이프라인

```
Phase -1          Phase 0a          Phase 0b
Collection    ┌─ RAG Search ─┐  ┌─ RAG Search ─┐
Selection     │  (Structure)  │  │   (Voice)    │
    │         │      ↓        │  │      ↓       │
    │         │  Results      │  │   Style      │
    │         │  Analyzer     │  │  Extractor   │
    │         │      ↓        │  │      ↓       │
    └────────→│  Structure    │  │ Target Voice │
              │  Layer        │  │   Layer      │
              └──────┬────────┘  └──────┬───────┘
                     │    PARALLEL      │
                     └────────┬─────────┘
                              │
                     Phase 1: Style Merge
                     (Priority Rules 적용)
                              │
                     Phase 2: Results Writer
                     Step 0: Input (CSV+Fig+MD)
                     Step 1: Intent Interview
                     Step 2: Interactive Outline
                     Step 3: Prose + RAG few-shot
                              │
                     Phase 3: Results Reviewer
                     (7-pass review)
                              │
                        Approved? ─No→ Writer (재작성)
                              │
                           Yes ↓
                     Phase 4: Pattern Learner
                     (스타일 가이드 업데이트)
```

### Dual-Layer 개념

시스템은 두 개의 독립적인 학습 레이어를 운영한다:

| Layer | 역할 | 기본 컬렉션 | 학습 내용 |
|-------|------|------------|----------|
| **Structure Layer** | 논문 구조/패턴 | agentpaper | 섹션 구성, Figure 배치, 서브섹션 계층 |
| **Target Voice Layer** | 문체/톤/스타일 | mypaper | 문장 패턴, 전환어, 통계 보고 형식, 어조 |

**Priority Rules**에 의해 충돌 시 어느 레이어를 우선할지 결정된다:

```
구조 (structure)        → Structure Layer 우선
문체/톤 (voice/tone)     → Target Voice Layer 우선
통계 형식 (statistics)    → Target Voice Layer 우선
전환어 (transitions)     → Target Voice Layer 우선
Figure 참조 (figure_refs) → Structure Layer + Voice overlay
용어 (terminology)       → Target Voice Layer 우선
```

---

## 3. 에이전트 상세

### 3.1 Style Extractor (신규)

**파일**: `agents/style-extractor.md`

Target Voice 컬렉션에서 문체를 추출하는 새 에이전트. 5가지 항목을 분석한다:

| 분석 항목 | 추출 내용 | 예시 |
|-----------|----------|------|
| **문장 구조** | 평균 길이, 시작 패턴, 능동/수동 비율 | "Active 65% / Passive 35%" |
| **전환어** | 섹션 간/내 전환 패턴, 특징적 표현 | "Having established X, we next..." |
| **통계 보고** | p-value 형식, effect size, CI 패턴 | "p < 0.001" vs "P = 0.003" |
| **논리 전개** | 발견 제시 순서, Figure 참조 방식 | "context → finding → figure ref" |
| **톤/어조** | hedging 빈도, 전문용어 밀도, "We" 빈도 | "Hedging: moderate (8/1000 words)" |

**RAG 쿼리 4종**:
1. `"Results section findings statistical analysis"` — 통계 보고 패턴
2. `"we found demonstrated showed revealed"` — 결과 보고 동사
3. `"Figure Table panel comparison significantly"` — Figure 연동 패턴
4. 분야별 특화 쿼리 (spatial transcriptomics, single-cell 등)

### 3.2 Results Analyzer (수정)

**파일**: `agents/results-analyzer.md`

| 항목 | v2.0 | v3.0 |
|------|------|------|
| 입력 소스 | Paper Preprocessor (PDF) | **RAG 검색 결과** |
| 입력 형태 | Markdown 추출 텍스트 | RAG search chunks |
| 대체 입력 | 없음 | User-pasted text, Preprocessor fallback |
| 출력 대상 | style-guide.md (전체) | **Structure Layer 섹션** |

**분석 6단계**: 구조 추출 → 흐름 분석 → Figure 통합 → 통계 패턴 → 전환어 매핑 → 톤 분석 → 다문서 종합

### 3.3 Results Writer (대폭 수정)

**파일**: `agents/results-writer.md`

**v3.0 작성 프로세스** (4단계):

```
Step 0: Input Collection
├── CSV 데이터 파일 + 설명
├── Figure 파일 + 설명 + 핵심 발견
├── Table + key metrics
├── Results context (.md 파일)      ← NEW
├── Experimental context
└── Journal target / Reporting guideline (optional)

Step 1: Writing Intent Interview (필수)
├── Q1. 이번 Results 중심 내용은?
├── Q2. 강조할 발견/비교는?
├── Q3. 서브섹션 순서 선호?
├── Q4. 이전 Results 부분 존재 여부?
│   ├── 있음 → 전환 연결 계획
│   └── 없음 → 첫 부분 시작 전략
└── Q5. 참고 논문/스타일?

Step 2: Interactive Outline (각 단계 유저 승인 필수)
├── 2a. 전체 구조 제안 → ✋ USER APPROVAL
├── 2b. 서브섹션별 핵심 포인트 → ✋ USER APPROVAL
├── 2c. Figure/Table 배치 → ✋ USER APPROVAL
└── 2d. 전환 계획 → ✋ USER APPROVAL

Step 3: Prose + RAG few-shot
├── 서브섹션별 키워드 추출
├── Target Voice RAG 검색 → 2-3개 스타일 exemplar
├── 문체 매칭하여 prose 작성
└── Integration pass (용어, Figure ref, 통계, 흐름, word count)
```

### 3.4 Results Reviewer (변경 없음)

**파일**: `agents/results-reviewer.md`

7-pass 리뷰 프로세스 유지:

| Pass | 검토 항목 | 점수 |
|------|----------|------|
| Pass 1 | Factual Accuracy — 데이터와 주장 일치 | 1-10 |
| Pass 2 | Statistical Review — p-value, 검정, 효과 크기 | 1-10 |
| Pass 3 | Structural Review — 논리적 흐름, 전환 | 1-10 |
| Pass 4 | Style & Clarity — 용어 일관성, 간결성 | 1-10 |
| Pass 5 | Completeness — 모든 Figure/Table 참조 | 1-10 |
| Pass 6 | Reporting Guideline — STROBE/CONSORT 등 준수 | 1-10 |
| Pass 7 | Reproducibility — 재현 가능 수준 상세도 | 1-10 |

**Revision loop**: major/minor revisions → Writer에게 `revision_diff`와 함께 반환 (최대 3회)

### 3.5 Pattern Learner (소폭 수정)

**파일**: `agents/pattern-learner.md`

v3.0 추가 학습 소스:

| 소스 | v2.0 | v3.0 |
|------|------|------|
| Source 1: Approved Sections | O | O |
| Source 2: Reviewer Feedback | O | O |
| Source 3: Reference Patterns | O | O |
| Source 4: Preprocessor Effectiveness | O | → **RAG Effectiveness** |
| **Source 5: Style Extractor Feedback** | — | **NEW** |

**Source 5 추적 항목**:
- Target Voice Layer 정확도 (생성된 문체 vs 유저 수정)
- 유저 문체 수정 시 학습 (voice gap 분석)
- 재추출 트리거 (3회 이상 voice 관련 수정 시)

### 3.6 Paper Preprocessor (Fallback)

**파일**: `agents/paper-preprocessor.md`

| 항목 | v2.0 | v3.0 |
|------|------|------|
| 역할 | Primary preprocessor | **Fallback preprocessor** |
| 활성화 조건 | 항상 | RAG 실패 시만 |
| PDF 처리 로직 | 동일 | 동일 (변경 없음) |

**Fallback 활성화 조건**:
- RAG 컬렉션 비어있음 또는 미설정
- RAG 검색 결과 3개 미만
- RAG MCP 서버 연결 실패
- 유저가 명시적으로 PDF 제공
- 유저가 직접 PDF 처리 요청

---

## 4. RAG 연동

### 사용 컬렉션

```yaml
agentpaper:
  id: "06bd503e-fd03-4451-8ce7-7c1ee5012584"
  용도: Structure Layer — 레퍼런스 논문 (구조/패턴 학습)
  문서 수: 30+ (전체 PDF 청킹)

mypaper:
  id: "fd3c6832-12b2-46e6-b724-a2fe62f5da09"
  용도: Target Voice Layer — 본인 논문 + 스타일 참고 논문 (문체/톤 학습)
  문서 수: 30+ (전체 PDF 청킹)
```

**참고**: 유저는 Phase -1에서 다른 컬렉션을 선택할 수 있고, 같은 컬렉션을 양쪽에 사용하는 것도 가능하다.

### MCP 도구

| 도구 | 용도 |
|------|------|
| `mcp__langconnect-rag__list_collections` | 사용 가능한 컬렉션 목록 조회 |
| `mcp__langconnect-rag__search_documents` | hybrid 검색 (semantic + keyword) |
| `mcp__langconnect-rag__agentic_search` | AI 기반 질의응답 |

### 검색 파라미터

```yaml
search_type: "hybrid"    # semantic + keyword 결합
limit: 5                 # 쿼리당 최대 5개 chunk
```

### 쿼리 전략 요약

| 용도 | 쿼리 수 | 대상 컬렉션 | 시점 |
|------|--------|------------|------|
| Structure Layer 학습 | 4개 | agentpaper (or 유저 선택) | Phase 0a |
| Target Voice 학습 | 4개 | mypaper (or 유저 선택) | Phase 0b |
| 실시간 few-shot | 서브섹션당 1-2개 | 양쪽 | Phase 2 Step 3 |

---

## 5. 파이프라인 상세

### Phase -1: Collection Selection

**목적**: 유저에게 RAG 컬렉션을 선택하게 한다.

**과정**:
1. `list_collections()` 호출 → 사용 가능한 컬렉션 표시
2. Q1: "Structure Layer (구조/패턴 학습)에 사용할 컬렉션은?" (기본: agentpaper)
3. Q2: "Target Voice Layer (문체/톤 학습)에 사용할 컬렉션은?" (기본: mypaper)
4. "skip" 또는 "default" → 기본값 사용

**참고**: Quick Mode에서도 Phase -1은 유지됨 (실시간 RAG few-shot에 필요)

### Phase 0a + 0b: Dual-Layer Learning (병렬 실행)

**Phase 0a** (Structure):
- agentpaper(또는 선택 컬렉션) 대상 4개 쿼리 실행
- Results Analyzer가 구조 패턴 추출
- `data/style-guide.md`의 Structure Layer 섹션 업데이트

**Phase 0b** (Voice):
- mypaper(또는 선택 컬렉션) 대상 4개 쿼리 실행
- Style Extractor가 문체 5항목 추출
- `data/style-guide.md`의 Target Voice Layer 섹션 업데이트

**두 Phase는 병렬로 실행**되어 시간 단축.

### Phase 1: Style Merge

Style Guide의 Priority Rules에 따라 두 레이어의 가이던스를 병합. 별도 파일 생성 없이 `data/style-guide.md` 자체가 merged guide 역할.

### Phase 2: Writing (Enhanced)

**Step 0 → Step 1 → Step 2 → Step 3** 순서로 진행.

핵심 변경: **Step 1 (Intent Interview)**과 **Step 2 (Interactive Outline)**이 필수가 되어 유저와의 alignment을 보장. **Step 3**에서는 서브섹션별 실시간 RAG 검색으로 few-shot 스타일 참조를 제공.

### Phase 3: Review (기존 유지)

7-pass 리뷰 → revision_diff → Writer 재작성 (최대 3회 루프)

### Phase 4: Pattern Learner (확장)

기존 학습 + RAG 효과성 추적 + Style Extractor 피드백 연동

---

## 6. 사용법

### 6.1 Complete Workflow (전체 파이프라인)

```
/academic-results-writer
```

1. **컬렉션 선택** — Structure/Voice 컬렉션 지정 (또는 기본값 사용)
2. **스타일 학습** — RAG 기반 Dual-Layer 자동 학습
3. **데이터 제공** — CSV, Figure, Table, 분석 맥락 (.md)
4. **인터뷰** — Q1-Q5 답변
5. **아웃라인** — 4단계 순차 승인
6. **Prose 생성** — 실시간 RAG few-shot 참조
7. **7-pass 리뷰** — 자동 수정 루프
8. **승인** → 패턴 학습

### 6.2 Quick Mode (스타일 가이드 이미 있을 때)

Phase 0-1을 건너뛰고 바로 작성 시작:

```
데이터/Figure 직접 제공 → Phase -1 (컬렉션 선택) → Phase 2 (작성) → Phase 3 (리뷰)
```

**사용 시점**:
- 이전 세션에서 이미 스타일 가이드 구축됨
- 빠른 작성이 필요한 경우
- 레퍼런스 논문 없이 기존 스타일로 작성

### 6.3 Style Learning Only (스타일 학습만)

```
컬렉션 선택 → Phase 0a/0b (RAG 학습) → Phase 1 (Style Merge) → 완료
```

새 레퍼런스 논문 추가 후 스타일 가이드만 업데이트하고 싶을 때.

### 6.4 Writing Only (기존 패턴으로 작성만)

```
데이터 제공 → Phase -1 (컬렉션 선택) → Phase 2 (작성) → Phase 3 (리뷰)
```

### 6.5 Input 형식 가이드

유저가 제공해야 하는 입력:

```yaml
research_materials:
  # 필수
  data_files:
    - file: "path/to/data.csv"
      description: "환자별 생존율 데이터"

  figures:
    - file: "figures/fig1.png"
      description: "UMAP plot showing cell clusters"
      key_finding: "5개의 distinct cluster 확인"
      statistics: "silhouette score = 0.73"

  experimental_context:
    study_type: "Spatial transcriptomics analysis"
    samples: "n=12, 6 tumor + 6 normal"
    methods_summary: "10x Visium, Seurat pipeline"

  # 선택
  results_context:
    - file: "analysis_summary.md"
      description: "CellChat 분석 결과 요약"

  tables:
    - file: "tables/table1.csv"
      description: "DEG analysis results"
      key_metrics: ["FDR < 0.05: 1,234 genes"]

  journal_target: "Nature Methods"
  reporting_guideline: "MIAME"
```

### 6.6 Journal Target 설정

저널을 지정하면 자동으로:
- Word limit 인식 및 초과 경고
- Figure 수 제한 적용
- 저널별 스타일 규칙 적용
- Reviewer가 저널 요건 대비 검토

**지원 저널**: Nature Methods, Bioinformatics, Genome Biology, Nucleic Acids Research, Cell Systems / Cell Reports Methods, PLOS Computational Biology

---

## 7. Fallback 체계

```
Primary                     Fallback 1                  Fallback 2
─────────────               ─────────────               ─────────────
RAG Search          ──X──→  Paper Preprocessor  ──X──→  User Input
(langconnect-rag)           (PDF 직접 처리)             (텍스트 직접 입력)
```

### Fallback 트리거

| 조건 | 다음 단계 |
|------|----------|
| RAG 서버 연결 실패 | → Paper Preprocessor |
| RAG 컬렉션 비어있음 | → Paper Preprocessor |
| RAG 결과 < 3 chunks | → Paper Preprocessor |
| 유저가 PDF 직접 제공 | → Paper Preprocessor |
| PDF 처리 실패 | → User Input 요청 |
| PDF 미제공 | → User Input 요청 |

### Fallback 시 유저 알림

```
"RAG 검색이 충분한 결과를 반환하지 못했습니다. PDF 직접 처리로 전환합니다."
```

---

## 8. 파일 구조

```
academic-results-writer/
│
├── SKILL.md                          # 오케스트레이터 (전체 파이프라인 정의)
│
├── agents/
│   ├── style-extractor.md            # [NEW] Target Voice 추출 에이전트
│   ├── results-analyzer.md           # [MOD] Structure Layer 분석 (RAG 기반)
│   ├── results-writer.md             # [MOD] 다단계 작성 (Interview + Outline + RAG)
│   ├── results-reviewer.md           # [---] 7-pass 리뷰 (변경 없음)
│   ├── pattern-learner.md            # [MOD] 지속 학습 (Voice 피드백 추가)
│   └── paper-preprocessor.md         # [MOD] Fallback 전용으로 역할 변경
│
├── data/
│   ├── style-guide.md                # [MOD] Dual-Layer 구조
│   │                                 #   ├── Structure Layer
│   │                                 #   ├── Target Voice Layer
│   │                                 #   ├── Priority Rules
│   │                                 #   ├── Statistical Reporting
│   │                                 #   ├── Transitions / Voice & Tense
│   │                                 #   ├── Reporting Guidelines
│   │                                 #   ├── Journal Conventions
│   │                                 #   └── Learned Patterns
│   ├── feedback-log.md               # 리뷰어 피드백 이력
│   ├── samples/                      # 예시 Results 섹션
│   ├── approved-sections/            # 승인된 최종 출력
│   └── pattern-history/              # 스타일 가이드 버전 관리
│       ├── style-guide-v1.md
│       ├── style-guide-v2.md
│       └── changelog.md
│
└── references/
    └── results-patterns.md           # 레퍼런스 논문 패턴 (A, B, C, D)
```

---

## 9. 지원 저널 및 가이드라인

### 지원 저널

| 저널 | Word Limit | Figure Limit | 특징 |
|------|-----------|-------------|------|
| Nature Methods | ~2,000-3,000 | 6-8 main | 간결, Figure 중심 서사 |
| Bioinformatics | ~2,000-4,000 | varies | 기술적 정밀성, 벤치마크 필수 |
| Genome Biology | 제한 없음 | 제한 없음 | 포괄적 생물학적 맥락 |
| Nucleic Acids Research | 카테고리별 | varies | Methods-heavy |
| Cell Systems | ~3,000-5,000 | 4-7 main | 시스템 생물학 관점 |
| PLOS Comp. Bio. | 제한 없음 | 제한 없음 | 교육적, 코드 공개 중시 |

### Reporting Guidelines

| 가이드라인 | 적용 대상 | 주요 체크 항목 |
|-----------|----------|--------------|
| **STROBE** | 관찰 연구 | 참가자 수, 기술통계, 주요 결과, 보조 분석 |
| **CONSORT** | RCT | 참가자 흐름, 기저 데이터, ITT, 효과 크기, 부작용 |
| **PRISMA** | 체계적 리뷰 | 선별 과정, 연구 특성, 비뚤림 위험, 합성 결과 |
| **ARRIVE** | 동물 실험 | 기저 데이터, 표본 크기, 효과 크기, 부작용 |
| **MIAME/MINSEQE** | -omics | 실험 설계, 정규화, QC, 차등 분석 |

### Field-Specific Conventions

지원 분야: Single-Cell Genomics, Machine Learning/AI Methods, Pathway Analysis, Spatial Transcriptomics, Metagenomics/Microbiome, Structural Biology/Protein Analysis

---

## 10. 지속적 학습

시스템은 사용할수록 개선된다:

```
사용 횟수 증가
    │
    ├── RAG 컬렉션 확장 → 패턴 다양성 향상
    ├── 승인된 Results 축적 → Style Guide 정교화 (양 레이어)
    ├── 리뷰어 피드백 → 반복 오류 제거
    ├── 패턴 버전 관리 → 추적 가능한 개선
    ├── Reporting 준수율 추적 → 가이드라인 갭 감소
    ├── RAG 쿼리 정제 → 검색 품질 향상
    └── Voice 정확도 추적 → 문체 모방 충실도 향상
```

### 학습 트리거

| 트리거 | 동작 |
|--------|------|
| 유저가 Results 승인 | 성공 패턴 추출 + 스타일 가이드 업데이트 |
| 리뷰어 수정 3건 이상 | 실패 패턴 분석 + 가이드 반영 |
| 10회 출력마다 | 정기 리뷰 + 버전 관리 |
| Voice 정확도 임계값 이하 | Style Extractor 재실행 요청 |

### 추적 지표

- Review pass rate (첫 제출 승인율)
- 반복 수정 유형 (감소 = 학습 중)
- RAG 검색 적중률
- Target Voice 매칭 점수
- Reporting guideline 준수율

---

## Quick Reference

```
/academic-results-writer                  # Full pipeline
/academic-results-writer + data only      # Quick mode (Phase 0-1 skip)

에이전트 6개:
  Style Extractor   → Target Voice Layer (문체)
  Results Analyzer   → Structure Layer (구조)
  Results Writer     → Interview → Outline → Prose
  Results Reviewer   → 7-pass review
  Pattern Learner    → 지속 학습
  Paper Preprocessor → Fallback (RAG 실패 시)

RAG 컬렉션:
  agentpaper → 구조 학습 (Structure Layer)
  mypaper    → 문체 학습 (Target Voice Layer)

핵심 파일:
  SKILL.md           → 파이프라인 오케스트레이터
  data/style-guide.md → Dual-Layer 스타일 가이드
```
