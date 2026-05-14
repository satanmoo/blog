---
tags:
  - ktor
  - setup
aliases:
  - "Ktor Setup (2): 프로젝트에 Java 설정"
---
# Ktor Setup (2): 프로젝트에 Java 설정

[[tutorial/mac-dev-setup/asdf/index#1-1-5. macOS `JAVA_HOME` integration|asdf 설정 1-1-5]]까지 완료했다고 가정

[[tutorial/ktor-setup/ktor-setup-1/index|Ktor Setup (1): Ktor Project Generator로 프로젝트 생성]]에서 다운로드 받은 프로젝트의 압축을 풀었던 폴더(프로젝트 루트)에서 다음 유틸리티 실행

```zsh
asdf set java latest:temurin-21
```

예상되는 결과는 다음과 같음
- `.tool-versions`가 프로젝트 루트에 생성됨
- 이 파일은 `java temurin-21.0.11+10.0.LTS` 같은 내용을 포함

이제 이 프로젝트에서 어떤 Java를 사용하는지 지정만 했을 분 설치 여부는 아직 모름

다음 유틸리티를 실행해 설치 여부를 확인하자

```zsh
asdf current java
```

예상되는 결과는 다음과 같음

```zsh
Name            Version                  Source                                          Installed
java            temurin-21.0.11+10.0.LTS <project-root>/.tool-versions false - Run `asdf install java temurin-21.0.11+10.0.LTS`
```

지금처럼 설치 되지 않은 경우 아래 유틸리티를 실행해 설치하자

```zsh
asdf install
```

이 유틸리티는 `.tool-versions` 파일에 따라 필요한 패키지를 설치함

다시 아래와 같이 확인해보면

```zsh
asdf current java
```

예상되는 결과는 다음과 같음

```zsh
Name            Version                  Source                                          Installed
java            temurin-21.0.11+10.0.LTS <project-root>/.tool-versions true
```

현재 터미널 세션에서 이 디렉터리를 기준으로 `java` 명령이 어떤 Java를 실행하는지 확인하기 위해 다음을 실행
- [[topics/java/reference/java-command/index|java command]] 참고

```zsh
java -version
```

예상되는 결과는 다음과 같음

```zsh
openjdk version "21.0.11" 2026-04-21 LTS
OpenJDK Runtime Environment Temurin-21.0.11+10 (build 21.0.11+10-LTS)
OpenJDK 64-Bit Server VM Temurin-21.0.11+10 (build 21.0.11+10-LTS, mixed mode, sharing)
```

asdf로 설정한 Java가 실행되는 것을 확인하자

> [!TODO]
> 
> 

1. 터미널에서 Java 명령어를 실행할 때 까지 생기는 과정
2. asdf가 어떻게 가로채는지
3. gradlew 및 동작
	1. 쉘의 Java 환경을 이어 받는지?
		1. 터미널에서 gradle
		2. intellij gradle 플러그인
4. intellij gradle plugin 의 동작

java 명령어의 정체
asdf의 동작(shim)
- 재귀적으로 어떻게 찾는가
- 부모를 찾아서...
쉘의 동작
intellij 에서 설정하는 법
gradle에서 설정하는법
asdf에서 JDK 관리 -> gradle build 에서 동일한 JDK 사용 -> github aciton 등 배포 라인에서 동일한 JDK 사용

---


