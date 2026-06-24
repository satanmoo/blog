---
tags:
  - books
  - pro-git
references:
  - https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository
---
# 2.2 Git Basics - Recording Changes to the Repository

## Recording Changes to the Repository

> [!quote]
> At this point, you should have a bona fide Git repository on your local machine, and a checkout or working copy of all of its files in front of you. Typically, you’ll want to start making changes and committing snapshots of those changes into your repository each time the project reaches a state you want to record.

"bona fide": 진정한, 명실상부한

작업하기 전 상태는 다음과 같음
- 정상적인 Git repository가 존재함
- 그 repository의 file들이 working copy 형태로 눈앞에 있는 상태
	- [[books/pro-git/01-getting-started/01-03-what-is-git/index#^checkout|checkout]]은 여기서 working copy를 가리키는 표현(*checkout*의 결과)으로 사용됨

원하는 시점의 작업 결과를 기록하기 위해 스냅샷을 커밋해야 함

> [!quote]
> Remember that each file in your working directory can be in one of two states: tracked or untracked. Tracked files are files that were in the last snapshot, as well as any newly staged files; they can be unmodified, modified, or staged. In short, tracked files are files that Git knows about.

*working directory* 안의 각 file은 크게 두 상태 중 하나
- **tracked**
- **untracked**

*tracked* file은 Git이 알고 있는 file
- 마지막 commit snapshot에 들어 있던 file
- 새로 *staging area*에 올린 file

*tracked* file은 다시 세부 상태를 가질 수 있음 ^tracked-file-states
- **unmodified**
	- 마지막 commit 이후 변경되지 않은 상태
- **modified**
	- 마지막 commit 이후 변경되었지만 아직 staging하지 않은 상태
- **staged**
	- 다음 commit에 들어가도록 *staging area*에 올려둔 상태

> [!summary] 핵심 구분
> *tracked*/*untracked*는 Git이 해당 file을 관리 대상으로 알고 있는지에 대한 구분
>
> *modified*/*staged*/*unmodified*는 *tracked* file 내부의 세부 상태

> [!quote]
> Untracked files are everything else — any files in your working directory that were not in your last snapshot and are not in your staging area. When you first clone a repository, all of your files will be tracked and unmodified because Git just checked them out and you haven’t edited anything.

*untracked* file은 Git이 *tracked* file이 아닌 file
- 마지막 commit snapshot에 없었음
- *staging area*에도 올라가 있지 않음

repository를 처음 clone한 직후에는 모든 file이 *tracked*이면서 *unmodified* 상태

> [!quote]
> As you edit files, Git sees them as modified, because you’ve changed them since your last commit. As you work, you selectively stage these modified files and then commit all those staged changes, and the cycle repeats.

file을 수정하면 Git은 해당 file을 *modified* 상태로 봄
- 여기서 말하는 수정 대상은 이미 *tracked* 상태인 file
- Git은 아래 근거로 수정된 file을 *modified* 상태로 봄
	- 마지막 commit snapshot과 현재 *working directory*의 file 내용이 달라짐
	- 마지막 commit snapshot에서 이미 해당 file을 tracking하고 있는 개념

다음의 작업 흐름은 반복됨
- file 수정
	- *modified* 상태
- 수정한 file 중 다음 commit에 넣을 것만 선택해서 stage
	- *staged* 상태
- *staged* changes를 commit
- 다시 file 수정...

![[recording-changes-to-the-repository-001.png]] ^file-status-lifecycle

### Checking the Status of Your Files

> [!quote]
> The main tool you use to determine which files are in which state is the `git status` command. If you run this command directly after a clone, you should see something like this:

file이 어떤 상태인지 확인할 때 주로 사용하는 명령은 `git status`

repository를 clone한 직후 `git status`를 실행하면 대략 아래와 같은 결과가 나옴

```zsh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working tree clean
```

> [!quote]
> This means you have a clean working directory; in other words, none of your tracked files are modified. Git also doesn’t see any untracked files, or they would be listed here. Finally, the command tells you which branch you’re on and informs you that it has not diverged from the same branch on the server. For now, that branch is always `master`, which is the default; you won’t worry about it here. Git Branching will go over branches and references in detail.

`working tree clean`은 *working directory*가 깨끗하다는 뜻
아래의 조건을 만족함
- *modified* 상태인 *tracked* file이 없음
- *untracked* file도 없음

`git status`는 file 상태 외에도 branch 정보를 함께 보여줌
- 현재 branch
	- 예시에서는 `master`
- server의 같은 branch와 diverge되지 않았다는 정보 ("up-to-date")
	- 예시에서는 `origin/master`와 차이가 벌어지지 않은 상태

> [!note] default branch 이름
> GitHub는 2020년 중반 default branch 이름을 `master`에서 `main`으로 변경했고, 다른 Git hosting service도 이를 따르는 경우가 많음
>
> 따라서 새로 만든 repository에서는 default branch가 `master`가 아니라 `main`일 수 있음
>
> default branch 이름은 설정으로 바꿀 수 있으므로 `master`, `main` 외의 이름도 가능함
>
> 이 책에서는 Git 자체의 기본값을 기준으로 `master`를 사용함

> [!summary] clean working directory
> `git status`에서 `nothing to commit, working tree clean`이 보이면 Git 관점에서 commit할 변경사항이 없다는 뜻
>
> *tracked* file의 수정도 없고, 새 *untracked* file도 보이지 않는 상태

> [!quote]
> Let’s say you add a new file to your project, a simple `README` file. If the file didn’t exist before, and you run `git status`, you see your untracked file like so:

project에 새 file `README`를 추가한다고 가정

이 file이 이전 commit snapshot에 없던 file이라면, `git status`에서 *untracked* file로 표시됨

```zsh
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README

nothing added to commit but untracked files present (use "git add" to track)
```

> [!quote]
> You can see that your new `README` file is untracked, because it’s under the “Untracked files” heading in your status output. Untracked basically means that Git sees a file you didn’t have in the previous snapshot (commit), and which hasn’t yet been staged; Git won’t start including it in your commit snapshots until you explicitly tell it to do so. It does this so you don’t accidentally begin including generated binary files or other files that you did not mean to include. You do want to start including `README`, so let’s start tracking the file.

새로 만든 `README`는 `Untracked files` 아래에 표시됨

*untracked*의 의미
- 이전 snapshot(commit)에 없던 file
- 아직 *staging area*에도 올라가지 않은 file
	- Git이 아직 다음 commit snapshot에 포함할 대상으로 보지 않는 file

Git은 *untracked* file을 자동으로 commit snapshot에 포함하지 않음
- 실수로 생성된 binary file, build output 등을 commit에 넣는 것을 막기 위함

*untracked* file을 commit에 포함하고 싶으면 명시적으로 tracking을 시작해야 함

> [!summary] untracked file
> *untracked* file은 *working directory*에는 있지만, 이전 commit snapshot에도 없고 *staging area*에도 없는 file
>
> Git은 이런 file을 사용자가 명시적으로 staging하기 전까지 commit 대상으로 포함하지 않음

### Tracking New Files

> [!quote]
> In order to begin tracking a new file, you use the command `git add`. To begin tracking the `README` file, you can run this:

새 file을 tracking하기 시작하려면 `git add`를 사용함

`README` file을 tracking하려면 아래처럼 실행

```zsh
$ git add README
```

> [!quote]
> If you run your status command again, you can see that your `README` file is now tracked and staged to be committed:

다시 `git status`를 실행하면 `README`가 *tracked* 상태가 되었고, commit될 대상으로 *staged*된 것을 볼 수 있음
- `git add` 명령어가 아래 그림에서 "Add the file"에 대응
	- [[#^file-status-lifecycle]]
- `git add`는 파일의 상태를 *staged*로 만듦 ^git-add-untracked-to-staged
	- *staged*는 *tracked* 상태에 포함되는 개념
		- 따라서 *tracked* 상태가 되었다고 볼 수 있음
		- [[#^tracked-file-states]]

```zsh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README
```

> [!quote]
> You can tell that it’s staged because it’s under the “Changes to be committed” heading. If you commit at this point, the version of the file at the time you ran `git add` is what will be in the subsequent historical snapshot.

`git status`에서 `Changes to be committed` 아래에 표시되는 file(지금 예시에서는 `README`)들은 *tracked* 상태

이 시점에 commit하면 다음 historical snapshot에는 `git add`를 ==실행한 시점==의 `README` file의 version이 들어감

> [!quote]
>
> You may recall that when you ran `git init` earlier, you then ran `git add <files>` — that was to begin tracking files in your directory.

앞에서 [[books/pro-git/02-git-basics/02-01-getting-a-git-repository/index#^tracking-files-with-git-add|git init 이후 git add를 실행했던 것]]도 directory 안의 file들을 tracking하기 위한 작업이었음

> [!quote]
>
>  The `git add` command takes a path name for either a file or a directory; if it’s a directory, the command adds all the files in that directory recursively.

`git add`는 file path나 directory path를 argument로 받음
- file path를 넘기면 해당 file을 staging함
- directory path를 넘기면 그 directory 안의 file들을 recursively staging함

> [!summary] git add
> `git add`는 *untracked* file을 tracking하기 시작하게 만들고, 그 file의 현재 상태를 *staging area*에 올림
>
> *staged*된 file은 다음 commit snapshot에 들어갈 후보가 됨

### Staging Modified Files

> [!quote]
> Let’s change a file that was already tracked. If you change a previously tracked file called `CONTRIBUTING.md` and then run your `git status` command again, you get something that looks like this:

이번에는 이미 *tracked* 상태인 file을 수정한다고 가정

예를 들어 기존에 *tracked* 상태였던 `CONTRIBUTING.md`를 수정한 뒤 `git status`를 실행하면 아래와 비슷한 결과가 나옴

```zsh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

> [!quote]
> The `CONTRIBUTING.md` file appears under a section named “Changes not staged for commit” — which means that a file that is tracked has been modified in the working directory but not yet staged.

`git status`에서 `CONTRIBUTING.md`는 `Changes not staged for commit` 아래에 표시됨

이는 다음 상태를 의미함
- *modified* 상태
	- `CONTRIBUTING.md`는 *tracked* file임
	- *working directory*에서 수정됨
	- 하지만 수정된 현재 내용은 아직 *staging area*에 올라가지 않음
		- [[#^tracked-file-states]]

> [!quote]
>
> To stage it, you run the `git add` command. `git add` is a multipurpose command — you use it to begin tracking new files, to stage files, and to do other things like marking merge-conflicted files as resolved.

이 file을 stage하려면 다시 `git add`를 실행함

지금까지 `git add`을 사용하는 목적 2가지를 배움
1. 파일을 *untracked* 상태에서 *staged* 상태로 변경
	- [[#^git-add-untracked-to-staged]]에서 본 상황
2. 파일을 *modified* 상태에서 *staged* 상태로 변경

"marking merge-conflicted files as resolved" 상황은 나중으로 미룸

> [!summary] git add의 역할
>
> `git add`는 여러 상황에서 사용되는 명령
> - 새 file을 tracking하기 시작할 때
> - 수정된 file을 *staging area*에 올릴 때

> [!quote]
>
> It may be helpful to think of it more as “add precisely this content to the next commit” rather than “add this file to the project”.

> [!tip]
>
> `git add`는 "file을 project에 추가한다"보다 "이 시점의 정확한 내용을 다음 commit에 추가한다"에 가깝게 이해하는 것이 좋음

> [!quote]
>
> Let’s run `git add` now to stage the `CONTRIBUTING.md` file, and then run `git status` again:

`git add`를 실행하고 `git status`로 상태를 확인해보면 다음과 같음

```zsh
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

`CONTRIBUTING.md`도 `Changes to be committed` 아래에 표시됨

> [!quote]
>
> Both files are staged and will go into your next commit.

이제 다음 commit에 들어갈 후보는 두 file
- `README`
	- 새 file
- `CONTRIBUTING.md`
	- 수정된 *tracked* file

> [!quote]
> At this point, suppose you remember one little change that you want to make in `CONTRIBUTING.md` before you commit it. You open it again and make that change, and you’re ready to commit. However, let’s run `git status` one more time:

그런데 commit하기 전에 `CONTRIBUTING.md`를 한 번 더 수정한다고 가정
- 이미 *staged* 상태의 파일을 수정하는 경우

```zsh
$ vim CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

> [!quote]
> What the heck? Now `CONTRIBUTING.md` is listed as both staged and unstaged. How is that possible?

이제 `CONTRIBUTING.md`가 두 곳에 모두 표시됨
- `Changes to be committed`
- `Changes not staged for commit`

> [!quote]
>
> It turns out that Git stages a file exactly as it is when you run the `git add` command.

이는 Git이 *working directory*의 최신 내용을 *staging area*에 자동 반영하는 것이 아니라, `git add`를 실행한 시점의 file 내용을 *staging area*에 올리기 때문

현재 상태를 구분하면 다음과 같음
- *staging area*의 `CONTRIBUTING.md`
	- 마지막으로 `git add CONTRIBUTING.md`를 실행했을 때의 version
- *working directory*의 `CONTRIBUTING.md`
	- 그 이후 다시 수정한 최신 version

> [!quote]
>
> If you commit now, the version of `CONTRIBUTING.md` as it was when you last ran the `git add` command is how it will go into the commit, not the version of the file as it looks in your working directory when you run `git commit`.

이 상태에서 commit하면 commit에는 마지막 `git add` 시점의 `CONTRIBUTING.md` version이 들어감

> [!quote]
>
> If you modify a file after you run `git add`, you have to run `git add` again to stage the latest version of the file:

*working directory*에 있는 최신 수정 내용까지 commit에 포함하려면 `git add`를 ==반드시 다시== 실행해야 함

```zsh
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

다시 `git add CONTRIBUTING.md`를 실행하면 최신 version의 `CONTRIBUTING.md`가 *staging area*에 올라감

이제 `CONTRIBUTING.md`는 `Changes not staged for commit`에 표시되지 않음

> [!summary] staged snapshot
> Git은 `git add`를 실행한 순간의 file 내용을 *staging area*에 기록함
>
> `git add` 이후 같은 file을 다시 수정하면, *staged* version과 *working directory* version이 달라질 수 있음
>
> 최신 수정까지 다음 commit에 포함하려면 `git add`를 다시 실행해야 함

### Short Status

> [!quote]
> While the `git status` output is pretty comprehensive, it’s also quite wordy. Git also has a short status flag so you can see your changes in a more compact way. If you run `git status -s` or `git status --short` you get a far more simplified output from the command:

`git status`로 변경 상태를 더 짧게 보려면 아래처럼 옵션을 추가하면
- `git status -s`
- `git status --short`

```zsh
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

> [!quote]
> New files that aren’t tracked have a `??` next to them, new files that have been added to the staging area have an `A`, modified files have an `M` and so on.

다음 형식`{short-status} {file-name}`의 row 출력

short status는 file 상태를 짧은 기호로 보여줌
- `??`: *untracked* file
- `A`: 새 file이 *staging area*에 올라간 상태
- `M`: *modified* 상태

> [!quote]
>
> There are two columns to the output — the left-hand column indicates the status of the staging area and the right-hand column indicates the status of the working tree.

short status 출력은 두 column으로 구성됨
- 왼쪽 column
	- *staging area*의 상태
- 오른쪽 column
	- *working tree*의 상태

> [!quote]
>
> So for example in that output, the `README` file is modified in the working directory but not yet staged, while the `lib/simplegit.rb` file is modified and staged. The `Rakefile` was modified, staged and then modified again, so there are changes to it that are both staged and unstaged.

예시를 해석하면 다음과 같음
- ` M README`
	- `README`는 *working tree*에서 수정됨
		- 오른쪽 column
	- 아직 *staging area*에는 올라가지 않음
- `MM Rakefile`
	- `Rakefile`은 수정 후 *staged*됨
		- 왼쪽 column
	- 그 이후 *working tree*에서 다시 수정됨
		- 오른쪽 column
		- "그 이후"인 이유: `git add`의 동작 때문, 만약에 *working tree*에서 먼저 수정하고 staging 했으면 이 file은 *staged* 상태일 수 밖에 없음
			- [[#Staging Modified Files]]
- `A  lib/git.rb`
	- 새 file `lib/git.rb`가 *staging area*에 올라감
		- 왼쪽 column
			- `A`는 왼쪽일 수 밖에 없음, *tracked* 상태의 file을 새로 추가할 수는 없으니
- `M  lib/simplegit.rb`
	- `lib/simplegit.rb`의 수정 내용이 *staging area*에 올라감
		- 왼쪽 column
- `?? LICENSE.txt`
	- `LICENSE.txt`는 *untracked* file

> [!summary] short status 두 칸 읽기
> 왼쪽 column은 *staging area*의 상태, 오른쪽 column은 *working tree*의 상태
>
> `M`이 왼쪽에 있으면 *staged* change이고, 오른쪽에 있으면 *unstaged* change
>
> `MM`은 *staged*된 수정도 있고, 그 이후 *working tree*에서 다시 수정한 내용도 있다는 뜻

### Ignoring Files

> [!quote]
> Often, you’ll have a class of files that you don’t want Git to automatically add or even show you as being untracked. These are generally automatically generated files such as log files or files produced by your build system. In such cases, you can create a file listing patterns to match them named `.gitignore`. Here is an example `.gitignore` file:

Git이 자동으로 추가하지도 않고, *untracked* file로 보여주지도 않았으면 하는 file들이 있음
- log file
- build system이 생성한 file
- 임시 file

이런 file들은 `.gitignore` file에 pattern으로 적어둘 수 있음

```zsh
$ cat .gitignore
*.[oa]
*~
```

> [!quote]
> The first line tells Git to ignore any files ending in “.o” or “.a” — object and archive files that may be the product of building your code. The second line tells Git to ignore all files whose names end with a tilde (`~`), which is used by many text editors such as Emacs to mark temporary files. You may also include a log, tmp, or pid directory; automatically generated documentation; and so on. Setting up a `.gitignore` file for your new repository before you get going is generally a good idea so you don’t accidentally commit files that you really don’t want in your Git repository.

위 예시의 의미
- `*.[oa]`
	- `.o` 또는 `.a`로 끝나는 file을 ignore
	- code build 결과물인 object file, archive file을 제외할 때 사용
- `*~`
	- `~`로 끝나는 file을 ignore
	- Emacs 같은 text editor가 만드는 temporary file을 제외할 때 사용

새 repository에서 작업을 시작하기 전에 `.gitignore`를 설정해두면 원하지 않는 file을 실수로 commit하는 일을 줄일 수 있음

> [!summary] .gitignore
> `.gitignore`는 Git이 *untracked* file로 보여주거나 commit 대상으로 추가하지 않았으면 하는 file pattern 목록
>
> 주로 log, temporary file, build output처럼 자동 생성되는 file을 제외하는 데 사용함

> [!quote]
> The rules for the patterns you can put in the `.gitignore` file are as follows:
>
> Blank lines or lines starting with `#` are ignored.
>
> Standard glob patterns work, and will be applied recursively throughout the entire working tree.
>
> You can start patterns with a forward slash (`/`) to avoid recursivity.
>
> You can end patterns with a forward slash (`/`) to specify a directory.
>
> You can negate a pattern by starting it with an exclamation point (`!`).

`.gitignore`에 적을 수 있는 pattern 규칙
- 빈 줄은 무시됨
- `#`로 시작하는 줄은 comment로 취급되어 무시됨
- [[#^glob-pattern|standard glob pattern]]을 사용할 수 있음
- pattern은 기본적으로 전체 *working tree*에 recursive하게 적용됨
- pattern을 `/`로 시작하면 recursive 적용을 피할 수 있음
	- 현재 `.gitignore` 위치 기준으로 match
- pattern을 `/`로 끝내면 directory를 의미함
- pattern을 `!`로 시작하면 앞에서 ignore된 pattern을 다시 포함할 수 있음

> [!quote]
> Glob patterns are like simplified regular expressions that shells use. An asterisk (`*`) matches zero or more characters; `[abc]` matches any character inside the brackets (in this case `a`, `b`, or `c`); a question mark (`?`) matches a single character; and brackets enclosing characters separated by a hyphen (`[0-9]`) matches any character between them (in this case `0` through `9`). You can also use two asterisks to match nested directories; `a/**/z` would match `a/z`, `a/b/z`, `a/b/c/z`, and so on.

**glob pattern**은 shell에서 사용하는 단순화된 pattern matching 규칙 ^glob-pattern
- `*`
	- 0개 이상의 문자와 match
- `[abc]`
	- bracket 안의 문자 중 하나와 match
		- 정확히 문자 하나만 올 수 있음
			- `ab`는 안 됨
	- 예: `a`, `b`, `c`
- `?`
	- 문자 하나와 match
- `[0-9]`
	- 범위 안의 문자 하나와 match
	- 예: `0`부터 `9`
- `**`
	- nested directory와 match

예를 들어 `a/**/z`는 아래 경로들과 match됨
- `a/z`
- `a/b/z`
- `a/b/c/z`

다른 `.gitignore` 예시는 아래와 같음

```gitignore
# ignore all .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in any directory named build
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf
```

위 예시를 해석하면 다음과 같음
- `*.a`
	- 모든 `.a` file을 ignore
- `!lib.a`
	- 위에서 `.a` file을 ignore했더라도 `lib.a`는 다시 tracking 대상에 포함
- `/TODO`
	- 현재 `.gitignore`가 있는 directory의 `TODO` file만 ignore
	- `subdir/TODO`는 ignore하지 않음
- `build/`
	- 이름이 `build`인 directory 안의 file들을 ignore
- `doc/*.txt`
	- `doc/notes.txt`는 ignore
	- `doc/server/arch.txt`는 ignore하지 않음
- `doc/**/*.pdf`
	- `doc/` directory와 그 하위 directory 안의 모든 `.pdf` file을 ignore

> [!tip]
> GitHub는 여러 project와 language를 위한 `.gitignore` 예시를 제공함
>
> 시작점이 필요하면 [github/gitignore](https://github.com/github/gitignore)를 참고할 수 있음

> [!note]
> 단순한 repository에서는 root directory에 `.gitignore` 하나를 두고 repository 전체에 recursive하게 적용하는 경우가 많음
>
> 하지만 subdirectory 안에 추가 `.gitignore`를 둘 수도 있음
>
> nested `.gitignore`의 rule은 해당 `.gitignore`가 위치한 directory 아래의 file에만 적용됨
>
> 여러 `.gitignore` file의 세부 동작은 `man gitignore`에서 확인할 수 있음

### Viewing Your Staged and Unstaged Changes

> [!quote]
> If the `git status` command is too vague for you — you want to know exactly what you changed, not just which files were changed — you can use the `git diff` command.

`git status`는 어떤 file이 변경되었는지 보여줌

하지만 정확히 어떤 line이 추가되거나 삭제되었는지 보려면 `git diff`를 사용함

> [!quote]
> We’ll cover `git diff` in more detail later, but you’ll probably use it most often to answer these two questions: What have you changed but not yet staged? And what have you staged that you are about to commit? Although `git status` answers those questions very generally by listing the file names, `git diff` shows you the exact lines added and removed — the patch, as it were.

`git diff`는 주로 아래 두 질문에 답할 때 사용함
- 아직 *staging area*에 올라가지 않은 변경은 무엇인가?
- 다음 commit에 들어갈 *staging area*에 올라간 변경은 무엇인가?

`git status`가 ==파일 이름== 중심으로 답한다면, `git diff`는 ==추가/삭제된 line==을 보여줌
- "patch" 는 라인 별로 보여준다는 의미

> [!quote]
> Let’s say you edit and stage the `README` file again and then edit the `CONTRIBUTING.md` file without staging it. If you run your `git status` command, you once again see something like this:

예를 들어 다음 상태라고 가정
- `README`
	- 수정 후 *staged* 상태
- `CONTRIBUTING.md`
	- 수정되었지만 아직 *staged* 상태 아님
	- *modified* 상태

```zsh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

> [!quote]
> To see what you’ve changed but not yet staged, type `git diff` with no other arguments:

argument 없이 `git diff`를 실행하면 아직 *staging area*에 올라가지 않은 변경을 보여줌

```zsh
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.
 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

> [!quote]
> That command compares what is in your working directory with what is in your staging area. The result tells you the changes you’ve made that you haven’t yet staged.

`git diff`의 기본 동작은 *working directory*와 *staging area*를 비교해 아직 *staging area*에 올라가지 않은 변경을 보여줌

위 예시에서는 `CONTRIBUTING.md`만 *staging area*에 올라가지 않은 상태이므로, `CONTRIBUTING.md`의 diff가 출력됨

> [!quote]
> If you want to see what you’ve staged that will go into your next commit, you can use `git diff --staged`. This command compares your staged changes to your last commit:

다음 commit에 들어갈 *staging area*에 올라간 변경을 보려면 `git diff --staged`를 사용함

`git diff --staged`는 *staging area*와 마지막 commit을 비교해 *staging area*에 올라간 변경을 보여줌

```zsh
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

위 예시에서는 `README`가 *staged* 상태이므로, `README`의 *staging area*에 올라간 diff가 출력됨

> [!quote]
> It’s important to note that `git diff` by itself doesn’t show all changes made since your last commit — only changes that are still unstaged. If you’ve staged all of your changes, `git diff` will give you no output.

`git diff` 옵션 없이 단독 실행은 아직 *staging area*에 올라가지 않은 변경만 보여줌

따라서 모든 변경이 이미 *staging area*에 올라갔다면 `git diff`는 아무 출력도 보여주지 않음

> [!summary] git diff 비교 기준
> `git diff`는 *working directory*와 *staging area*를 비교함
>
> `git diff --staged`는 *staging area*와 last commit을 비교함
>
> `git diff` 단독 실행은 last commit 이후의 모든 변경을 보여주는 명령이 아님

> [!quote]
> For another example, if you stage the `CONTRIBUTING.md` file and then edit it, you can use `git diff` to see the changes in the file that are staged and the changes that are unstaged. If our environment looks like this:

같은 file이 *staged* 상태이면서 동시에 *unstaged* 상태일 수 있음

아래 시나리오
1. 특정 시점의 file 변화는 이미 *staging area*에 올라감
2. 그 이후 *working directory*에서 같은 file을 다시 수정함

예를 들어 `CONTRIBUTING.md`를 *staging area*에 올린 뒤, 같은 file에 한 줄을 더 추가한다고 가정

```zsh
$ git add CONTRIBUTING.md
$ echo '# test line' >> CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

이 상태에서 `CONTRIBUTING.md`는 두 version으로 나뉘어 있음
- *staging area*의 `CONTRIBUTING.md`
	- `git add CONTRIBUTING.md`를 실행한 시점의 내용
- *working directory*의 `CONTRIBUTING.md`
	- 그 이후 `# test line`까지 추가된 최신 내용

> [!quote]
> Now you can use `git diff` to see what is still unstaged:

`git diff`는 아직 *staging area*에 올라가지 않은 추가 수정만 보여줌

```zsh
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 643e24f..87f08c8 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -119,3 +119,4 @@ at the
 ## Starter Projects
 See our [projects list](https://github.com/libgit2/libgit2/blob/development/PROJECTS.md).
+# test line
```

여기서 보이는 것은 `git add CONTRIBUTING.md` 이후 *working directory*에 추가한 `# test line`뿐임

> [!quote]
> and `git diff --cached` to see what you’ve staged so far (`--staged` and `--cached` are synonyms):

반대로 *staging area*에 올라간 변경만 보려면 `git diff --cached`를 사용할 수 있음

```zsh
$ git diff --cached
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.
 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

`git diff --cached`는 *staging area*에 올라간 변경만 보여줌

`--staged`와 `--cached`는 같은 의미의 option
- `git diff --staged`
- `git diff --cached`

> [!note] Git Diff in an External Tool
> 이 책에서는 이후에도 `git diff`를 여러 방식으로 계속 사용함
>
> graphical diff tool이나 external diff tool로 diff를 보고 싶다면 `git diff` 대신 `git difftool`을 사용할 수 있음
>
> `git difftool`은 emerge, vimdiff 같은 여러 diff viewer를 사용할 수 있음
>
> 현재 system에서 사용할 수 있는 tool 목록은 `git difftool --tool-help`로 확인할 수 있음

### Committing Your Changes

> [!quote]
> Now that your staging area is set up the way you want it, you can commit your changes.

*staging area*가 원하는 상태로 준비되었다면 이제 변경사항을 commit할 수 있음

> [!quote]
>
> Remember that anything that is still unstaged — any files you have created or modified that you haven’t run `git add` on since you edited them — won’t go into this commit. They will stay as modified files on your disk.

주의할 점은 commit 대상이 *working directory* 전체가 아니라 *staging area*라는 것
- 아직 *unstaged* 상태인 변경은 이번 commit에 들어가지 않음
	- file을 만들거나 수정했지만 아직 `git add`하지 않은 경우
	- `git add` 이후 같은 file을 다시 수정했지만 다시 `git add`하지 않은 경우

이런 변경은 *working directory*에 *modified* 상태로 그대로 남음

> [!quote]
> In this case, let’s say that the last time you ran `git status`, you saw that everything was staged, so you’re ready to commit your changes. The simplest way to commit is to type `git commit`:

앞으로 진행할 논의에서 상황은 마지막으로 `git status`를 실행했을 때 모든 변경이 *staging area*에 올라갔다고 가정

가장 단순한 commit 방법은 `git commit`을 실행하는 것

```zsh
$ git commit
```

`git commit`만 실행하면 Git은 commit message를 입력할 editor를 실행함

> [!note] commit message editor 설정
> commit message editor는 shell의 `EDITOR` environment variable로 결정됨
>
> 보통 `vim`이나 `emacs`가 사용되지만, 원하는 editor로 바꿀 수 있음
>
> 예를 들어 `git config --global core.editor` 설정으로 Git이 사용할 editor를 지정할 수 있음

editor에는 대략 아래와 같은 내용이 열림

```text
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
#	new file:   README
#	modified:   CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```

> [!quote]
> You can see that the default commit message contains the latest output of the `git status` command commented out and one empty line on top. You can remove these comments and type your commit message, or you can leave them there to help you remember what you’re committing.

기본 commit message 화면에는 최근 `git status` output이 주석 형태로 들어 있음
- `#`로 시작하는 line은 commit message에 포함되지 않음
- 위쪽의 빈 줄에 실제 commit message를 작성하면 됨

comment를 지우고 message를 작성해도 되고, 무엇을 commit하는지 확인하기 위해 그대로 두어도 됨

> [!note] `git commit -v`
> commit할 내용을 더 명확히 확인하고 싶으면 `git commit -v`를 사용할 수 있음
>
> `-v` option을 사용하면 commit message editor 안에 diff도 함께 표시됨 (`git diff` 출력과 동일)
>
> 이 diff를 보면서 정확히 어떤 변경을 commit하는지 확인할 수 있음

> [!quote]
> When you exit the editor, Git creates your commit with that commit message (with the comments and diff stripped out).

editor를 저장하고 종료하면 Git은 작성한 message로 commit을 생성함
- 이때 comment와 diff는 commit message에서 제거됨

> [!quote]
> Alternatively, you can type your commit message inline with the commit command by specifying it after a `-m` flag, like this:

commit message를 editor에서 작성하지 않고 command line에서 바로 넘길 수도 있음

이때는 `-m` option을 사용함

```zsh
$ git commit -m "Story 182: fix benchmarks for speed"
[master 463dc4f] Story 182: fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```

> [!quote]
> Now you’ve created your first commit! You can see that the commit has given you some output about itself: which branch you committed to (`master`), what SHA-1 checksum the commit has (`463dc4f`), how many files were changed, and statistics about lines added and removed in the commit.

commit이 생성되면 Git은 방금 만든 commit에 대한 정보를 출력함 ^commit-output-summary
- commit한 branch
	- 예시에서는 `master`
- commit의 SHA-1 checksum 일부
	- 예시에서는 `463dc4f`
- 변경된 file 수
- 추가/삭제된 line 통계
- 새로 생성된 file mode
	- 예시에서는 `README` create mode

> [!quote]
> Remember that the commit records the snapshot you set up in your staging area. Anything you didn’t stage is still sitting there modified; you can do another commit to add it to your history. Every time you perform a commit, you’re recording a snapshot of your project that you can revert to or compare to later.

commit은 *staging area*에 준비해둔 snapshot을 기록함
- *staging area*에 올라가지 않은 변경은 commit에 들어가지 않고 *modified* 상태로 남음
- *staging area*에 올라가지 않은 변경을 history에 추가하려면 나중에 다시 *staging area*에 올리고 commit하면 됨
	- `git add`로 *staging area*에 올릴 수 있음

commit을 할 때마다 나중에 되돌아가거나 비교할 수 있는 project snapshot이 하나씩 기록됨

> [!summary] commit
> commit은 *staging area*에 있는 snapshot을 repository history에 기록하는 작업
>
> *unstaged* change는 commit에 포함되지 않고 *working directory*에 남음
>
> `git commit`은 editor에서 message를 작성하고, `git commit -m "message"`는 command line에서 message를 바로 전달함

### Skipping the Staging Area

> [!quote]
> Although it can be amazingly useful for crafting commits exactly how you want them, the staging area is sometimes a bit more complex than you need in your workflow. If you want to skip the staging area, Git provides a simple shortcut.

Git은 *staging area*를 건너뛰고 커밋하는 shortcut을 제공함

> [!quote]
> Adding the `-a` option to the `git commit` command makes Git automatically stage every file that is already tracked before doing the commit, letting you skip the `git add` part:

`git commit`에 `-a` option을 붙이면 이미 *tracked* 상태인 file의 변경을 자동으로 staging함 ^git-commit-a-auto-stage
- 즉 `git add`를 따로 실행하지 않고 commit할 수 있음

```zsh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git commit -a -m 'Add new benchmarks'
[master 83e38c7] Add new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)
```

위 예시에서는 `CONTRIBUTING.md`가 이미 *tracked* file임

따라서 `git commit -a -m 'Add new benchmarks'`를 실행하면 Git이 `CONTRIBUTING.md`의 변경을 자동으로 *staging area*에 올린 뒤 commit함

따로 아래 명령을 실행하지 않아도 됨

```zsh
$ git add CONTRIBUTING.md
```

> [!quote]
> Notice how you don’t have to run `git add` on the `CONTRIBUTING.md` file in this case before you commit. That’s because the `-a` flag includes all changed files. This is convenient, but be careful; sometimes this flag will cause you to include unwanted changes.

편리하지만 주의해야 함
- 의도하지 않은 *tracked* 상태를 가진 file의 변경까지 함께 commit될 수 있음
- *untracked* 상태인 file은 자동으로 포함되지 않음
	- 새 file은 여전히 `git add`로 tracking을 시작해야 함

> [!summary] `git commit -a`
> `git commit -a`는 commit 전에 변경된 *tracked* file을 자동으로 staging함
>
> 새 *untracked* file은 포함하지 않음
>
> 편리하지만 원하지 않는 *tracked* change까지 commit에 들어갈 수 있으므로 commit 전에 `git status`나 `git diff`로 확인하는 것이 좋음

### Removing Files

> [!quote]
> To remove a file from Git, you have to remove it from your tracked files (more accurately, remove it from your staging area) and then commit.

Git에서 file을 제거하려면 tracking 대상에서 제거하고 commit해야 함
- 제거 대상이라는 change를 *staging area*에 올리고 commit하는 것

> [!quote]
>
> The `git rm` command does that, and also removes the file from your working directory so you don’t see it as an untracked file the next time around.

`git rm`은 다음을 한 번에 처리함 ^git-rm
1. file을 tracking 대상에서 제거하는 change를 *staging area*에 올림
2. *working directory*에서도 file을 삭제함
	- `git rm` 이후 `git status`에서 같은 file이 *untracked* 상태로 표시되지 않음
		- *working directory*에서 지움

> [!quote]
> If you simply remove the file from your working directory, it shows up under the “Changes not staged for commit” (that is, unstaged) area of your `git status` output:

*working directory*에서 바로 file만 삭제하면, 삭제 사실(change)은 아직 *staged*되지 않은 상태로 남음
- `git rm`의 2번 동작만 직접 수행한 것과 동일함

즉 `git status`에서는 `Changes not staged for commit` 아래에 표시됨

```zsh
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

위 상태는 *working directory*에서는 `PROJECTS.md`가 삭제되었지만, 삭제 change가 아직 *staging area*에 올라가지 않았다는 뜻

> [!quote]
> Then, if you run `git rm`, it stages the file’s removal:

이후 `git rm`을 실행하면 file 삭제라는 변화가 *staging area*에 올라감
- 1번 동작 수행
	- 2번 동작은 이미 했으니 생략해도 괜찮음

```zsh
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md
```

이제 `PROJECTS.md` 삭제라는 변화는 다음 commit에 들어갈 예정

> [!quote]
> The next time you commit, the file will be gone and no longer tracked.

다음 commit을 하면 `PROJECTS.md`는 repository snapshot에서 사라짐

그리고 더 이상 Git이 tracking하는 file이 아니게 됨

> [!quote]
> If you modified the file or had already added it to the staging area, you must force the removal with the `-f` option. This is a safety feature to prevent accidental removal of data that hasn’t yet been recorded in a snapshot and that can’t be recovered from Git.

`git rm -f` option이 필요한 경우는 다음과 같음
- *modified* 상태의 file을 대상으로 하는 경우
- 이미 *staging area*에 올라간 file을 대상으로 하는 경우

참고로 위에서 본 `rm 'PROJECT.md'`의 예시는 *unmodified* 상태의 file을 대상으로 하는 경우
- `-f`옵션이 필요한 경우에 해당되지 않음

#### *modified* 상태의 파일을 제거하려고 시도하는 경우

```zsh
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   README

no changes added to commit (use "git add" and/or "git commit -a")
$ git rm README
error: the following file has local modifications:
    README
(use --cached to keep the file, or -f to force removal)
$ git rm -f README
rm 'README'
% git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    README
```

이번 수정 사항 변화는 *staging area*에 올라가지 않음
- 이미 tracking된 변화까지는 commit되어 있는 상태

지금 *working directory*에서 수정한 변화가 snapshot으로 기록되지 않고, *working directory*에서 완전히 사라질 수 있기에 Git에서 안전 장치로 막아줌

#### *staged* 상태의 파일을 제거하려고 시도하는 경우

```zsh
$ touch 123
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    README

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        123
$ git add 123
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   123
        deleted:    README
$ git rm 123
error: the following file has changes staged in the index:
    123
(use --cached to keep the file, or -f to force removal)
$ git rm -f 123
rm '123'
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    README
```

마찬가지로 snapshot으로 기록되지 않은 변화(*staging area*에 올라온 새 file)가 *working directory*에서 사라질 수 있기에 Git에서 안전 장치로 막아줌

> [!warning] `git rm -f`
> `-f`는 force removal을 의미함
>
> 아직 snapshot에 기록되지 않은 data를 실수로 삭제하면 Git으로 복구할 수 없을 수 있음
>
> 그래서 Git은 *modified* file이나 *staged* file을 그냥 제거하지 않고 명시적인 force option을 요구함

> [!quote]
> Another useful thing you may want to do is to keep the file in your working tree but remove it from your staging area. In other words, you may want to keep the file on your hard drive but not have Git track it anymore.

file은 *working tree*에 남겨두고 Git의 tracking에서 제외하고 싶을 때도 있음
- [[#^git-rm]]은 기본적으로 *working tree*에서 file을 삭제하기 때문

> [!quote]
> This is particularly useful if you forgot to add something to your `.gitignore` file and accidentally staged it, like a large log file or a bunch of `.a` compiled files. To do this, use the `--cached` option:

이 상황은 `.gitignore`에 넣었어야 할 file을 실수로 staging했을 때 유용함

이때는 `--cached` option을 사용함

```zsh
$ git rm --cached README
```

`git rm --cached README`는 `README`를 *working tree*에는 남겨두고 Git tracking 대상에서 제거함

> [!quote]
> You can pass files, directories, and file-glob patterns to the `git rm` command. That means you can do things such as:

`git rm`에는 file, directory, file-glob pattern을 넘길 수 있음

예를 들어 아래처럼 실행할 수 있음

```zsh
$ git rm log/\*.log
```

> [!quote]
> Note the backslash (`\`) in front of the `*`. This is necessary because Git does its own filename expansion in addition to your shell’s filename expansion. This command removes all files that have the `.log` extension in the `log/` directory.

`*` 앞의 backslash(`\`)에 주의해야 함
- backslash는 shell이 먼저 `*.log`를 expand하지 않도록 막음

shell이 먼저 **expand**하면 다음과 같은 문제가 발생
- shell은 파일 시스템만 알 수 있음
	- Git *tracked*/*untracked* 모름
- shell 구현체마다 glob 동작이 다를 수 있음
	- Git이 자체 **filename expansion**을 수행하기에 오직 Git의 *filename expansion*만 동작하도록 막음

위에서 본 [[#Ignoring Files]]에서는 이스케이프(backslash)처리가 필요 없음
- 이 파일은 shell을 거치지 않고 Git이 바로 읽기 때문
- shell에서 Git command를 입력할 때는 shell의 command 처리 과정을 거침

위 명령은 `log/` directory 안의 `.log` file들을 제거함

```zsh
$ git rm \*~
```

이 명령은 이름이 `~`로 끝나는 모든 file을 제거함

> [!summary] git rm
> `git rm`은 *working directory*에서 file을 삭제하고, 그 제거 변화를 *staging area*에 올림
>
> `git rm --cached`는 *working directory*의 file은 유지하고 Git tracking에서만 제거함
>
> *modified* file이나 *staged* file을 제거하려면 `git rm -f`가 필요할 수 있음

### Moving Files

> [!quote]
> If you rename a file in Git, no metadata is stored in Git that tells it you renamed the file.

rename 정보 자체가 Git object에 따로 저장되는 것은 아님

Git에서 file 이름을 바꾸고 싶다면 다음 command를 사용할 수 있음

```zsh
$ git mv file_from file_to
```

이 command는 file system의 file 이름을 바꾸고, 그 rename처럼 보이는 변화를 *staging area*에 올림

예를 들어 `README.md`를 `README`로 바꾸면 다음과 같음

```zsh
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
```

`renamed: README.md -> README`는 Git이 *staging area*에 올라온 변화를 rename으로 인식했다는 뜻
- old path 제거라는 변화와 new path 추가라는 변화가 *staging area*에 있고 Git이 두 file content의 유사성을 보고 rename으로 보여주는 것
- `git mv`가 special rename metadata를 저장했기 때문이 아님

사실 `git mv`는 다음 3개 명령을 순서대로 처리한 것과 동일함

```zsh
$ mv README.md README
$ git rm README.md
$ git add README
```

정리하면 `git mv`가 처리하는 작업은 다음과 같음
1. file system에서 `README.md`를 `README`로 rename함
2. old path인 `README.md` removal을 *staging area*에 올림
3. new path인 `README` add를 *staging area*에 올림

> [!summary] git mv
> Git은 rename metadata를 따로 저장하지 않음
>
> `git mv`는 `mv + git rm + git add`를 한 번에 수행하는 shortcut임
>
> 다른 tool로 파일 시스템에서 rename해도 commit 전에 `git rm`/`git add` 상태만 맞추면 `git mv`와 동일한 효과
