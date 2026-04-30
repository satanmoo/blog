---
aliases:
  - 00. zsh simple command
tags:
  - explanation
  - zsh
---
# 00. zsh simple command

터미널에서 아래처럼 입력한다고 하자

```zsh
java -version
```

zsh의 Shell Grammar 기준으로 이 입력은 [[concepts/zsh/reference/shell-grammer/questions#Simple Commands|Simple  Command]]로 볼 수 있음

이 입력에는 두 개의 word가 있음

- `java`
- `-version`

simple command에서 첫 번째 word는 실행할 command이고, 나머지 word는 command에 전달되는 argument임

command가 아직 실행된 것은 아님
