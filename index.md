# 건설안전기사 학습앱 인수인계 v32 (최종)

## ⚠️ 새 채팅 시작 시 필수 업로드
HTML 파일 + 이 MD 파일 반드시 함께 업로드

---

## 현재 작업 파일
- 파일명: `2026년_건설안전기사_v32.html`
- 용량: 약 4.1MB
- v31 → v32 변경사항: 필답형 빈출 패널이 5문제만 표시되던 div 불균형 버그 수정 (아래 "최근 수정 이력" 참고)

---

## 카드 현황 (총 888개, 2026년 1회 필답형 12문 포함 시 약 900개)
- 필답형 466개 + 2026년 1회 12개 (2015~2026)
- 작업형 422개 (2019~2025)
  - 작업형 네비 순서: 2025→2024→2023→2022→2021→2020→2019

---

## 전체 기능 목록

### 기출문제
| 메뉴 | 내용 |
|---|---|
| 필답형 기출문제 | 2015~2025 연도별 탭 |
| 필답형 키워드 | 14개 주제별 필터 (A-1~A-6, B-1~B-6, C-1~C-2) |
| 필답형 빈출 | ★★★/★★/★ 별점 필터 |
| 필답형 공부 | 정답·중간·오답 노트 |
| 작업형 기출문제 | 2019~2025 연도별 탭 |
| 작업형 키워드 | 주제별 필터 |
| 작업형 빈출 | 별점 필터 |
| 작업형 공부 | 정답·중간·오답 노트 |

### Enhancement 모듈
| 기능 | 설명 |
|---|---|
| 모의시험 | 오답·중간 우선 랜덤 20문제, 키워드 자동 매칭, 자가채점(○△✕) |
| 에빙하우스 복습 스케줄러 | 오답 체크 시 1→3→7→14→30일 자동 등록, 오늘 복습 배너 |
| 학습통계 대시보드 | 연속학습일 스트릭, 월별 달력, 채점분포, 복습예정 |
| 오늘복습 | 복습 예정일 도달한 문제만 모의시험 |
| Lazy Loading | IntersectionObserver로 성능 최적화 |

---

## 네비게이션 구조
```
필답형 기출/키워드/빈출/공부  |  작업형 기출/키워드/빈출/공부  |  모의시험 학습통계 오늘복습
```
- `|` : 텍스트 구분선 (`.main-nav-divider`)
- 모의시험·학습통계·오늘복습은 JS로 동적 주입 (`injectToolbarButtons()`)

---

## localStorage 키 목록
| 키 | 내용 |
|---|---|
| `ksaWrong_2021_2025` | 오답 Set |
| `ksaCorrect_2021_2025` | 정답 Set |
| `ksaPartial_2021_2025` | 중간 Set |
| `ksaNote_2021_2025` | 오답노트 Map |
| `ksaEnhReview` | 에빙하우스 복습 스케줄 |
| `ksaEnhLog` | 일별 학습 로그 (스트릭·히트맵) |

---

## 카드 ID / q-code 규칙
- 필답형 `data-id`: `"25_01_01"` / `q-code`: `25/01/01`
- 작업형 `data-id`: `"qw_22_11_01"` / `q-code`: `22/11/01`
- 작업형 `data-type="work"` 필수 / `id="q_qw_22_11_01"` 형식
- 별 색상: `color:#555` (★ 노란색 `#f5a623` 절대 금지)
- 키워드 칩 구분자: `  /  ` (두칸 슬래시 두칸) — `  [두칸]/[두칸]  `

---

## ✅ 새 카드 작성 표준 템플릿

> **Claude에게:** 새 카드 추가 시 반드시 아래 템플릿을 기준으로 작성할 것.
> 기존 파일에 다양한 스타일이 혼재하더라도 **이 템플릿이 기준**임.

---

### A. 필답형 카드 전체 구조

