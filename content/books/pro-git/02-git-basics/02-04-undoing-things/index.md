---
tags:
  - books
  - pro-git
aliases:
  - 2.4 Git Basics - Undoing Things
references:
  - https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things
---
# 2.4 Git Basics - Undoing Things

## Undoing Things

> [!note] 이 절의 명령은 잘못 쓰면 ==복구가 불가능==할 수 있으므로 주의가 필요함

> [!quote]
> 
> One of the common undos takes place when you commit too early and possibly forget to add some files, or you mess up your commit message. If you want to redo that commit, make the additional changes you forgot, stage them, and commit again using the `--amend` option:
> 
> ```
> $ git commit --amend
> ```

흔한 *undo* 시나리오
- 너무 일찍 *commit*해서 일부 파일을 빠뜨림
- *commit message*를 잘못 작성함

해결: 빠뜨린 변경을 *staging*한 뒤 `--amend` 옵션으로 다시 *commit*하면 직전 *commit*을 ==덮어씀==

> [!quote]
> 
> This command takes your staging area and uses it for the commit. If you've made no changes since your last commit (for instance, you run this command immediately after your previous commit), then your snapshot will look exactly the same, and all you'll change is your commit message.

`git commit --amend`는 ==현재 staging area의 상태==로 직전 *commit*을 다시 작성함
- 직전 *commit* 이후 변경한 게 없다면
	- *snapshot*은 그대로 유지됨
	- *commit message*만 바뀜
	- 예를 들어 `git commit` 이후 `git commit --amend`를 바로 실행하는 경우

> [!quote]
> 
> The same commit-message editor fires up, but it already contains the message of your previous commit. You can edit the message the same as always, but it overwrites your previous commit.

`--amend` 실행 시 평소처럼 *commit message* 편집기가 열리되, ==직전 commit의 메시지가 미리 채워져== 있음
- 그 상태에서 수정·저장하면 직전 *commit*이 새 *commit*으로 ==덮어써짐==

> [!quote]
> 
> As an example, if you commit and then realize you forgot to stage the changes in a file you wanted to add to this commit, you can do something like this:
> 
> ```
> $ git commit -m 'Initial commit'
> $ git add forgotten_file
> $ git commit --amend
> ```
> 
> You end up with a single commit — the second commit replaces the results of the first.

빠뜨린 파일을 추가하는 시나리오 예시
- 첫 *commit* 직후 빠뜨린 파일을 *staging*에 추가
- `git commit --amend`로 다시 *commit*하면 ==두 번째 commit이 첫 번째를 대체==해서 ==최종 결과는 commit 하나==가 됨

> [!quote] Note
> 
> It's important to understand that when you're amending your last commit, you're not so much fixing it as replacing it entirely with a new, improved commit that pushes the old commit out of the way and puts the new commit in its place. Effectively, it's as if the previous commit never happened, and it won't show up in your repository history.

`--amend`는 "수정"이 아니라 ==완전한 교체==임
- 이전 *commit*을 밀어내고 새 *commit*을 그 자리에 ==놓는 동작==
- 결과적으로 이전 *commit*은 ==처음부터 없었던 것처럼== *history*에 나타나지 않음

"not so much A as B": A라기보다는 오히려 B

> [!quote]
> 
> The obvious value to amending commits is to make minor improvements to your last commit, without cluttering your repository history with commit messages of the form, "Oops, forgot to add a file" or "Darn, fixing a typo in last commit".

`--amend`의 가치
- 직전 *commit*에 사소한 보완을 더할 때, "Oops, forgot to add a file" 같은 ==잡음 commit message로 history가 지저분해지는 것을 방지==

> [!quote] Note
> 
> Only amend commits that are still local and have not been pushed somewhere. Amending previously pushed commits and force pushing the branch will cause problems for your collaborators. For more on what happens when you do this and how to recover if you're on the receiving end read [The Perils of Rebasing](https://git-scm.com/book/en/v2/ch00/_rebase_peril).

