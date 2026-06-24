---
tags:
  - books
  - pro-git
aliases:
  - 2.7 Git Basics - Git Aliases
references:
  - https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases
---
# 2.7 Git Basics - Git Aliases

## Git Aliases

> [!quote]
>
> Git doesn't automatically infer your command if you type it in partially. If you don't want to type the entire text of each of the Git commands, you can easily set up an alias for each command using `git config`. Here are a couple of examples you may want to set up:
>
> ```zsh
> $ git config --global alias.co checkout
> $ git config --global alias.br branch
> $ git config --global alias.ci commit
> $ git config --global alias.st status
> ```

Git의 command 입력 동작
- Git은 ==명령을 부분적으로 입력해도 자동으로 추론해주지 않음==
	- 예: `git com`만 입력해도 `git commit`으로 ==자동 확장되지 않음==
- 매번 ==명령 전체를 타이핑==해야 함

**Alias**
- 자주 쓰는 명령에 ==짧은 별칭==을 붙여 등록하는 기능
- `git config`로 ==손쉽게 설정== 가능
- 긴 명령 전체를 매번 입력하는 부담을 줄여줌

*alias* 등록 형식
- `git config --global alias.<short> <full-command>`
	- `--global`: ==전역 설정==으로 등록 (해당 사용자의 모든 repository에 적용)
	- `alias.<short>`: 등록할 ==별칭 이름==
	- `<full-command>`: 별칭이 ==대체할 실제 Git 하위 명령==

예시 alias 해석
- `alias.co checkout` → `git co` = `git checkout`
- `alias.br branch` → `git br` = `git branch`
- `alias.ci commit` → `git ci` = `git commit`
- `alias.st status` → `git st` = `git status`

> [!quote]
>
> This means that, for example, instead of typing `git commit`, you just need to type `git ci`.

위 alias 등록의 효과
- `git commit`을 ==전부 타이핑할 필요 없이== `git ci`만 입력해도 ==동일하게 동작==함
- 다른 *alias*들도 ==같은 방식==으로 단축 입력 가능

> [!quote]
>
> This technique can also be very useful in creating commands that you think should exist. For example, to correct the usability problem you encountered with unstaging a file, you can add your own unstage alias to Git:
>
> ```zsh
> $ git config --global alias.unstage 'reset HEAD --'
> ```

*alias*의 또 다른 용도
- 단순 단축뿐 아니라 ==존재했으면 좋겠는 명령을 직접 만들기==에도 유용
- 예: [[books/pro-git/02-git-basics/02-04-undoing-things/index#Unstaging a Staged File|2.4 Unstaging a Staged File]]에서 본 ==unstage UX 문제==를 alias로 보완 가능
	- 원래 명령 `git reset HEAD <file>`은 ==이름만 봐서는 unstage라는 의도가 잘 드러나지 않음==
	- `unstage`라는 ==의도가 명확한 이름==을 직접 부여

위 명령 해석 `git config --global alias.unstage 'reset HEAD --'`
- `alias.unstage` → 새 *alias* 이름(`<short>`)은 `unstage`
- `'reset HEAD --'` → 대체되는 실제 명령
	- ==뒤따라 오는 인자를 file path로 강제 해석==하기 위해 끝에 `--`를 둠
		- `--` 뒤의 토큰은 ==option이 아니라 path==로 처리됨
		- branch / tag 이름과 동일한 file 이름이 와도 ==모호성 제거==
		- [[books/pro-git/02-git-basics/02-03-viewing-the-commit-history/index#^double-dash-separator|2.3 path filter에서 본 -- separator]]와 동일한 관용
- `<full-command>` 인자는 `'`를 ==사용해 quoting==해서 여러 토큰을 ==하나의 alias value==로 묶음

> [!quote]
>
> This makes the following two commands equivalent:
>
> ```zsh
> $ git unstage fileA
> $ git reset HEAD -- fileA
> ```

위 등록 결과 ==두 명령이 동치==
- `git unstage fileA` = `git reset HEAD -- fileA`
- *alias*의 뒤에 입력한 추가 인자(`fileA`)는 ==그대로 확장된 명령의 끝에 붙음==
	- 즉 `git <alias> <args>` → `git <alias-value> <args>`로 단순 치환
	- 그래서 `'reset HEAD --'`의 끝 `--` 덕분에 `fileA`가 ==항상 path로 해석==됨

> [!quote]
>
> It's also common to add a `last` command, like this:
>
> ```zsh
> $ git config --global alias.last 'log -1 HEAD'
> ```
>
> This way, you can see the last commit easily:
>
> ```zsh
> $ git last
> commit 66938dae3329c7aebe598c2246a8e6af90d04646
> Author: Josh Goebel <dreamer3@example.com>
> Date:   Tue Aug 26 19:48:51 2008 +0800
>
>     Test for current head
>
>     Signed-off-by: Scott Chacon <schacon@example.com>
> ```

또 다른 자주 쓰는 alias: `last`
- 등록: `git config --global alias.last 'log -1 HEAD'`
	- `log -1`: 최근 ==1개의 commit만== 출력
		- `git log -<n>`은 [[books/pro-git/02-git-basics/02-03-viewing-the-commit-history/index|2.3 Viewing the Commit History]]에서 다룬 ==출력 개수 제한 옵션==
	- `HEAD`: ==출력 시작점== — 현재 *branch*가 가리키는 *commit*
- 사용: `git last` → ==직전 commit 하나를 즉시 확인==
	- *commit* hash / author / date / message / footer(`Signed-off-by` 등) 모두 표시

> [!quote]
>
> As you can tell, Git simply replaces the new command with whatever you alias it for. However, maybe you want to run an external command, rather than a Git subcommand. In that case, you start the command with a `!` character. This is useful if you write your own tools that work with a Git repository. We can demonstrate by aliasing `git visual` to run `gitk`:
>
> ```zsh
> $ git config --global alias.visual '!gitk'
> ```

*alias* 확장의 기본 동작 재확인
- Git은 등록된 *alias*를 ==alias value로 단순 치환==함
	- 지금까지의 예시는 모두 ==Git 하위 명령==(`commit`, `reset`, `log`)으로 치환

==외부 명령==을 실행하고 싶을 때: `!` prefix
- *alias value* 앞에 `!`를 붙이면 ==Git 하위 명령이 아니라 외부 shell command==로 실행됨
- 활용 예
	- ==Git repository와 함께 동작하는 자체 도구==를 alias로 노출
	- `gitk`처럼 ==별도 binary로 제공되는 도구==를 짧게 호출

예시 `git config --global alias.visual '!gitk'`
- `git visual` 실행 시 Git이 `gitk`를 ==외부 명령으로 실행==
- `!`가 없었다면 Git은 `gitk`를 ==하위 명령으로 해석하려 시도해 실패==했을 것
