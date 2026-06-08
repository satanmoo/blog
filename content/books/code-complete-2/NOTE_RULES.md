# Code Complete 2 노트 작성 규칙

이 디렉터리는 *Code Complete 2* 한국어판을 읽으며 정리한 한국어 노트 모음임.
Pro Git 노트 규칙을 기반으로 하되, 이 책의 성격에 맞게 **중요 인용**, **개념 설명**, **판단 기준**, **코드 예시** 중심으로 작성함.

기준 스타일은 `01-welcome-to-software-construction/01-01-what-is-software-construction/index.md`와 `01-welcome-to-software-construction/01-02-why-is-software-construction-important/index.md`임.
읽으면서 반복되는 패턴이 생기면 이 파일을 계속 조정함.

---

## 1. 디렉터리/파일 규칙

- 경로: `{NN-chapter-slug}/{NN-NN-section-slug}/index.md`
- 챕터 번호는 책의 장 번호와 일치시킴
- 섹션 번호가 책에 명확히 드러나는 경우 책의 순서를 따름
- 섹션 번호가 애매한 경우 노트 작성 순서 기준으로 두 자리 zero-pad
- 슬러그는 두 자리 zero-pad + kebab-case 사용
- 이미지: 동일 섹션 폴더 내 `images/{section-slug}-{NNN}.png` 사용
- 책의 서문, 들어가기, 감사의 글처럼 장 번호가 없는 부분은 `00-preface` 아래에 둠

예시:

```text
00-preface/
  00-01-preface/index.md
01-welcome-to-software-construction/
  01-01-what-is-software-construction/index.md
```

## 2. Frontmatter

```yaml
---
tags:
  - books
  - code-complete-2
aliases:
  - {N} {Chapter Title}
references:
  - Code Complete 2, Chapter N
---
```

- `tags`는 `books`, `code-complete-2` 고정
- `aliases`, `references`는 선택
- 한국어판을 기준으로 읽으므로 본문 제목은 한국어판 제목을 우선 사용
- alias에는 영문 제목과 한국어판 제목을 함께 둠
- 한국어판 장 제목이 노트 검색에 유용하면 alias를 추가함

예시:

```yaml
---
tags:
  - books
  - code-complete-2
aliases:
  - 1 Welcome to Software Construction
  - 1 소프트웨어 구현으로의 초대
references:
  - Code Complete 2, Chapter 1
---
```

## 3. 제목 위계

- H1: `# {N} {한국어판 Chapter Title}` 또는 `# {N.M} {한국어판 Section Title}`
- H2: 책의 주요 절 제목. 절 단위 노트에서는 H1의 제목을 한 번 더 사용
- H3: 세부 개념, 원칙, 비교 항목
- H4: 한 개념 안에서 case 분기나 비교가 필요할 때만 사용
- H5 이하는 사용하지 않음

장 전체를 하나의 노트로 정리하는 경우:

```md
# 1 소프트웨어 구현으로의 초대

## 소프트웨어 구현이란 무엇인가?

### Construction as the Central Activity
```

절 단위로 분리하는 경우:

```md
# 1.1 소프트웨어 구현이란 무엇인가?

## 소프트웨어 구현이란 무엇인가?

### Construction as the Central Activity
```

## 4. 본문 작성 흐름

한 개념의 기본 패턴:

1. `> [!quote]` - 인용할 만큼 중요한 책 문장
2. 한국어 설명
3. 필요한 경우 코드 예시, 비교, 판단 기준 추가

본문 설명은 명사형 종결을 기본으로 함.

- 가장 상위 진술은 `-` 없이 평문으로 작성
- 하위 개념, 조건, 예시, 반례만 bullet로 들여쓰기
- 개인 의견이나 외부 지식은 최소화
- 책의 주장을 내 언어로 재구성하되, 책의 논지와 반대로 확장하지 않음
- 목차용 절, 읽는 방법처럼 독립 노트로 남길 내용이 거의 없는 절은 생략 가능

예시:

