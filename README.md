# Workspace Apps README

이 문서는 `Workspace Apps` 단일 HTML 파일의 구조와 역할, 데이터 설계, 수정 포인트를 설명합니다.

기준 파일: 사용자 제공 HTML

## 1. 개요

`Workspace Apps`는 여러 개의 소형 앱을 한 화면에서 소개하고, 각 앱에 대한 설명과 외부 링크를 모달로 보여주는 단일 페이지 HTML입니다.

페이지는 크게 3단 구조로 구성됩니다.

1. Header / Hero  
   브랜드와 상단 링크, 소개 문구를 보여줍니다.

2. Apps Section  
   앱 카드 목록을 그리드 형태로 보여줍니다.

3. Footer  
   추가 앱 확장 가능성과 외부 이동 링크를 보여줍니다.

또한 각 앱 카드는 “열기” 버튼과 “설명 보기” 버튼을 가지며, 설명 보기를 누르면 모달이 열려 상세 설명과 연결 링크를 표시합니다.

---

## 2. 파일 특성

- 단일 HTML 파일 구조
- 별도 빌드 도구 없음
- CSS / JavaScript가 HTML 내부에 모두 포함된 형태
- 외부 CDN 사용
  - Pretendard 폰트
  - Remix Icon 아이콘

즉, 정적 페이지로 바로 배포 가능한 구조입니다.

---

## 3. 레이아웃 구조

### 3.1 전체 래퍼

- `.page`
  - 전체 화면 높이와 바깥 여백 담당
- `.container`
  - 최대 너비 제한 (`--max-w`)

이 구조를 통해 화면이 너무 넓어지지 않도록 제어합니다.

### 3.2 Header / Hero

`header.header-section`

포함 요소:
- `.top-nav`
- `.brand`
- `.nav-links`
- `.hero-content`

역할:
- 브랜드명 `Workspace Apps` 노출
- GitHub / Blog 링크 제공
- 서비스 성격을 설명하는 히어로 문구 제공

특징:
- glassmorphism 스타일
- gradient 텍스트
- 큰 radius와 blur를 활용한 상단 강조

### 3.3 Apps Section

`main.apps-section`

포함 요소:
- `.section-head`
- `#product-grid.grid`

역할:
- 앱 목록을 하나의 그룹으로 묶음
- 앱 카드들을 반응형 grid로 배치

현재 타이틀은 `Apps`만 표시되도록 단순화되어 있습니다.

### 3.4 Footer

`footer.footer-section`

역할:
- “More apps coming” 메시지 표시
- GitHub 방문 버튼 표시

상단/중단과 동일한 UI 계열을 유지하면서 페이지 마무리 역할을 합니다.

---

## 4. 디자인 시스템

CSS 변수는 `:root`에 정의되어 있습니다.

주요 토큰:
- `--primary`
- `--primary-light`
- `--accent`
- `--bg`
- `--glass`
- `--glass-strong`
- `--text-main`
- `--text-mute`
- `--text-dim`
- `--border`
- `--shadow-sm`, `--shadow-md`, `--shadow-lg`
- `--card-radius`
- `--max-w`

이 값들을 수정하면 전체 톤앤매너를 한 번에 변경할 수 있습니다.

### 디자인 특징
- 밝은 배경 + 은은한 radial gradient
- 반투명 glass 박스
- 카드 중심 레이아웃
- 둥근 radius
- hover 시 카드 상승 효과
- primary / ghost 버튼 체계

---

## 5. 앱 카드 구조

각 앱은 `PRODUCTS` 배열 내부 객체 하나로 관리됩니다.

예시 구조:

```js
{
  id: "canvas",
  name: "Canvas Workspace",
  icon: "ri-layout-grid-fill",
  short: "시각적 브레인스토밍 보드",
  summary: "아이디어를 공간 위에 자유롭게 배치하고 연결하는 보드형 앱입니다.",
  tags: ["시각화", "브레인스토밍"],
  useCases: ["아이디어를 자유롭게 배치하고 싶을 때"],
  links: {
    open: "#",
    blog: "https://your-blog-link.com"
  }
}
```