```html
<div class="q-card" data-star="1" id="q_YY_RR_NN" data-id="YY_RR_NN">
<div class="q-header" onclick="toggleCard(this.parentElement)">
<span class="q-code">YY/RR/NN</span><span class="q-star star-1" data-star="1"><span style="color:#555">★</span></span>
<span class="q-title">카드 제목</span>
<button class="correct-btn" onclick="event.stopPropagation(); toggleCorrect('YY_RR_NN')" title="정답">○</button><button class="partial-btn" onclick="event.stopPropagation(); togglePartial('YY_RR_NN')" title="중간답">▲</button><button class="wrong-btn" onclick="event.stopPropagation(); toggleWrong('YY_RR_NN')" title="오답 체크">✗</button>
<span class="q-toggle">▼</span>
</div>
<div class="q-body">
<div class="question-text">문제 텍스트. <strong>강조어</strong>는 strong 태그 사용.</div>
<div class="answer-box"><div class="answer-label">정답</div>
  [정답 아이템 — 아래 B 참고]
  [참조 표가 있는 경우 — 아래 C 참고]
</div>
<div class="keyword-section">
<button class="keyword-toggle" onclick="toggleKeyword(this)"><span class="kt-icon">▼</span> 🔑 핵심 키워드</button>
<div class="keyword-content"><div class="keyword-chips"><span class="chip">칩1</span><span class="chip">칩2</span><span class="chip">칩3</span></div></div>
</div>
<div class="wrong-note-area" id="note_YY_RR_NN" style="display:none;">
<div class="wrong-note-header" onclick="this.parentElement.classList.toggle('open')">📝 오답노트</div>
<textarea class="wrong-note-input" id="textarea_YY_RR_NN" onchange="saveNote('YY_RR_NN', this.value)" placeholder="틀린 이유, 헷갈린 부분, 핵심 암기사항 등을 적어두세요..."></textarea>
<div class="wrong-note-actions">
<button class="note-save-btn" onclick="saveNote('YY_RR_NN', document.getElementById('textarea_YY_RR_NN').value)">💾 저장</button>
<button class="note-clear-btn" onclick="clearNote('YY_RR_NN')">🗑 삭제</button>
</div>
</div>
</div>
</div>
```

**별점 변환표**
| 별 수 | data-star | class | 표시 | 비고 |
|---|---|---|---|---|
| ● | 0 | star-0 | `●` | 암기완료 — 빈출·모의시험 제외, 기출탭 참고용 유지 |
| ★ | 1 | star-1 | `★` | |
| ★★ | 2 | star-2 | `★★` | |
| ★★★ | 3 | star-3 | `★★★` | |

> **● 카드 규칙:**
> - `data-star="0"`, `class="q-star star-0"`, 텍스트 `●`
> - CSS: `.q-star.star-0 { opacity: 0.5 }`
> - 빈출 패널(`showFreq`)에서 `data-star=0` 자동 제외 (basePool 필터)
> - 기출문제 연도별 탭에는 그대로 표시 (참고용)

---

### B. answer-item 정답 강조 3가지 패턴

**패턴 1 — 전체 강조** (정답 전체가 핵심인 경우)
```html
<div class="answer-item"><span class="ans-num">①</span><span><strong>정답 전체 텍스트</strong></span></div>
```

**패턴 2 — 부분 키워드 강조** (문장 중 일부만 핵심인 경우)
```html
<div class="answer-item"><span class="ans-num">①</span><span>앞 문장 <span class="hl">핵심 키워드</span> 뒷 문장</span></div>
```

**패턴 3 — 핵심어 + 접미어 분리** (「~에 관한 사항」처럼 반복 접미어가 있는 경우)
```html
<div class="answer-item"><span class="ans-num">①</span><span><strong class="ans-core">핵심어</strong><span class="ans-suffix">에 관한 사항</span></span></div>
```

> **선택 기준:**
> - 정답이 짧고 전체가 핵심 → 패턴 1
> - 정답이 문장이고 일부 단어만 강조 → 패턴 2
> - 여러 항목의 접미어가 동일하게 반복 → 패턴 3

---

### C. 참조 표 (ref-extra)

비교 정리가 필요한 경우 answer-box 닫기 전에 삽입:

```html
<div class="ref-extra"><div class="divider"></div>
<table class="answer-table">
<tbody><tr><th>헤더1</th><th>헤더2</th></tr>
<tr><td><span class="hl">항목명</span></td><td>내용 설명</td></tr>
<tr><td><span class="hl">항목명</span></td><td>내용 설명</td></tr>
</tbody></table></div>
```

> answer-box 전체 닫기 순서: `[answer-items]` → `[ref-extra]` → `</div>` (answer-box 닫기)

---

### D. 작업형 카드 전체 구조

