# Topics 노트 작성 규칙

이 문서는 `topics/` 아래 설명, 참조, 개념 노트의 공통 작성 기준임.

`topics`는 특정 책을 따라가는 노트가 아니라, 기술 주제 자체를 설명하고 연결하는 지식 노트 모음임. 하위 분류별 세부 방식은 다를 수 있지만, frontmatter, 제목 위계, 인용 보존, 이미지, 상호 참조, 강조 규칙은 이 문서를 기본값으로 삼음.

---

## 1. 디렉터리/파일 규칙

기본 경로:

```text
topics/{domain}/{type}/{subject}/index.md
topics/{domain}/{type}/{series}/{NN-section-slug}/index.md
```

- `{domain}`은 `java`, `git`, `unity`, `valkey` 같은 기술 영역
- `{type}`은 `explanation`, `reference`, `concept` 같은 노트 성격
- `{subject}`는 주제 slug
- 시리즈형 explanation은 `{series}/{NN-section-slug}/index.md` 구조를 사용할 수 있음
- 슬러그는 kebab-case 사용
- 번호가 필요한 시리즈는 두 자리 zero-pad를 사용함
- 이미지: 같은 노트 폴더의 `images/{slug}-{NNN}.png`
  - `NNN`은 `001`부터 순번

## 2. Frontmatter

기본 형식:

```yaml
---
tags:
  - {type}
  - {domain}
  - {topic}
aliases:
  - {search alias}
references:
  - {url or source}
---
```

- 첫 번째 tag는 노트 성격을 나타냄
  - `explanation`
  - `reference`
  - `concept`
- 두 번째 tag는 기술 영역을 나타냄
- 세 번째 이후 tag는 구체 주제나 라이브러리를 나타냄
- `aliases`는 검색에 도움이 되는 제목, 약어, 한국어/영문 변형을 둠
- `references`는 공식 문서, 소스 코드, API 문서, 글의 근거 자료를 둠
- 외부 자료나 공식 문서를 설명 근거로 사용했다면 frontmatter의 `references`에 남김

## 3. 제목 위계

- H1: 파일당 1회
  - 글의 실제 주제를 가장 직접적으로 드러냄
  - 시리즈 글이면 번호나 편 제목을 포함할 수 있음
- H2: 글의 주요 설명 단계 또는 주제 그룹
- H3: H2 안의 세부 개념, 비교 항목, 구현 단계
- H4: 복잡한 case 분기가 꼭 필요할 때만 사용
- H5 이하는 사용하지 않음

설명 글에서는 H1/H2/H3 위계를 독자가 글의 흐름을 따라갈 수 있는 목차로 사용함.

## 4. 공통 본문 스타일

- 한국어 설명은 명사형 종결을 기본으로 함
- 문장 끝에 마침표 `.`를 붙이지 않음
- 가장 상위 진술은 `-` 없이 평문으로 작성함
- 하위 개념, 조건, 예시, 반례만 bullet로 들여쓰기함
- 공식 문서, 원문, 소스 코드에서 가져온 문장은 quote 블록으로 분리함
- 외부 지식이나 개인 판단은 근거와 함께 분리해서 작성함
- 코드는 실제 언어명을 붙인 fenced code block으로 작성함
- 명령어 예시는 `zsh`와 `$ ` 프롬프트를 사용함

## 5. Callout 종류

- `> [!quote]` - 원문 인용
- `> [!summary] {제목}` - 핵심 요약
- `> [!note] {제목}` - 보충 설명
- `> [!tip] {제목}` - 실용적 조언
- `> [!warning] {제목}` - 주의 사항
- `> [!example] {제목}` - 예시
- `> [!checklist] {제목}` - 점검 항목
- `> [!todo] {메모}` - 작성 중 임시 표시

Callout 이름은 소문자로 작성함. 제목은 한국어 또는 영문으로 작성할 수 있고, 본문은 한 줄 띄우고 시작함.

## 6. 인용 규칙