```md
### Managing Complexity

> [!quote]
>
> 책의 핵심 문장을 짧게 인용함.

**복잡성 관리(complexity management)** 는 구현 활동의 중심 문제임.

좋은 구현은 단순히 코드를 많이 작성하는 것이 아니라, 사람이 이해하고 유지할 수 있는 형태로 복잡성을 제한하는 일임.

- 복잡성을 줄이는 방법
  - 명확한 이름 사용
  - 작은 routine 작성
  - interface 뒤로 세부 구현 숨김
  - 변경 가능성이 높은 부분을 분리함

> [!principle] 핵심 원칙
>
> 구현 품질은 코드가 실행되는지만이 아니라, 사람이 코드를 이해하고 변경할 수 있는지로 판단해야 함.
```

## 5. Callout 종류

- `> [!quote]` - 책 인용
- `> [!principle] {제목}` - 설계/구현 원칙
- `> [!checklist] {제목}` - 실제 점검 항목
- `> [!warning] {제목}` - 흔한 실수나 오해
- `> [!example] {제목}` - 코드나 상황 예시
- `> [!note] {제목}` - 보충 설명
- `> [!tip] {제목}` - 실무 적용 조언

제목은 한국어 또는 영문으로 작성함. 본문은 한 줄 띄우고 시작함.

## 6. 인용 규칙

- 한국어판을 읽더라도 `> [!quote]`에는 책의 핵심 문장만 짧게 인용함
- 긴 문단 전체를 옮기지 않음
- 인용은 설명의 출발점으로만 사용하고, 바로 뒤에 한국어 설명을 붙임
- 한 개념에 반드시 quote를 붙일 필요는 없음
- 장의 핵심 주장, 정의, 중요한 비유에는 quote를 붙임
- 책 원문 인용 안에는 bold, italic, highlight를 추가하지 않음

> [!warning] 인용 밀도
>
> Code Complete 2는 설명이 긴 책이므로 Pro Git처럼 모든 개념마다 quote를 붙이면 노트가 무거워질 수 있음. 인용할 만큼 중요한 문장만 가져오고, 바로 뒤에 설명을 작성함.

## 7. 코드 블록

- 언어별 코드 블록은 실제 언어명을 사용
- 쉘 명령은 `zsh` 사용
- 쉘 프롬프트는 `$ ` 포함
- 출력 예시는 `text` 사용
- 책의 코드 예제를 그대로 길게 옮기지 않음
- 필요한 경우 요지를 보여주는 짧은 예시로 재작성함

예시:

```zsh
$ dotnet test
```

```text
Passed!
```

언어 예시:

```java
int elapsedDays = daysSinceLastRelease(releaseDate);
```

## 8. 강조 규칙

네 가지 인라인 마크업이 각자 한 가지 역할만 맡음.

- `**bold**` - 새 핵심 개념의 첫 도입
- `*italic*` - 도입된 핵심 개념의 재언급
- `==highlight==` - 문장 안의 핵심 판단 포인트
- `` `backtick` `` - 코드 식별자, 명령어, 파일명, 옵션, 디렉터리

Pro Git 노트보다 완화된 정책을 적용함.

- 모든 일반 단어에 italic을 강제하지 않음
- 장 전체를 관통하는 핵심 개념어에만 첫 도입/재언급 규칙을 적용
- 번역어가 흔들릴 수 있는 용어는 첫 도입 시 원어를 병기함

예시:

```md
**정보 은닉(information hiding)** 은 구현 세부사항을 interface 뒤로 숨기는 설계 원칙임.

*정보 은닉*의 목적은 호출자가 불필요한 세부사항에 의존하지 않게 하는 것임.
```

판단 기준:

- "이 장에서 중심이 되는 개념인가?" -> `**bold**`
- "이미 도입한 중심 개념을 다시 가리키는가?" -> `*italic*`
- "이 문장에서 독자가 놓치면 안 되는 판단 지점인가?" -> `==highlight==`
- "코드 또는 코드에 준하는 식별자인가?" -> `` `backtick` ``

## 9. 용어 표기

한국어판을 기준으로 읽되, 개발 개념의 안정성을 위해 영문 병기를 적극 사용함.

