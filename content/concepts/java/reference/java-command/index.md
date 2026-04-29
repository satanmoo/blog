---
aliases:
  - java command
tags:
  - reference
  - java
  - java-command
references: https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html
---
# java command

`java`는 Java application을 실행하는 launcher

## `java -version`

아래 명령어는 `java`라는 프로그램에 `-version` 옵션을 전달해서 실행함

```zsh
java -version
```

`-version` 옵션은 Java application을 실행하지 않고, 현재 실행된 `java`가 속한 **product**의 버젼을 보여준다.

예상되는 출력은 다음과 같음

```zsj
openjdk version "21.0.11" 2026-04-21 LTS
OpenJDK Runtime Environment Temurin-21.0.11+10 (build 21.0.11+10-LTS)
OpenJDK 64-Bit Server VM Temurin-21.0.11+10 (build 21.0.11+10-LTS, mixed mode, sharing)
```

**product**라고 표현한 이유는 복합적인 정보를 보여주기 때문
- 현재 실행된 `java`가 속한 JDK
- Runtime Environment
- VM 구현체