- quote 블록은 원문을 보존함
- quote 블록 내부에 bold, italic, highlight를 추가하지 않음
- 원문이 길면 핵심 문장만 짧게 인용함
- 한 quote 안에는 가능한 한 하나의 개념만 담음
- 주제가 바뀌면 새 quote로 분리함
- 인용은 설명의 출발점으로 사용하고, 바로 뒤에 한국어 설명을 붙임

## 7. 강조 규칙

네 가지 인라인 마크업이 각자 한 가지 역할만 맡음.

- `**bold**` - 새 핵심 개념의 첫 도입
- `*italic*` - 도입된 핵심 개념의 재언급
- `==highlight==` - 문장 안의 핵심 판단 포인트
- `` `backtick` `` - 코드 식별자, 명령어, 파일명, 옵션, 디렉터리

완화된 강조 정책을 적용함.

- 모든 일반 단어에 italic을 강제하지 않음
- 글 전체를 관통하는 핵심 개념어에만 첫 도입/재언급 규칙을 적용함
- 번역어가 흔들릴 수 있는 용어는 첫 도입 시 원어를 병기함
- `==highlight==`는 자동 적용하지 않고, 작성자가 독자가 놓치면 안 되는 지점에 직접 표시함

판단 기준:

- "이 글에서 중심이 되는 개념인가?" -> `**bold**`
- "이미 도입한 중심 개념을 다시 가리키는가?" -> `*italic*`
- "이 문장에서 독자가 놓치면 안 되는 판단 지점인가?" -> `==highlight==`
- "코드 또는 코드에 준하는 식별자인가?" -> `` `backtick` ``

## 8. 이미지 임베드

- Obsidian wiki-link 사용: `![[filename.png]]`
- 표준 마크다운 이미지 문법 `![alt](path)`는 사용하지 않음
- 이미지는 같은 노트 폴더의 `images/` 아래에 둠
- 파일명은 `images/{slug}-{NNN}.png` 형식을 사용함

## 9. 상호 참조와 Anchor

Anchor 이름은 kebab-case 의미명을 사용함.

- 정의: 줄 끝 또는 다음 줄에 `^kebab-anchor-name`
- 동일 노트 내 참조: `[[#^anchor-name]]` 또는 `[[#^anchor-name|표시 텍스트]]`
- 헤딩 참조: `[[#Heading Name]]`
- 타 노트 참조: `[[topics/{domain}/{type}/{path}/index#Section|표시 텍스트]]`

Wiki-link 표기:

- pipe 양쪽 공백 없음: `[[ref|display]]`
- 자동 생성 hash ID anchor 사용 금지
- cross-link 뒤에 "참고" 같은 보조어 첨가 금지
- 이미지도 wiki-link 방식으로 임베드함

## 10. Explanation 노트

`explanation`은 "무엇을 눌러야 하는가"보다 "왜 그렇게 동작하는가"와 "어떤 흐름으로 이어지는가"를 설명하는 글임.

### 목적

- 내부 동작, 실행 순서, 설계 의도, 개념 간 관계를 설명함
- 독자가 나중에 구현이나 디버깅을 할 때 판단 근거로 다시 사용할 수 있게 작성함
- 단순 절차는 `how-to-guide`로 보내고, 개념 원리와 흐름은 `topics/{domain}/explanation`에 둠

### Frontmatter

- 첫 번째 tag는 `explanation`
- 두 번째 tag는 기술 영역
- 세 번째 이후 tag는 라이브러리, 프레임워크, 세부 주제
- 공식 문서, 소스 코드, API 문서, 관련 글은 `references`에 기록함
- 시리즈 글은 alias에 편 번호와 짧은 제목을 함께 둘 수 있음

예시:

```yaml
---
tags:
  - explanation
  - unity
  - vcontainer
aliases:
  - VContainer Root LifetimeScope (1) Project root LifetimeScope 등록
  - Project root LifetimeScope 등록
references:
  - https://example.com/official-doc
---
```

