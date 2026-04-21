# Wiki: 엔티티 연결만 갱신

이미 존재하는 `wiki/*.md`들 사이에서 **Named entity**를 찾아 **양방향 링크**와 앵커를 정리한다. **`wiki/entity/`·`wiki/rel/`** 가 있으면 그래프 쪽 링크도 함께 맞춘다. 새 주제 글을 쓰지 않을 때 사용한다.

## 전제

1. `.cursor/skills/wiki-knowledge-ingest/SKILL.md`를 읽고 **Named entity 연결 규칙**과, 해당 문서가 **인과·RCA·Impact** 유형이면 스킬의 **인과관계 구조화** 절을 함께 적용한다.
2. 링크는 **출발 파일 기준 상대 경로**만 사용한다.
   - `wiki/` 루트 문서 ↔ `wiki/entity/foo.md`: `entity/foo.md`
   - `wiki/` 루트 문서 ↔ `wiki/rel/bar.md`: `rel/bar.md`
   - **`wiki/entity/` ↔ `wiki/rel/`:** `../rel/...`, `../entity/...`
   - **`wiki/entity-list.md`:** `entity/...`, `rel/...`

## 입력

- 사용자가 지정한 파일 경로(예: `wiki/nmn.md`, `wiki/entity/samsung-electronics.md`, `wiki/rel/roe-to-valuation-multiples-per-pbr.md`), 또는 “`wiki/` 전체 점검” 요청.

## 수행 순서

1. 대상 마크다운을 읽고 Person / Organization / Work / Compound / Pathway / Concept / Index·시장 후보를 뽑는다. **`Entity ID`·엣지 표**가 있으면 `source`/`target`이 다른 문서의 동일 ID와 **일치**하는지 확인한다.
2. 같은 `wiki/` 트리 안에서 **동일 실체**를 언급하는 다른 파일을 찾는다 (`rg` 등으로 고유명사·약어·`Entity ID` 검색). **`wiki/entity/*.md`** 에 정의된 실체가 본문에만 있고 **링크가 없으면** `[**표시명**](entity/<slug>.md)` 형태로 연결한다.
3. **`wiki/rel/*.md`** 를 보면 표·서두의 **`../entity/...`** 링크가 **실제 파일명과 일치**하는지 확인하고, 누락된 엔티티 페이지가 있으면 **생성은 하지 않되**(이 커맨드 범위 밖이면 사용자에게 알림), 링크 깨짐만 고친다.
4. **교차 참조용 앵커**가 없으면 스킬 패턴으로 `<a id="..."></a>`를 추가한다.
5. 첫 등장·정의에 가까운 문장에 인라인 링크를 넣고, 상대 문서에는 **역링크** 또는 `## 관련 문서 (Named entity)` 불릿을 보강한다. **`wiki/entity/<slug>.md`** 에는 **`## 관련 인과관계`** 절에 해당 `../rel/*.md` 가 모두 오도록 정리한다.
6. 표가 이미 있으면 **연결 열**만 최신화한다. 불필요한 중복 링크는 줄인다. **인과 문서**면 Causality map·엣지 표·`RCA`/`Impact`/`Evidence` 절 링크가 끊기지 않았는지 점검한다.
7. **`wiki/entity-list.md`** 가 있으면, 새로 잡힌 엔티티·관계 파일명이 표에 반영돼 있는지 확인하고 **한 줄이라도 추가·삭제가 생기면 표를 갱신**한다.
8. 스킬에 따라 편집한 각 파일의 YAML 프런트매터에 **`wiki_updated`**(및 필요 시 `wiki_created` 보충)를 반영한다.

## 산출물

- 수정된 파일 목록과 각 파일에서 추가·변경한 링크·앵커 요약.
- 엔티티 타입별로 어떤 문서가 연결됐는지 한 단락. 인과 문서가 있으면 **주요 엣지** 1~3개를 한 줄로 요약한다. `entity/`·`rel/` 을 건드렸으면 **`entity-list.md` 반영 여부**를 명시한다.

## 금지

- 근거 없는 새 사실 문장 추가 (링크·앵커·표 구조만 다룬다).