`--amend`는 ==아직 push하지 않은 로컬 commit==에만 사용하기를 권함
- 이미 원격에 올라간 *commit*을 *amend* + force push하면 협업자에게 문제가 발생함
- 자세한 내용·복구 방법은 *The Perils of Rebasing* 절에서 다룸

> [!TODO] The Perils of Rebasing 문서 링크

### Unstaging a Staged File

> [!quote]
> 
> For example, let's say you've changed two files and want to commit them as two separate changes, but you accidentally type `git add *` and stage them both. How can you unstage one of the two? The `git status` command reminds you:
> 
> ```
> $ git add *
> $ git status
> On branch master
> Changes to be committed:
>   (use "git reset HEAD <file>..." to unstage)
> 
>     renamed:    README.md -> README
>     modified:   CONTRIBUTING.md
> ```

두 파일을 따로 *commit*하려 했는데 실수로 `git add *`로 둘 다 *staging*된 상황
- 한쪽만 *unstaging*하고 싶을 때
- `git status` 출력의 안내 문구가 ==그 방법을 직접 알려줌==
	- `(use "git reset HEAD <file>..." to unstage)`

> [!quote]
> 
> Right below the "Changes to be committed" text, it says use `git reset HEAD <file>…` to unstage. So, let's use that advice to unstage the `CONTRIBUTING.md` file:
> 
> ```
> $ git reset HEAD CONTRIBUTING.md
> Unstaged changes after reset:
> M	CONTRIBUTING.md
> $ git status
> On branch master
> Changes to be committed:
>   (use "git reset HEAD <file>..." to unstage)
> 
>     renamed:    README.md -> README
> 
> Changes not staged for commit:
>   (use "git add <file>..." to update what will be committed)
>   (use "git checkout -- <file>..." to discard changes in working directory)
> 
>     modified:   CONTRIBUTING.md
> ```
> 
> The command is a bit strange, but it works. The `CONTRIBUTING.md` file is modified but once again unstaged.

안내대로 `git reset HEAD CONTRIBUTING.md` 실행
- 대상 파일이 *staging area*에서 빠지고 ==modified 상태로 되돌아감==
- `git status`에서 해당 파일이 `Changes to be committed`가 아닌 `Changes not staged for commit`로 이동한 것을 확인 가능
- 다른 *staged* 파일(`README` rename)은 ==영향 없이 그대로== 남음