필답형과 동일하나 아래 3가지 차이:

```html
<div class="q-card" data-star="1" data-id="qw_YY_RR_NN" id="q_qw_YY_RR_NN" data-type="work">
<div class="q-header" onclick="toggleCard(this.parentElement)">
<span class="q-code">YY/RR/NN</span><span class="q-star star-1" data-star="1"><span style="color:#555">★</span></span>
<span class="q-title">카드 제목</span>
<button class="correct-btn" onclick="event.stopPropagation(); toggleCorrect('qw_YY_RR_NN')" title="정답">○</button><button class="partial-btn" onclick="event.stopPropagation(); togglePartial('qw_YY_RR_NN')" title="중간답">▲</button><button class="wrong-btn" onclick="event.stopPropagation(); toggleWrong('qw_YY_RR_NN')" title="오답 체크">✗</button>
<span class="q-toggle">▼</span>
</div>
<div class="q-body">
<div class="question-text">🖥️ 동영상에서는 ... <strong>강조어</strong>...</div>
<div class="answer-box"><div class="answer-label">정답</div>
  [정답 아이템]
</div>
<div class="keyword-section">
<button class="keyword-toggle" onclick="toggleKeyword(this)"><span class="kt-icon">▼</span> 🔑 핵심 키워드</button>
<div class="keyword-content"><div class="keyword-chips"><span class="chip">칩1</span><span class="chip">칩2</span></div></div>
</div>
<div class="wrong-note-area" id="note_qw_YY_RR_NN" style="display:none;">
<div class="wrong-note-header" onclick="this.parentElement.classList.toggle('open')">📝 오답노트</div>
<textarea class="wrong-note-input" id="textarea_qw_YY_RR_NN" onchange="saveNote('qw_YY_RR_NN', this.value)" placeholder="틀린 이유, 헷갈린 부분, 핵심 암기사항 등을 적어두세요..."></textarea>
<div class="wrong-note-actions">
<button class="note-save-btn" onclick="saveNote('qw_YY_RR_NN', document.getElementById('textarea_qw_YY_RR_NN').value)">💾 저장</button>
<button class="note-clear-btn" onclick="clearNote('qw_YY_RR_NN')">🗑 삭제</button>
</div>
</div>
</div>
</div>
```

> **필답형과의 차이:**
> 1. `data-id="qw_YY_RR_NN"` / `id="q_qw_YY_RR_NN"` (qw_ 접두사)
> 2. `data-type="work"` 속성 추가
> 3. 문제 텍스트 앞에 `🖥️ 동영상에서는 ...` 형식
> 4. toggleCorrect/Partial/Wrong / saveNote / clearNote 등 함수 인자도 모두 `'qw_YY_RR_NN'`

---

### E. 작업형 연도/회차 구조

```html
<div id="yw20YY" class="year-section" style="display:none;">
  <div id="ywSections20YY">
    <div class="round-section">
      <div class="round-label">20YY년 제N회</div>
      <div class="round-cards-wrap">
        [q-card들]
      </div>
    </div>
  </div>
</div>
```

---

## ⛔ 수정 금지 항목

### 1. applyCardState 함수 (JS)
- **절대 수정 금지**
- 오답노트 아코디언 open 클래스 복원 로직 포함
- 오답 해제 시 na hide/open 제거 로직 포함
- noteMap 키 정규화(`fid`) 포함
- 해당 함수 변경 시 오답노트 아코디언 접기 오류 재발

### 2. handleSearch 함수 (JS)
- **검색 범위 수정 금지**
- `#yearSections .q-card[data-id], #workYearSections .q-card[data-id]` 셀렉터 유지
- 변경 시 키워드·빈출·공부 패널 사용 후 검색 시 중복 출력 재발

### 3. y2015 year-section div 구조 (HTML)
- **2015년 제2회 마지막 카드(15_02_14) 이후 `</div>` 개수 수정 금지**
- 변경 시 2015년 제4회가 y2015 밖으로 탈출하여 다른 연도에 표시됨

### 4. 21_04_03 카드 answer-box (HTML)
- **닫는 `</div>` 수정 금지** (현재 정상)
- 과거 누락으로 y2020~y2015 전체가 y2021 안에 중첩되는 버그 있었음

