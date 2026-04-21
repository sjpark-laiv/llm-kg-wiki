# Wiki: 새 지식 저장

사용자가 제공한 **새 정보**를 `wiki/` 아래 마크다운으로 저장하고, 기존 문서와 **Named entity**를 연결한다. 입력이 **영상 요약·인터뷰 요약**처럼 **고유명사·인과**가 풍부하면 **`wiki/entity/` + `wiki/rel/` + `entity-list`** 경로를 함께 쓴다.

## 전제

1. **반드시** 프로젝트 스킬 `.cursor/skills/wiki-knowledge-ingest/SKILL.md` 전체를 읽고 그 절차·규칙을 그대로 따른다.
2. 저장 위치는 항상 **`wiki/`** (상대 링크만 사용).
3. **파일명:** 본문·엔티티·인과 문서 모두 **소문자·하이픈 영문 slug** (`wiki/topic-slug.md`, `wiki/entity/samsung-electronics.md`, `wiki/rel/roe-to-valuation-multiples-per-pbr.md`). **한글 표시명은 H1**에 둔다.

## 입력

- 사용자 메시지에 붙여넣은 본문, 또는 열린 파일/선택 영역의 텍스트를 **원문**으로 취급한다.
- 제목·파일명(`wiki/slug.md`)이 없으면 본문에서 주제를 추론해 **slug**를 제안하고, 한글 제목은 H1에 둔다.

## 수행 순서

1. `wiki/`의 기존 `*.md`와 **`wiki/entity/*.md`**, **`wiki/rel/*.md`**, **`wiki/entity-list.md`**(있으면)를 목록화하고, 내용이 겹치는 주제·엔티티가 있으면 읽는다.
2. 스킬의 **작성 시점(YAML 프런트매터: `wiki_created`, 필요 시 `wiki_updated`·`content_as_of`)**과 **새 문서 추가 절차**로 초안을 작성한다 (`##` 절, 불릿, 본문용 `---` 구분선은 메타 YAML과 혼동 금지, 용어 통일).
3. 입력에 **원인→결과·전달 기제·근원/영향**이 있으면 스킬 **「인과관계 구조화」**를 적용한다: **Entity ID 표**, **Causality map**, **`source|edge|target|mechanism_id` 엣지 표**, **`RCA` / `Impact` / `Evidence` 절**(앵커 포함), **의사코드 또는 Mermaid**, 관련 `wiki/*.md`와 **역링크**. 참고 레이아웃: `wiki/oil-price-inflation-causality.md`.
4. 교차 참조가 필요한 절 앞에 `<a id="..."></a>`를 배치한다 (스킬의 앵커 규칙).
5. 기존 문서의 해당 절에 **인라인 링크** 또는 하단 **`## 관련 문서 (Named entity)`**로 **역링크**를 추가한다.
6. 인물·화합물·이론이 많으면 스킬대로 말미에 **Named entity 표**를 선택 적용한다.

### (선택·권장) Named entity 그래프: `entity/` + `rel/` + `entity-list`

다음에 해당하면 **주제 한 페이지(`wiki/<slug>.md`)만** 두지 말고, **개체·인과를 분해해 저장**한다.

- **적용 신호:** 영상/발화 **요약**, **고유명사** 다수, **A가 B를 야기한다** 식의 **인과**가 여러 쌍으로 반복됨.

**절차:**

1. **엔티티 추출:** Person / Organization / Index·시장 / 개념·지표 등 후보를 정리한다.
2. **`wiki/entity/<entity-slug>.md`:** 각 파일에 YAML 프런트매터, H1(한글명), `## 개요`, **`## 관련 인과관계 (교차 링크)`**에 이 엔티티가 등장하는 **`../rel/<relation-slug>.md`** 링크를 불릿으로 단다. 필요 시 스킬대로 **`Entity ID` 표** 한 블록.
3. **`wiki/rel/<cause-slug>-to-<effect-slug>.md`:** 파일명은 **원인→결과**가 드러나는 **영문 slug** (예: `roe-to-valuation-multiples-per-pbr.md`). 본문에 다음을 **모두** 포함한다.
   - **교차 링크:** 서두 또는 표에서 관련 **`../entity/*.md`** 를 인라인 링크로 나열.
   - **`##` 절(권장 제목):** **원인·근원**, **결과·영향**, **전달 기제**, **인과관계 설명**, **Evidence**(가능하면 **타임코드·수치**를 표로).
   - 스킬 **인과관계 구조화:** **Entity 정의 표**(개체 문서 링크 포함), **Causality map**, **`source|edge|target|mechanism_id` 엣지 표**, **`#root-cause` / `#impact` / `#evidence-data` 등 앵커**, 하단 **역링크**(`../entity/...`, 동일 주제 `wiki/<slug>.md`, 다른 `rel` 문서).
4. **`wiki/entity-list.md`:** `wiki/entity/`에 만든 파일을 **표로 인덱싱**(표시명·유형·`entity/...md` 경로). `wiki/rel/`은 **원인 요약 | 결과 요약 | `rel/...md`** 표로 같이 넣어 **검색 허브**로 유지한다. **신규·수정 시** 이 파일을 **갱신**한다.
5. **주제 문서 `wiki/<slug>.md`와의 연결:** 한 편의 요약·메모를 남길 경우, 본문에서 **`entity/`·`rel/`** 로 링크하거나 하단 `## 관련 문서`에 한 줄씩 연결한다 (**양방향**이 되도록 상대방에도 링크).

**상대 경로 규칙 (필수):**

| 출발 | 링크 예 |
|------|---------|
| `wiki/topic.md` | `[ROE](entity/roe.md)`, `[인과](rel/roe-to-valuation-multiples-per-pbr.md)` |
| `wiki/entity/foo.md` | `[인과](../rel/roe-to-valuation-multiples-per-pbr.md)` |
| `wiki/rel/bar.md` | `[엔티티](../entity/roe.md)` |
| `wiki/entity-list.md` | `[entity/roe.md](entity/roe.md)` |

## 산출물

- 새 파일: `wiki/<slug>.md` (주제 메모·인과 통합본이 필요할 때)
- **엔티티·인과 분해 시 추가:** `wiki/entity/<entity-slug>.md`, `wiki/rel/<relation-slug>.md`, 갱신된 `wiki/entity-list.md`
- 수정 파일: 연결된 기존 `wiki/*.md` (역링크·용어 정합)
- 짧은 요약: 생성·수정 경로 목록, 주요 연결 엔티티 3~7개 (인과 문서면 **엣지·RCA/Impact·Evidence** 요약 포함)

## 금지

- 사용자가 요청하지 않은 **외부 URL** 삽입.
- `wiki/` 밖에 지식 본문을 두는 것 (예외 없음).
