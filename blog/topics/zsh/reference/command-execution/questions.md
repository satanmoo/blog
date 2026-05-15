---
tags:
  - reference
  - questions
  - zsh
aliases:
  - Command Execution
references: https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution
---
# Command Execution

`java`와 같이 슬래시 없으면 쉘은 탐색을 시도함
- 참고로 슬래시가 있으면 경로로 취급해서 OS에 그대로 넘김

명령 이름과 일치하는 *Shell Function*이 있으면 실행하고, 없으면 *Shell Builtin*을 탐색해 실행함
- [[topics/zsh/reference/functions/questions|Functions]]
- [[topics/zsh/reference/shell-builtin-commands/questions|Shell Builtin Commands]]

위에서 해당되지 않으면 `$path`의 원소(실행 파일이 포함된 디렉토리)를 탐색

## element of `$path`

[[topics/zsh/reference/parameters/questions#15.6 Parameters Used By The Shell#`path`|path]] 참고