`git reset` 출력의 `M	CONTRIBUTING.md`는 [[books/pro-git/02-git-basics/02-02-recording-changes-to-the-repository/index#Short Status|short status]] 형식과 같음
- 왼쪽 column 비어있고 오른쪽이 `M` → *working tree*만 *modified*, *staging area*는 비어있음을 의미

> [!quote] Note
> 
> It's true that `git reset` can be a dangerous command, especially if you provide the `--hard` flag. However, in the scenario described above, the file in your working directory is not touched, so it's relatively safe.

`git reset`은 `--hard` ==플래그를 붙이면 위험==한 명령
- 다만 위 시나리오처럼 ==플래그 없이 경로 인자만== 주는 경우엔 ==working directory를 건드리지 않으므로== 비교적 안전

> [!quote]
> 
> For now this magic invocation is all you need to know about the `git reset` command. We'll go into much more detail about what reset does and how to master it to do really interesting things in [Reset Demystified](https://git-scm.com/book/en/v2/ch00/_git_reset).

지금 단계에서 `git reset`에 대해 알아야 할 것은 ==unstaging 한 줄 사용법==뿐
- `git reset`의 전반적인 동작과 활용은 *Reset Demystified* 절에서 본격적으로 다룸

> [!TODO] Reset Demystified 문서 링크

### Unmodifying a Modified File

> [!quote]
> 
> What if you realize that you don't want to keep your changes to the `CONTRIBUTING.md` file? How can you easily unmodify it — revert it back to what it looked like when you last committed (or initially cloned, or however you got it into your working directory)? Luckily, `git status` tells you how to do that, too. In the last example output, the unstaged area looks like this:
> 
> ```
> Changes not staged for commit:
>   (use "git add <file>..." to update what will be committed)
>   (use "git checkout -- <file>..." to discard changes in working directory)
> 
>     modified:   CONTRIBUTING.md
> ```

수정한 파일의 변경을 ==통째로 버리고 마지막 commit 시점의 상태로 되돌리는== 시나리오
- 기준이 되는 "원래 상태"
	- 마지막 *commit*
	- 또는 처음 *clone*했을 때
	- 또는 *working directory*에 들어온 시점의 모습
- 이 경우에도 `git status` 출력이 ==방법을 직접 알려줌==
	- `(use "git checkout -- <file>..." to discard changes in working directory)`

> [!quote]
> 
> It tells you pretty explicitly how to discard the changes you've made. Let's do what it says:
> 
> ```
> $ git checkout -- CONTRIBUTING.md
> $ git status
> On branch master
> Changes to be committed:
>   (use "git reset HEAD <file>..." to unstage)
> 
>     renamed:    README.md -> README
> ```
> 
> You can see that the changes have been reverted.

`git checkout -- CONTRIBUTING.md` 실행 결과는 다음과 같음
- `git status` 출력에서 `CONTRIBUTING.md`의 *modified* 항목이 사라짐
- 파일이 ==마지막 commit 시점의 내용으로 되돌아옴== 
- ==working directory의 로컬 변경도 사라짐==

> [!quote] Important
> 
> It's important to understand that `git checkout -- <file>` is a dangerous command. Any local changes you made to that file are gone — Git just replaced that file with the last staged or committed version. Don't ever use this command unless you absolutely know that you don't want those unsaved local changes.

`git checkout -- <file>`은 ==위험한 명령==
- *working directory*의 로컬 변경이 ==완전히 사라짐==
	- Git이 파일을 마지막 *staged* 또는 *committed* 버전으로 ==덮어씀==
- 그 변경을 정말 버려도 된다고 ==확신할 때만== 사용

> [!quote]
> 
> If you would like to keep the changes you've made to that file but still need to get it out of the way for now, we'll go over stashing and branching in [Git Branching](https://git-scm.com/book/en/v2/ch00/ch03-git-branching); these are generally better ways to go.

==변경은 보존하면서== 잠시 치워두고 싶다면 `git checkout --` 대신 다른 방법을 사용
- *stashing*
- *branching*
- 두 기법 모두 *Git Branching* 챕터에서 다룸 — 변경을 잃지 않으려는 경우엔 ==이쪽이 더 안전한 선택==

> [!TODO] Git Branching 문서 링크

> [!quote]
> 
> Remember, anything that is committed in Git can almost always be recovered. Even commits that were on branches that were deleted or commits that were overwritten with an `--amend` commit can be recovered (see [Data Recovery](https://git-scm.com/book/en/v2/ch00/_data_recovery) for data recovery). However, anything you lose that was never committed is likely never to be seen again.

==한 번이라도 commit된 것==은 Git에서 거의 항상 복구 가능
- 삭제된 *branch* 위에 있던 *commit*도 복구 가능
- `--amend`로 덮어쓴 *commit*도 복구 가능
- 복구 방법은 *Data Recovery* 절에서 다룸

반면 ==한 번도 commit되지 않은 변경==은 잃어버리면 ==사실상 복구 불가==

> [!TODO] Data Recovery 문서 링크

### Undoing things with git restore

> [!quote]
> 
> The next two sections demonstrate how to work with your staging area and working directory changes with `git restore`. The nice part is that the command you use to determine the state of those two areas also reminds you how to undo changes to them. For example, let's say you've changed two files and want to commit them as two separate changes, but you accidentally type `git add *` and stage them both. How can you unstage one of the two? The `git status` command reminds you:
> 
> ```
> $ git add *
> $ git status
> On branch master
> Changes to be committed:
>   (use "git restore --staged <file>..." to unstage)
>     modified:   CONTRIBUTING.md
>     renamed:    README.md -> README
> ```

Git version 2.23부터 *staging area*·*working directory*의 변경을 `git restore`로 ==다루는 흐름== 소개
- 앞의 `git reset HEAD`([[#Unstaging a Staged File]]) / `git checkout -- <file>`([[#Unmodifying a Modified File]]) 시나리오와 ==동일한 상황을== `git restore`로 푸는 방식
- 여기서도 `git status`가 ==사용해야 할 명령을 직접 안내==
	- `(use "git restore --staged <file>..." to unstage)`

> [!quote]
> 
> Right below the "Changes to be committed" text, it says use `git restore --staged <file>…` to unstage. So, let's use that advice to unstage the `CONTRIBUTING.md` file:
> 
> ```
> $ git restore --staged CONTRIBUTING.md
> $ git status
> On branch master
> Changes to be committed:
>   (use "git restore --staged <file>..." to unstage)
>     renamed:    README.md -> README
> 
> Changes not staged for commit:
>   (use "git add <file>..." to update what will be committed)
>   (use "git restore <file>..." to discard changes in working directory)
>     modified:   CONTRIBUTING.md
> ```
> 
> The `CONTRIBUTING.md` file is modified but once again unstaged.

안내대로 `git restore --staged CONTRIBUTING.md` 실행
- 대상 파일이 *staging area*에서 빠지고 ==modified 상태로 되돌아감==
- 결과는 앞서 본 `git reset HEAD CONTRIBUTING.md`와 ==동일==
	- [[#Unstaging a Staged File]]
- `git status`는 다음 단계로 `git restore <file>`을 ==안내== (변경 폐기용)
	- `(use "git restore <file>..." to discard changes in working directory)`
		- 이는 [[#Unmodifying a Modified File]] 와 동일한 동작
		- 바로 아래 섹션에서 다룸

#### Unmodifying a Modified File with git restore

> [!quote]
> 
> What if you realize that you don't want to keep your changes to the `CONTRIBUTING.md` file? How can you easily unmodify it — revert it back to what it looked like when you last committed (or initially cloned, or however you got it into your working directory)? Luckily, `git status` tells you how to do that, too. In the last example output, the unstaged area looks like this:
> 
> ```
> Changes not staged for commit:
>   (use "git add <file>..." to update what will be committed)
>   (use "git restore <file>..." to discard changes in working directory)
>     modified:   CONTRIBUTING.md
> ```

수정한 파일의 변경을 ==통째로 버리고 원래 상태로 되돌리는== 시나리오 — `git restore` 버젼
- 기준이 되는 "원래 상태"
	- 마지막 *commit*
	- 또는 처음 *clone*했을 때
	- 또는 *working directory*에 들어온 시점의 모습
- 앞서 본 [[#Unmodifying a Modified File]]의 `git checkout -- <file>`을 `git restore <file>`이 ==대체==
- `git status` 출력이 사용해야 할 명령을 ==직접 안내==
	- `(use "git restore <file>..." to discard changes in working directory)`

> [!quote]
> 
> It tells you pretty explicitly how to discard the changes you've made. Let's do what it says:
> 
> ```
> $ git restore CONTRIBUTING.md
> $ git status
> On branch master
> Changes to be committed:
>   (use "git restore --staged <file>..." to unstage)
>     renamed:    README.md -> README
> ```

안내대로 `git restore CONTRIBUTING.md` 실행
- `git status` 출력에서 `CONTRIBUTING.md`의 *modified* 항목이 ==사라짐==
- 파일이 ==마지막 commit 시점의 내용으로 되돌아옴==
- 결과는 앞서 본 `git checkout -- CONTRIBUTING.md`와 ==동일==
	- [[#Unmodifying a Modified File]]

> [!quote] Important
> 
> It's important to understand that `git restore <file>` is a dangerous command. Any local changes you made to that file are gone — Git just replaced that file with the last staged or committed version. Don't ever use this command unless you absolutely know that you don't want those unsaved local changes.

`git restore <file>`은 ==위험한 명령==
- *working directory*의 로컬 변경이 ==완전히 사라짐==
	- Git이 파일을 마지막 *staged* 또는 *committed* 버전으로 ==덮어씀==
- 그 변경을 정말 버려도 된다고 ==확신할 때만== 사용
- 앞서 본 `git checkout -- <file>`의 [[#Unmodifying a Modified File|동일한 경고]]가 그대로 적용됨