### 각 필드 설명

- `id`
  - 앱 고유 식별자
  - 모달 열기 시 대상 찾기에 사용

- `name`
  - 앱 이름

- `icon`
  - Remix Icon 클래스명

- `short`
  - 카드 상단 짧은 설명

- `summary`
  - 카드 본문 및 모달 설명에 사용되는 핵심 소개 문장

- `tags`
  - 카드 하단 태그 목록

- `useCases`
  - 카드에는 일부만 노출되고, 모달에서는 전체 표시

- `links`
  - 외부 이동 링크 정보
  - `open`, `github`, `blog`, `docs` 등의 타입 사용 가능

---

## 6. JavaScript 동작 방식

### 6.1 핵심 상수

#### `PRODUCTS`
앱 데이터 원본입니다.

#### `LINK_META`
링크 타입별 버튼 표현 규칙입니다.

예:
- `open` → 바로 열기
- `github` → GitHub
- `blog` → 기술 블로그
- `docs` → 문서

각 링크 타입은 다음 정보를 가집니다.
- 버튼 라벨
- 아이콘
- 버튼 클래스

### 6.2 주요 DOM 참조

- `gridEl`
- `modalEl`
- `modalContentEl`

각각:
- 카드가 렌더링될 영역
- 모달 배경
- 모달 내부 콘텐츠 영역

### 6.3 함수 설명

#### `escapeHtml(v)`
문자열을 HTML 안전 문자열로 변환합니다.

목적:
- 문자열이 HTML로 직접 삽입될 때 XSS 위험 감소

#### `renderCards()`
`PRODUCTS` 배열을 순회하여 카드 HTML을 생성하고 `#product-grid`에 삽입합니다.

카드에 포함되는 요소:
- 아이콘
- 이름
- 짧은 설명
- summary
- useCases 일부
- tags
- 열기 버튼
- 설명 보기 버튼

#### `openDetail(id)`
전달된 `id`에 해당하는 앱 데이터를 찾아 모달 콘텐츠를 생성합니다.

모달에 포함되는 요소:
- 앱 아이콘
- 앱 이름
- Description
- Use Cases
- 링크 버튼 목록

링크는 `p.links`를 순회하면서 실제 값이 존재하는 경우에만 생성됩니다.

즉:
- 링크가 있으면 버튼 표시
- 링크가 없으면 해당 버튼 생성 안 함

#### `closeDetail()`
모달을 닫고 body의 `modal-open` 클래스를 제거합니다.

---

## 7. 모달 구조

모달은 다음 구조를 가집니다.

- `#detail-modal.modal-backdrop`
- `.modal-card`
- `.close-btn`
- `#modal-content`

동작 방식:
- “설명 보기” 버튼 클릭 시 열림
- 배경 클릭 시 닫힘
- `Escape` 키 입력 시 닫힘

이 구조는 가볍고 직관적이며, 별도 라이브러리 없이 동작합니다.

---

## 8. 반응형 대응

`@media (max-width: 768px)` 기준으로 다음을 조정합니다.

- header 내부 정렬을 세로로 변경
- footer를 세로 정렬로 변경
- hero 제목 크기 축소
- grid를 1열로 변경
- modal padding 축소

따라서 모바일에서도 사용 가능하지만, 카드 길이와 버튼 수가 많아질 경우 추가 조정이 필요할 수 있습니다.

---

## 9. 현재 구조의 장점

1. 단일 파일이라 배포가 간단함
2. 데이터 구조가 명확하여 앱 추가가 쉬움
3. 모달 방식으로 상세 정보 노출이 가능함
4. 링크 유무에 따라 버튼을 선택적으로 표시할 수 있음
5. 디자인 토큰이 CSS 변수로 정리되어 있어 테마 수정이 쉬움

---

## 10. 현재 구조의 한계

