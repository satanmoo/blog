---
tags:
  - books
  - pro-git
---
# 2.2 Git Basics - Recording Changes to the Repository

## Recording Changes to the Repository

> [!quote]
> At this point, you should have a bona fide Git repository on your local machine, and a checkout or working copy of all of its files in front of you. Typically, you’ll want to start making changes and committing snapshots of those changes into your repository each time the project reaches a state you want to record.

작업하기 전 상태는 다음과 같음
- 정상적인 Git repository가 존재함
- 그 repository의 file들이 working copy 형태로 눈앞에 있는 상태
	- [[books/pro-git/01-getting-started/01-03-what-is-git/index#^checkout|checkout]]은 여기서 working copy를 가리키는 표현(checkout의 결과)으로 사용됨

원하는 시점의 작업 결과를 기록하기 위해 스냅샷을 커밋해야 함

> [!quote]
> Remember that each file in your working directory can be in one of two states: tracked or untracked. Tracked files are files that were in the last snapshot, as well as any newly staged files; they can be unmodified, modified, or staged. In short, tracked files are files that Git knows about.

working directory 안의 각 file은 크게 두 상태 중 하나
- tracked
- untracked

tracked file은 Git이 알고 있는 file
- 마지막 commit snapshot에 들어 있던 file
- 새로 staging area에 올린 file

tracked file은 다시 세부 상태를 가질 수 있음 ^tracked-file-states
- unmodified
	- 마지막 commit 이후 변경되지 않은 상태
- modified
	- 마지막 commit 이후 변경되었지만 아직 staging하지 않은 상태
- staged
	- 다음 commit에 들어가도록 staging area에 올려둔 상태

> [!summary] 핵심 구분
> tracked/untracked는 Git이 해당 file을 관리 대상으로 알고 있는지에 대한 구분 
> 
> modified/staged/unmodified는 tracked file 내부의 세부 상태

> [!quote]
> Untracked files are everything else — any files in your working directory that were not in your last snapshot and are not in your staging area. When you first clone a repository, all of your files will be tracked and unmodified because Git just checked them out and you haven’t edited anything.

untracked file은 Git이 아직 모르는 file
- 마지막 commit snapshot에 없었음
- staging area에도 올라가 있지 않음

repository를 처음 clone한 직후에는 모든 file이 tracked이면서 unmodified 상태

> [!quote]
> As you edit files, Git sees them as modified, because you’ve changed them since your last commit. As you work, you selectively stage these modified files and then commit all those staged changes, and the cycle repeats.

file을 수정하면 Git은 해당 file을 modified 상태로 봄
- 여기서 말하는 수정 대상은 이미 tracked 상태인 file
- Git은 다음 근거로 수정된 file을 modified 상태로 봄
	- 마지막 commit snapshot과 현재 working directory의 file 내용이 달라짐
	- 마지막 commit snapshot에서 이미 해당 file을 추적하고 있는 개념

다음의 작업 흐름은 반복됨
- file 수정
	- modified 상태
- 수정한 file 중 다음 commit에 넣을 것만 선택해서 stage
	- staged 상태
- staged changes를 commit
- 다시 file 수정...

![[recording-changes-to-the-repository-001.png]] ^file-status-lifecycle

> [!note]
> 이 그림은 file 상태 전이를 단순화해서 보여주는 참고용 그림
> 
> 여기서는 그림 전체를 완전히 해설하지 않고, 문맥에 필요한 상태 전이만 참조함

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

`working tree clean`은 working directory가 깨끗하다는 뜻
- modified 상태인 tracked file이 없음
- untracked file도 없음

`git status`는 file 상태 외에도 branch 정보를 함께 보여줌
- 현재 branch
	- 예시에서는 `master`
- server의 같은 branch와 diverge되지 않았다는 정보
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
> tracked file의 수정도 없고, 새 untracked file도 보이지 않는 상태

> [!quote]
> Let’s say you add a new file to your project, a simple `README` file. If the file didn’t exist before, and you run `git status`, you see your untracked file like so:

project에 새 file `README`를 추가한다고 가정

이 file이 이전 commit snapshot에 없던 file이라면, `git status`에서 untracked file로 표시됨

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

`untracked`의 의미
- 이전 snapshot(commit)에 없던 file
- 아직 staging area에도 올라가지 않은 file
	- Git이 아직 다음 commit snapshot에 포함할 대상으로 보지 않는 file

Git은 untracked file을 자동으로 commit snapshot에 포함하지 않음
- 실수로 생성된 binary file, build output 등을 commit에 넣는 것을 막기 위함

untracked file을 commit에 포함하고 싶으면 tracking을 시작해야 함

> [!summary] untracked file
> untracked file은 working directory에는 있지만, 이전 commit snapshot에도 없고 staging area에도 없는 file
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

다시 `git status`를 실행하면 `README`가 tracked 상태가 되었고, commit될 대상으로 staged된 것을 볼 수 있음
- `git add` 명령어가 아래 그림에서 "Add the file"에 대응
	- [[#^file-status-lifecycle]]
- `git add`는 파일의 상태를 **staged**로 만듦
	- **tracked** 상태에 포함됨
		- [[#^tracked-file-states]] 참고

```zsh
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README
```

> [!quote]
> You can tell that it’s staged because it’s under the “Changes to be committed” heading. If you commit at this point, the version of the file at the time you ran `git add` is what will be in the subsequent historical snapshot. You may recall that when you ran `git init` earlier, you then ran `git add <files>` — that was to begin tracking files in your directory. The `git add` command takes a path name for either a file or a directory; if it’s a directory, the command adds all the files in that directory recursively.

`git status`에서 `Changes to be committed` 아래에 표시되는 파일(지금 예시에서는 `README`)들은 tracked 상태

이 시점에 commit하면 다음 historical snapshot에는 `git add`를 실행한 시점의 `README` file의 version이 들어감

앞에서 [[books/pro-git/02-git-basics/02-01-getting-a-git-repository/index#^tracking-files-with-git-add|`git init` 이후 `git add`를 실행했던 것]]도 directory 안의 file들을 tracking하기 위한 작업이었음

`git add`는 file path나 directory path를 argument로 받음
- file path를 넘기면 해당 file을 staging함
- directory path를 넘기면 그 directory 안의 file들을 recursively staging함

> [!summary] git add
> `git add`는 untracked file을 tracking하기 시작하게 만들고, 그 file의 현재 상태를 staging area에 올림
> 
> staged된 file은 다음 commit snapshot에 들어갈 후보가 됨

### Staging Modified Files

> [!quote]
> Let’s change a file that was already tracked. If you change a previously tracked file called `CONTRIBUTING.md` and then run your `git status` command again, you get something that looks like this:

이번에는 이미 tracked 상태인 file을 수정한다고 가정

예를 들어 기존에 tracked 상태였던 `CONTRIBUTING.md`를 수정한 뒤 `git status`를 실행하면 아래와 비슷한 결과가 나옴

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
> The `CONTRIBUTING.md` file appears under a section named “Changes not staged for commit” — which means that a file that is tracked has been modified in the working directory but not yet staged. To stage it, you run the `git add` command. `git add` is a multipurpose command — you use it to begin tracking new files, to stage files, and to do other things like marking merge-conflicted files as resolved. It may be helpful to think of it more as “add precisely this content to the next commit” rather than “add this file to the project”. Let’s run `git add` now to stage the `CONTRIBUTING.md` file, and then run `git status` again:

`git status`에서 `CONTRIBUTING.md`는 `Changes not staged for commit` 아래에 표시됨

이는 다음 상태를 의미함
- **modified** 상태
	- `CONTRIBUTING.md`는 tracked file임
	- working directory에서 수정됨
	- 하지만 수정된 현재 내용은 아직 staging area에 올라가지 않음
		- [[#^tracked-file-states]] 참고

이 file을 stage하려면 다시 `git add`를 실행함

> [!summary] git add의 역할
> 
> `git add`는 여러 상황에서 사용되는 명령
> - 새 file을 tracking하기 시작할 때
> - modified file을 staging할 때

> [!tip]
> 
> `git add`는 "file을 project에 추가한다"보다 "이 시점의 정확한 내용을 다음 commit에 추가한다"에 가깝게 이해하는 것이 좋음

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

`git add CONTRIBUTING.md`를 실행한 뒤에는 `CONTRIBUTING.md`도 `Changes to be committed` 아래에 표시됨

이제 다음 commit에 들어갈 후보는 두 file
- `README`
	- 새 file
- `CONTRIBUTING.md`
	- 수정된 tracked file

> [!quote]
> Both files are staged and will go into your next commit. At this point, suppose you remember one little change that you want to make in `CONTRIBUTING.md` before you commit it. You open it again and make that change, and you’re ready to commit. However, let’s run `git status` one more time:

두 file 모두 staged 상태이므로 다음 commit에 들어갈 예정

그런데 commit하기 전에 `CONTRIBUTING.md`를 한 번 더 수정한다고 가정

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
> What the heck? Now `CONTRIBUTING.md` is listed as both staged and unstaged. How is that possible? It turns out that Git stages a file exactly as it is when you run the `git add` command. If you commit now, the version of `CONTRIBUTING.md` as it was when you last ran the `git add` command is how it will go into the commit, not the version of the file as it looks in your working directory when you run `git commit`. If you modify a file after you run `git add`, you have to run `git add` again to stage the latest version of the file:

이제 `CONTRIBUTING.md`가 두 곳에 모두 표시됨
- `Changes to be committed`
- `Changes not staged for commit`

이는 Git이 working directory의 최신 내용을 staging area에 자동 반영하는 것이 아니라, `git add`를 실행한 시점의 file 내용을 staging area에 올리기 때문

현재 상태를 구분하면 다음과 같음
- staging area의 `CONTRIBUTING.md`
	- 마지막으로 `git add CONTRIBUTING.md`를 실행했을 때의 version
- working directory의 `CONTRIBUTING.md`
	- 그 이후 다시 수정한 최신 version

이 상태에서 commit하면 commit에는 마지막 `git add` 시점의 `CONTRIBUTING.md` version이 들어감

> [!note] working directory에 있는 최신 수정 내용까지 commit에 포함하려면 `git add`를 다시 실행해야 함

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

다시 `git add CONTRIBUTING.md`를 실행하면 최신 version의 `CONTRIBUTING.md`가 staging area에 올라감

이제 `CONTRIBUTING.md`는 `Changes not staged for commit`에 표시되지 않음

> [!summary] staged snapshot
> Git은 `git add`를 실행한 순간의 file 내용을 staging area에 기록함
> 
> `git add` 이후 같은 file을 다시 수정하면, staged version과 working directory version이 달라질 수 있음
> 
> 최신 수정까지 다음 commit에 포함하려면 `git add`를 다시 실행해야 함
