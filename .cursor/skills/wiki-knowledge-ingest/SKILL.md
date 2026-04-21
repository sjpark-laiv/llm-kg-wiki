---
name: wiki-knowledge-ingest
description: >-
  Adds structured Markdown pages under wiki/, normalizes terminology, and links
  named entities across documents using stable anchors and relative paths.
  When content involves causes and effects, structures wiki pages for RCA
  (root cause) and impact analysis: entity IDs, causality maps, edge tables,
  and evidence sections. Requires YAML frontmatter with wiki_created (and
  wiki_updated when editing). Use when ingesting wiki notes, building causal
  reasoning graphs, cross-linking topics, or maintaining entity index sections.
---

# Wiki 지식 추가 (Markdown + Named entity)

## 전제

- 지식 페이지는 저장소 루트의 **`wiki/`** 디렉터리에 둔다 (`wiki/topic.md`).
- 문서 간 연결은 **상대 경로**만 사용한다: `[표시](other.md)`, `[표시](other.md#anchor)`.
- 추론/파서가 따라가기 쉽게 **헤딩 계층**, **목록**, **구분선(`---`)**으로 절을 나눈다.

## 작성 시점(문서 메타데이터) — 필수

에이전트·사람이 나중에 **근거가 된 시점**을 구분할 수 있도록, `wiki/`의 모든 페이지는 **파일 맨 앞**에 YAML 프런트매터로 작성·갱신 시점을 남긴다.

- **`wiki_created`** (필수): 이 파일이 지식 베이스에 **처음 반영된 날짜**. ISO **날짜** `YYYY-MM-DD` (예: `2026-04-18`). 세션의 “오늘 날짜”가 주어지면 그것을 우선한다.
- **`wiki_updated`** (선택·권장): 본문을 **실질적으로 고친 날짜**. 최초 작성 시에는 `wiki_created`와 같게 두거나 생략한다. 이후 편집마다 최종 수정일로 갱신한다.
- **`content_as_of`** (선택): 본문 사실·수치·발언이 **어느 시점까지의 것인지**가 파일 작성일과 다를 때만 둔다 (예: 인터뷰 녹취일, 리포트 기준일 `2026-03-01`).

프런트매터는 **파일 첫 줄부터** 열고, 본문의 H1은 그 **다음**에 둔다. 본문 구분선으로 쓰는 `---`와 겹치지 않도록, 메타데이터 블록은 맨 위 한 번만 사용한다.

```yaml
---
wiki_created: 2026-04-18
wiki_updated: 2026-04-18
content_as_of: 2026-04-01
---

# 문서 제목
```

- 기존 문서에 프런트매터가 없으면, **다음 편집 시** 위 필드를 추가하고 `wiki_created`는 **최초 추정 불가 시** 첫 편집일을 넣되, 본문에 “작성 시점 불명”이면 한 줄 각주로 남긴다.

## 새 문서 추가 절차

1. **메타데이터**: 위 **작성 시점** 규칙에 따라 YAML 프런트매터를 채운다.
2. **파일명**: 소문자·하이픈·영문 위주 (`lifespan.md`, `nmn.md`). 한글 제목은 H1에 둔다.
3. **H1**: 주제 한 가지. 서두 1~2문단으로 범위를 고정한다.
4. **본문**: 논리 순서대로 `##` 절. 절마다 짧은 불릿으로 사실·주장을 분해한다.
5. **용어 통일**: 이미 있는 문서의 표기를 따른다 (예: Sirtuins → **시르투인**). 표기가 갈리면 기존 문서를 기준으로 맞추고, 필요 시 한 줄로 동의어를 병기한다.
6. **외부 URL**: 사용자가 요청하지 않으면 넣지 않는다. 지식 그래프는 **내부 `wiki/*.md` 링크**가 중심이다.
7. **인과·RCA·Impact 주제**(한 변수가 다른 결과를 **메커니즘으로 전달**하는 서술): 아래 **인과관계 구조화** 절을 **반드시** 적용한다 (해당 없으면 생략).

## 인과관계 구조화 — RCA·Impact 분석 근거 (주제별 필수)

**언제:** “A가 B를 야기한다”, “전달 경로”, “왜(근원)·무엇이 바뀌는가(영향)”처럼 **원인–결과**가 핵심인 입력.

**목표:** 에이전트가 **root cause analysis(RCA)**와 **impact analysis**를 할 때, 위키 한 페이지를 **근거 데이터**로 읽을 수 있게 한다.

### 필수 블록 (순서 권장)