### 제목과 흐름

- H1은 글의 주제를 직접적으로 표현함
- H2는 설명 흐름의 주요 단계로 사용함
- H3는 H2 안의 세부 개념이나 비교에 사용함
- H4 이하는 가능하면 피함
- 시리즈 글은 이전/다음 글과의 연결을 wiki-link로 남김

### 문장 스타일

- 한국어 설명은 명사형 종결로 통일함
- 문장 끝 마침표는 붙이지 않음
- 질문형 문장은 실제로 독자의 문제 제기를 드러낼 때만 사용함
- 강조를 위해 느낌표를 사용하지 않음
- 가장 중요한 흐름은 평문으로 두고, 조건과 예외만 bullet로 내림

### Quote와 근거

- 공식 문서나 소스 설명은 `> [!quote]`로 분리함
- quote 블록의 원문은 보존함
- quote 블록 안에는 bold, italic, highlight를 추가하지 않음
- quote 뒤에는 반드시 글의 맥락에 맞는 한국어 설명을 붙임
- 소스 코드 line, 공식 문서 URL, API 문서는 가능한 한 `references`에도 남김

### 코드와 식별자

- 코드 블록은 실제 언어명을 사용함
- C# 예시는 ```` ```csharp ```` 사용
- Java 예시는 ```` ```java ```` 사용
- 쉘 명령은 ```` ```zsh ```` 사용
- 코드 식별자, 클래스명, 메서드명, 파일명은 backtick 사용

### 이미지

- 이미지는 같은 노트 폴더의 `images/` 아래에 둠
- 임베드는 `![[filename.png]]`만 사용함
- 시리즈 글은 파일명에 글 번호나 주제 slug를 포함해 충돌을 피함

## 11. Reference 노트

`reference`는 명령어, API, 설정, 문법을 빠르게 확인하기 위한 노트임.

- 정의, 문법, 옵션, 반환값, 예외, 예시를 빠르게 찾을 수 있게 작성함
- 설명보다 정확한 항목화와 검색 가능성을 우선함
- 코드 식별자와 명령어는 backtick으로 표시함
- 공식 문서 링크는 `references`에 남김
- 질문 후보나 미확인 항목은 별도 `questions.md`에 둘 수 있음

권장 흐름:

1. H1에 대상 명령어/API 이름
2. 짧은 정의
3. Syntax 또는 signature
4. 주요 옵션/파라미터
5. 예시
6. 주의 사항 또는 관련 링크

## 12. Concept 노트

`concept`는 용어, 모델, 프로토콜 구성 요소처럼 독립적으로 재사용되는 개념을 설명하는 노트임.

- 한 노트는 가능한 한 하나의 개념에 집중함
- 정의를 먼저 두고, 동작/예시/연결 개념을 뒤에 둠
- 너무 긴 절차 설명은 explanation이나 how-to-guide로 분리함
- 관련 concept와 explanation은 wiki-link로 연결함
- 용어가 흔들릴 수 있으면 첫 도입 시 원어를 병기함

## 13. 새 노트 작성 체크리스트

- [ ] 경로가 `topics/{domain}/{type}/.../index.md` 구조를 따름
- [ ] Frontmatter의 첫 tag가 노트 성격과 일치함
- [ ] `references`에 근거 자료를 남김
- [ ] H1/H2/H3 위계가 글의 흐름을 드러냄
- [ ] 한국어 설명은 명사형 종결
- [ ] 문장 끝 마침표를 붙이지 않음
- [ ] quote 블록 원문을 보존함
- [ ] callout 이름은 소문자
- [ ] 이미지는 `images/` 폴더와 `![[...]]` 임베드 사용
- [ ] 핵심 개념은 첫 도입 bold, 재언급 italic
- [ ] 코드 식별자는 backtick 사용
- [ ] 판단 포인트는 필요할 때만 highlight 사용
- [ ] Anchor는 kebab-case 의미명 사용
- [ ] Wiki-link pipe 공백 없음