### 5. 21/02/01, 21/04/04 안전보건표지 이미지 (HTML)
- **base64 이미지 수정 금지**
- 실제 법령 기준 안전보건표지 사진
- 임의로 SVG/이모지로 교체 절대 금지

### 6. 23/02/12 정답 형식 (HTML)
- `ans-core` + `ans-suffix` 구조로 20/04/11과 동일하게 정렬됨

### 7. panelKeyword / panelFreq margin-top (CSS)
- **`#panelFreq { margin-top: 0; }` / `#panelKeyword { margin-top: 0; }` 수정 금지**
- 다시 음수로 변경 시 메뉴와 기출문제 겹침 버그 재발

### 8. subKeyword flex-wrap (CSS/HTML)
- **`#subKeyword` 인라인 스타일에 `flex-wrap:nowrap` 유지**
- 제거 시 키워드 칩이 2줄로 wrap되어 패널과 겹침 재발

---

## 주요 기술 패턴 (Python 작업 시)

- **div 균형 검증 시 주석 제거 금지**
  ```python
  # 주석을 제거하면 위치 기반 조회 오류 발생
  # 길이 보존 치환 사용:
  re.sub(r'<!--.*?-->', lambda m: ' '*len(m.group()), content, flags=re.DOTALL)
  ```

- **yearStore 구조**
  ```
  <div id="yearStore"><div id="yearSections">   (+2 오픈)
  ...
  </div></div><!-- /yearStore -->               (-2 클로즈)
  ```
  각 year-section은 내부에서 독립적으로 div 균형을 맞춰야 함.

- **카드 재정렬 시** div 깊이 추적으로 완전한 카드 블록을 추출해야 함 (ID만 변경 금지)

- **동일 문자열이 여러 카드에 존재할 때** 위치(pos) 기반으로 카드 범위를 한정하여 1회만 교체

---

## 최근 수정 이력

### v32 (2026-07-22) — 필답형 빈출 패널 5문제만 표시되던 버그 수정
- **증상**: 필답형 빈출(★★★) 클릭 시 5문제만 표시 (정상은 100문제 이상이어야 함). 작업형 빈출은 정상(267문제).
- **원인**: `26_01_12` 카드(2026년 1회 마지막 문제) 뒤에 `</div>`가 1개 초과로 들어가 있어, `#yearSections`가 2026년 섹션 직후 조기 종료됨.
  - 그 결과 2025~2015년 전체 연도 섹션이 `#yearSections` 밖으로 밀려나 형제(sibling) 요소가 됨.
  - `showFreq()`가 `#yearSections .q-card[data-id]`로 카드를 수집하는데, DOM상 진짜 자식이 아니게 되어 2026년 12문제만 인식됨.
- **진단 방법**: 브라우저는 잘못된 div 중첩도 관대하게 파싱하므로, 겉보기엔 화면이 멀쩡해 보여도 `document.querySelectorAll('#yearSections .q-card')` 개수를 콘솔에서 확인하면 바로 드러남. (Python regex만으로는 div 중첩 오류를 못 잡으므로, BeautifulSoup 등으로 실제 DOM 트리 파싱해서 확인 필요)
- **수정**: 26_01_12 카드의 wrong-note-area 닫는 구간 뒤 `</div>` 7개 → 6개로 정정 (yearSections를 조기 종료시키던 1개 삭제)
- **교훈**: 새 연도/회차 카드를 맨 앞(최신 연도)에 추가할 때는 해당 연도 섹션의 닫는 div 개수를 반드시 재검증할 것. 특히 해당 섹션이 `yearSections`의 첫 번째 자식일 경우, 여기서 발생한 불균형이 전체 연도 목록을 통째로 날려버릴 수 있음.

---

## 알려진 버그 (미해결)
- 작업형 연도 버튼 오류: `yw2023~yw2015`가 `workYearSectionsInner` 밖에 위치

---

## 미수록 기출
- 작업형 2023년 1·2·4회
- 작업형 2017년 4회 2·3부
- 작업형 2015년 3회

---

## 수험생 정보
- 시험일: **2026년 4월 18일**
- 도전 횟수: 3번째
- 강점: 숫자·그림·공간 감각 (인테리어 20년)
- 약점: 긴 문장 암기
- 전략: 키워드 두음법 + 모의시험 인출 반복 + ★★★ 빈출 집중