1. **서두 1문단**: 문서 목적을 “RCA·Impact 추론용 근거”라고 명시한다.
2. **Entity 정의 표**: 열 `Entity ID | 한글·설명 | 정의(분석용)`. ID는 **영문** `Camel_Case` 또는 `snake_case`로 고정해 파일 간에 재사용한다.
3. **Causality map**: 전달 기제를 구분한 표(예: **직접 / 간접 / 2차**) + 열에 `분석 태그`로 `RCA`, `Impact`, `Evidence`를 병기한다.
4. **그래프 엣지 표(기계 친화)**: 최소 열 `source | edge | target | mechanism_id` (또는 `relation`). 방향은 **인과 방향**만 쓴다.
5. **`##` 절 앵커**: 예) `#root-cause`(근원), `#impact`(영향 체크리스트), `#evidence-data`(검증 지표), `#causality-map`, `#logic-framework`.
6. **논리 프레임워크**: 짧은 **의사코드** 또는 **Mermaid** `flowchart` 중 하나 이상(둘 다 가능).
7. **Evidence 표**: 관측·지표 후보(시차, 산업 집약도 등) — 수치·계수는 **출처 없으면** “국면·시기별 상이”로 일반화한다.
8. **역링크**: 거시·사례 메모가 있으면 해당 `wiki/*.md` 절에 **한 줄** 링크로 연결한다.

### 금지·주의

- 인과를 단정할 수 없는 주제에 **가짜 엣지**를 만들지 않는다.
- LaTeX만으로 인과를 표현하기보다 **표 + 의사코드/Mermaid**를 우선한다.

### 이 저장소 예시

- `wiki/oil-price-inflation-causality.md` — `Oil_Price` → `Price_Level`, 엣지 표, RCA/Impact/Evidence, [거시 메모](macro-energy-inflation-outlook.md)와 양방향 연결.

## Named entity 연결 규칙

### 식별할 타입 (최소)

| 타입 | 예 |
|------|-----|
| Person | 데이비드 싱클레어 |
| Organization | 하버드 의과대학 |
| Work | 《노화의 종말》, Lifespan |
| Compound / Drug | NMN, NAD+, 메트포르민 |
| Pathway / Protein | 시르투인, mTOR |
| Concept / Theory | 정보 이론적 노화 |
| Macro variable / Index | `Oil_Price`, `Price_Level`, CPI |

인과 전용 문서에서는 표의 **개체** 열에 `Entity ID`를 넣고, 일반 위키와 동일하게 **관련 문서** 절로 연결한다.

### 안정 앵커 (필수 패턴)

렌더러마다 헤딩 슬러그가 달라질 수 있으므로, **교차 참조가 필요한 절 앞**에 HTML 앵커를 둔다.

```markdown
<a id="sinclair"></a>

## 2. 하버드대 데이비드 싱클레어 교수
```

- `id` 값은 **영문 소문자·하이픈** (`sinclair`, `sirtuins`, `information-theory-aging`).
- 한 문서에 **동일 주제로 들어오는 링크가 2회 이상**이면 앵커를 우선한다.

### 인라인 링크

- 첫 등장 또는 정의가 중요한 개체에 `[**NMN**](nmn.md)` 형태로 연결한다.
- 다른 문서의 절로 보낼 때: `[시르투인 이론](lifespan.md#sirtuins)`.

### 역링크 (기존 문서 갱신)

새 문서가 기존 주제를 확장하면 **반드시** 관련된 기존 `wiki/*.md`를 연다.

- 해당 절에 한 줄 맥락 링크를 추가하거나,
- 문서 하단에 고정 절을 둔다:
  - `## 관련 문서 (Named entity)` — 짧은 불릿으로 새·옛 문서 연결.

### 선택: 엔티티 인덱스 표

인물·화합물·이론이 많은 페이지 말미에 **표 한 블록**으로 `개체 | 유형 | 연결`을 정리한다. `연결` 칸에는 `nmn.md`, `lifespan.md#sirtuins` 등 구체 URL만 쓴다.

## 작업 체크리스트

```
- [ ] YAML 프런트매터 wiki_created (및 wiki_updated / content_as_of 필요 시)
- [ ] wiki/ 아래 경로·파일명 확정
- [ ] H1/## 구조 및 용어 기존 문서와 정합
- [ ] 교차 참조용 <a id="..."></a> 배치
- [ ] 신규 → 기존 인라인 링크
- [ ] 기존 → 신규 역링크 또는 "관련 문서" 절
- [ ] (선택) Named entity 표
- [ ] 인과 주제 시: Entity 표·Causality map·엣지 표·RCA/Impact/Evidence 절·논리 프레임워크·역링크
```

## 이 저장소의 참고 예시

- 보충제 단일 주제: `wiki/nmn.md` (효능 절 + 하단 관련 문서).
- 저서·이론·여러 화합물: `wiki/lifespan.md` (§2 시르투인 앵커, §4 NMN 링크, 하단 표).
- 인과·RCA·Impact: `wiki/oil-price-inflation-causality.md` (엔티티 ID, 엣지 표, 거시 메모와 연결).

새 주제를 추가할 때 위 두 파일 중 **어느 쪽과 맞닿는지** 먼저 판단하고, 맞닿는 절에 양방향 링크를 만든다. **원인–결과**가 중심이면 보건 문서가 아니라 **인과 전용 패턴**을 따른다.