- 최초 도입: `**한국어 용어(English term)**`
- 이후 재언급: 문맥상 더 자연스러운 하나로 통일
- 코드 구조 용어는 영어를 유지해도 됨
- 번역어가 흔들리는 경우 `terms.md`에 고정함

우선 유지할 영문 용어:

- `construction`
- `complexity`
- `routine`
- `class`
- `module`
- `interface`
- `abstraction`
- `information hiding`
- `coupling`
- `cohesion`
- `defensive programming`
- `assertion`
- `refactoring`
- `test`
- `debugging`

번역 원칙:

- 한국어 번역어가 자연스럽고 널리 쓰이면 한국어 우선
- 영어가 더 정확하거나 코드 맥락과 직접 연결되면 영어 유지
- 같은 노트 안에서는 한 용어의 표기를 흔들지 않음

## 10. 이미지 임베드

- Obsidian wiki-link 사용: `![[filename.png]]`
- 표준 마크다운 이미지 문법은 사용하지 않음
- 책의 도표를 그대로 옮기기보다, 필요한 경우 직접 재구성한 다이어그램을 사용

## 11. 상호 참조와 Anchor

Anchor 이름은 kebab-case 의미명 사용.

- 정의: 줄 끝에 `^kebab-anchor-name`
- 동일 노트 내 참조: `[[#^anchor-name]]`
- 헤딩 참조: `[[#Heading Name]]`
- 타 노트 참조: `[[books/code-complete-2/{path}/index#Section|표시 텍스트]]`

Wiki-link 표기:

- pipe 양쪽 공백 없음: `[[ref|display]]`
- cross-link 뒤에 "참고" 같은 보조어 첨가 금지
- 장을 넘나드는 핵심 개념에는 anchor를 부여함

## 12. 노트 밀도

Code Complete 2는 모든 내용을 같은 밀도로 정리하지 않음.

- 핵심 원칙: quote + 설명 + principle
- 코드 예시: 짧은 코드 + 왜 좋은지/나쁜지 설명
- 사례 설명: 필요한 논지만 압축
- 반복적인 부연: 과감히 압축
- 개인 의견: 별도 note로 최소화

기준은 "나중에 코드를 작성하거나 리뷰할 때 다시 쓸 수 있는가"임.

## 13. 규칙을 확장하는 방식

실제로 읽으면서 다음 항목을 관찰하고 이 규칙 파일에 반영함.

- 책이 반복해서 사용하는 핵심 용어
- 한국어판 번역어와 원어를 같이 고정해야 하는 용어
- 장별로 반복되는 설명 패턴
- 코드 예시를 어느 정도까지 재작성할지
- 인용을 붙여야 하는 문장과 설명만으로 충분한 문장의 기준

새 규칙이 필요해지는 경우:

1. 먼저 해당 노트 안에서 임시로 일관되게 적용함
2. 같은 패턴이 두 번 이상 반복되면 이 파일에 일반 규칙으로 추가함
3. 기존 규칙과 충돌하면 더 읽기 쉬운 쪽을 우선함

## 14. 새 노트 작성 체크리스트

- [ ] 경로/슬러그 규칙 준수
- [ ] Frontmatter의 `tags` 순서 준수
- [ ] H1/H2/H3 위계 준수
- [ ] 인용할 만큼 중요한 문장에만 quote 사용
- [ ] quote 뒤에 설명을 붙임
- [ ] 한국어 설명은 명사형 종결
- [ ] 핵심 개념은 첫 도입 bold, 재언급 italic
- [ ] 코드 식별자는 backtick 사용
- [ ] 판단 포인트는 highlight 사용
- [ ] 용어 표기가 같은 노트 안에서 흔들리지 않음
- [ ] 필요한 개념에 anchor 부여
- [ ] 개인 의견과 책의 주장을 구분

---

## 기준 노트

현재 기준 노트는 다음 두 파일임.

- `01-welcome-to-software-construction/01-01-what-is-software-construction/index.md`
- `01-welcome-to-software-construction/01-02-why-is-software-construction-important/index.md`