1. HTML / CSS / JS가 한 파일에 모두 들어 있어 규모가 커지면 유지보수가 어려움
2. `PRODUCTS` 데이터가 하드코딩되어 있어 외부 데이터 연동이 없음
3. 접근성 속성이 충분히 보강되어 있지 않음
4. 모달 내부 텍스트 구조가 아직 단순함
5. section head와 카드 그룹의 관계를 더 정교하게 다듬을 여지가 있음

---

## 11. 유지보수 가이드

### 11.1 앱 추가 방법

`PRODUCTS` 배열에 객체를 추가합니다.

필수 권장 필드:
- `id`
- `name`
- `icon`
- `short`
- `summary`
- `tags`
- `useCases`
- `links`

예시:

```js
{
  id: "textlist",
  name: "Textlist",
  icon: "ri-file-list-3-line",
  short: "텍스트 흐름 관리 도구",
  summary: "짧은 텍스트를 순서대로 정리하고 관리하는 앱입니다.",
  tags: ["텍스트", "리스트"],
  useCases: ["짧은 문장을 모아 관리할 때"],
  links: {
    open: "/textlist.html",
    blog: "",
    github: ""
  }
}
```

### 11.2 링크 타입 추가 방법

`LINK_META`에 새 타입을 추가합니다.

예시:

```js
portfolio: {
  label: "포트폴리오",
  icon: "ri-briefcase-line",
  className: "ghost"
}
```

그 다음 `PRODUCTS[].links.portfolio`에 URL을 넣으면 자동으로 버튼이 생성됩니다.

### 11.3 스타일 수정 우선순위

디자인 변경 시 아래 순서로 보는 것을 권장합니다.

1. `:root` 변수
2. 섹션 박스 (`.header-section`, `.apps-section`, `.footer-section`)
3. 카드 (`.app-card`)
4. 버튼 (`.btn`)
5. 모달 (`.modal-card`)

---

## 12. 추천 개선 사항

### 개선안 1. 섹션 헤더 보강
현재 `Apps` 타이틀만 존재하므로, 아래 요소 중 일부를 추가하면 더 안정적입니다.

- 섹션 아이콘
- 짧은 설명 문구
- 카드 개수 표시
- 필터 / 정렬 UI

### 개선안 2. 접근성 개선
아래 속성 보강 권장:
- 모달에 `aria-hidden`, `role="dialog"`, `aria-modal="true"`
- 닫기 버튼에 명확한 `aria-label`
- 설명 보기 버튼에 대상 앱 이름 포함

### 개선안 3. 컴포넌트 분리
규모가 커질 경우:
- HTML
- CSS
- JS
- data

를 분리하는 것이 유지보수에 유리합니다.

### 개선안 4. 링크 유효성 관리
현재 `"#"`도 링크로 인식되므로, 실제 배포 시에는 아래와 같은 정책이 필요합니다.

- `"#"`는 미연결 상태로 간주
- 빈 문자열과 함께 렌더링 제외
- 임시 링크 표시 배지 추가

---

## 13. 실행 방법

이 파일은 정적 HTML이므로 브라우저에서 바로 열 수 있습니다.

방법:
1. 파일을 저장
2. 브라우저로 열기
3. 또는 정적 호스팅에 업로드

예:
- GitHub Pages
- Cloudflare Pages
- Netlify
- Vercel

---

## 14. 요약

이 HTML은 “작은 앱들을 한 화면에서 소개하는 앱 디렉토리 페이지”입니다.

핵심 구성은 다음과 같습니다.

- 상단: 브랜드 + 소개
- 중단: 앱 카드 그리드
- 하단: 확장 안내
- 보조 인터랙션: 설명 모달

앱 데이터는 `PRODUCTS` 배열로 관리되며, 링크는 존재하는 값만 버튼으로 렌더링됩니다.  
단일 파일 기반이기 때문에 빠르게 수정하고 배포하기에 적합한 구조입니다.
