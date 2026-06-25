---
tags:
  - books
  - pro-git
aliases:
  - 3.6 Git Branching - Rebasing
references:
  - https://git-scm.com/book/en/v2/Git-Branching-Rebasing
---
# 3.6 Git Branching - Rebasing

## Rebasing

### The Basic Rebase

> [!quote]
>
> If you go back to an earlier example from Basic Merging, you can see that you diverged your work and made commits on two different branches.

작업이 갈라져 서로 다른 두 branch에 각각 commit이 쌓인 상황에서 시작
- [[books/pro-git/03-git-branching/03-02-basic-branching-and-merging/index#Basic Merging|Basic Merging]]의 예시처럼
![[rebasing-001.png]]

> [!quote]
>
> The easiest way to integrate the branches, as we’ve already covered, is the merge command. It performs a three-way merge between the two latest branch snapshots (C3 and C4) and the most recent common ancestor of the two (C2), creating a new snapshot (and commit).

이미 다룬 것처럼 branch를 통합하는 가장 쉬운 방법은 `merge` 명령임 ^merge-example
- 두 branch의 최신 snapshot(`C3`, `C4`)과 둘의 공통 조상(`C2`) 사이에서 ==three-way merge==를 수행함
- 그 결과 ==새 snapshot(commit)==을 하나 만듦

![[rebasing-002.png]]

> [!quote]
>
> However, there is another way: you can take the patch of the change that was introduced in C4 and reapply it on top of C3. In Git, this is called rebasing. With the rebase command, you can take all the changes that were committed on one branch and replay them on a different branch.

하지만 통합에는 또 다른 방법이 있음
- `C4`에서 도입된 변경의 patch만 떼어내 `C3` 위에 ==다시 적용==할 수 있음
- Git에서는 이 방식을 **rebase**라고 부름

`rebase` 명령은 한 branch에 commit된 ==모든 변경을 다른 branch 위에 replay==함

> [!quote]
>
> For this example, you would check out the experiment branch, and then rebase it onto the master branch as follows:
>
> ```zsh
> $ git checkout experiment
> $ git rebase master
> First, rewinding head to replay your work on top of it...
> Applying: added staged command
> ```
>
> This operation works by going to the common ancestor of the two branches (the one you’re on and the one you’re rebasing onto), getting the diff introduced by each commit of the branch you’re on, saving those diffs to temporary files, resetting the current branch to the same commit as the branch you are rebasing onto, and finally applying each change in turn.

예시에서는 `experiment`를 checkout한 뒤 `master` 위로 rebase함
- `git checkout experiment`로 rebase할 branch로 이동
- `git rebase master`로 `experiment`의 작업을 `master` 위에 다시 얹음

`rebase`가 내부에서 처리하는 동작은 다음 순서를 따름
- 두 branch(현재 branch와 rebase 대상 branch)의 ==공통 조상==으로 이동함
- 현재 branch의 ==각 commit이 도입한 diff==를 구해 임시 파일에 저장함
- 현재 branch를 rebase 대상 branch와 ==같은 commit으로 reset==함
- 저장해 둔 변경을 ==차례대로 하나씩 적용==함

![[rebasing-003.png]]

> [!quote]
>
> At this point, you can go back to the master branch and do a fast-forward merge.
>
> ```zsh
> $ git checkout master
> $ git merge experiment
> ```

이 시점에서 `master`로 돌아가 ==fast-forward merge==를 하면 됨
- `git checkout master`로 `master`로 이동
- `git merge experiment`로 *fast-forward merge*
	-  `master`가 `experiment`의 자손이므로 새 merge commit 없이 pointer만 앞으로 이동함

![[rebasing-004.png]]

> [!quote]
>
> Now, the snapshot pointed to by C4' is exactly the same as the one that was pointed to by C5 in the merge example. There is no difference in the end product of the integration, but rebasing makes for a cleaner history. If you examine the log of a rebased branch, it looks like a linear history: it appears that all the work happened in series, even when it originally happened in parallel.

rebase로 만든 최종 snapshot은 merge 결과와 ==완전히 같음==
- `C4`가 가리키는 snapshot은 [[#^merge-example|merge 예시]]의 `C5`가 가리키던 것과 정확히 동일함
- 통합의 ==최종 결과물에는 차이가 없음==

차이는 history의 모양에 있음
- rebase는 history를 ==더 깔끔하게== 만듦
- rebased branch의 log를 보면 ==선형(linear) history==처럼 보임
- ==원래 병렬==로 진행된 작업도 마치 ==순차적으로 일어난 것처럼== 나타남

> [!quote]
>
> Often, you’ll do this to make sure your commits apply cleanly on a remote branch — perhaps in a project to which you’re trying to contribute but that you don’t maintain. In this case, you’d do your work in a branch and then rebase your work onto origin/master when you were ready to submit your patches to the main project. That way, the maintainer doesn’t have to do any integration work — just a fast-forward or a clean apply.

rebase를 쓰는 흔한 이유는 내 commit이 ==remote branch에 깔끔하게 적용되도록== 하기 위함임

예를 들어 내가 maintainer는 아니지만 patch로 기여하려는 project
- 이때 작업 흐름은 다음과 같음
	- 별도 branch에서 작업함
	- main project에 patch를 제출할 준비가 되면 내 작업을 `origin/master` 위로 rebase함
	- maintainer는 별도 통합 작업이 필요 없음
		- ==fast-forward 또는 clean apply==만 하면 됨

> [!quote]
>
> Note that the snapshot pointed to by the final commit you end up with, whether it’s the last of the rebased commits for a rebase or the final merge commit after a merge, is the same snapshot — it’s only the history that is different. Rebasing replays changes from one line of work onto another in the order they were introduced, whereas merging takes the endpoints and merges them together.

어느 방식이든 최종 commit이 가리키는 ==snapshot은 동일==함
- rebase의 마지막 rebased commit이든, merge 후의 merge commit이든 결과 snapshot은 같음
- 달라지는 것은 ==history뿐==임

같은 결과를 만드는 두 방식의 차이는 진행 방향에 있음
- rebase: 한 작업 흐름의 변경을 ==도입된 순서대로== 다른 흐름 위에 replay함
- merge: 두 흐름의 ==endpoint를 가져와 하나로 합침==

### More Interesting Rebases

> [!quote]
>
> You can also have your rebase replay on something other than the rebase target branch. Take a history like A history with a topic branch off another topic branch, for example. You branched a topic branch (server) to add some server-side functionality to your project, and made a commit. Then, you branched off that to make the client-side changes (client) and committed a few times. Finally, you went back to your server branch and did a few more commits.

rebase는 평소의 **rebase target branch**가 ==아닌 다른 base==(목적지) 위에도 replay할 수 있음

> [!note] rebase의 출발지와 목적지
>
> rebase에는 분리될 수 있는 두 역할이 있음
> - ==출발지==: 어디 이후의 commit을 가져올지 자르는 기준 (branch가 갈라져 나온 지점)
> - ==목적지==: 가져온 commit을 새로 얹는 토대 (Git 용어로 **new base**)
>
> 기본 rebase는 한 branch가 두 역할을 겸함
> - `experiment`에서 `git rebase master`를 실행하면 `master`가 출발지이자 목적지임
>   - `master`와 `experiment`의 공통 조상 이후 commit을 골라 `master` 끝에 얹음
> - 그래서 평소에는 둘을 구분할 일이 없음
>
> `--onto`는 이 ==출발지와 목적지를 따로 지정==함
> - 책에서 말하는 "rebase target branch"는 평소 `git rebase <X>`의 `<X>` 자리(출발지 겸 목적지)를 가리킴
> - `--onto`를 쓰면 출발지는 그대로 두고 ==목적지만 다른 base로== 바꿔 replay할 수 있음

예를 들어 다음 history를 가정함
- server-side 기능을 추가하려고 topic branch `server`를 분기해 commit 하나(C3)를 만듦
- 그 `server`에서 다시 분기해 client-side 변경용 `client` branch를 만들고 commit을 몇 번 함
	- C8, C9
- 다시 `server`로 돌아와 commit을 몇 개 더 함
	- C4, C10

![[rebasing-005.png]]

> [!quote]
>
> ```zsh
> $ git rebase --onto master server client
> ```
>
> This basically says, “Take the client branch, figure out the patches since it diverged from the server branch, and replay these patches in the client branch as if it was based directly off the master branch instead.” It’s a bit complex, but the result is pretty cool.

`git rebase --onto master server client`는 출발지와 목적지를 분리해 지정함
- `--onto master`: patch를 얹을 ==목적지== (새 base)
- `server`: ==출발지== — `server`와 `client`의 공통 조상 이후 commit만 가져옴
	- 즉 `server`까지 공통인 commit은 ==제외==함
- `client`: 실제로 옮길 branch

즉 이 명령의 의미는 다음과 같음
- `client`가 `server`와 갈라진 이후의 patch(C8, C9)만 골라냄
- 마치 `client`가 처음부터 `master`에서 직접 분기한 것처럼 그 patch들을 `master` 위에 replay함

![[rebasing-006.png]]

> [!quote]
>
> Now you can fast-forward your master branch
>
> ```zsh
> $ git checkout master
> $ git merge client
> ```

이제 `master`를 ==fast-forward==할 수 있음
- `git checkout master`로 `master`로 이동
- `git merge client`로 합침 — `client`(C8', C9')가 `master`의 자손이 됐으므로 pointer만 앞으로 이동함

![[rebasing-007.png]]

> [!quote]
>
> Let’s say you decide to pull in your server branch as well. You can rebase the server branch onto the master branch without having to check it out first by running `git rebase <basebranch> <topicbranch>` — which checks out the topic branch (in this case, server) for you and replays it onto the base branch (master):
>
> ```zsh
> $ git rebase master server
> ```
>
> This replays your server work on top of your master work, as shown in Rebasing your server branch on top of your master branch.

이번에는 `server` branch의 작업도 `master`에 통합하기로 함
- `server`를 ==직접 checkout하지 않고== `master` 위로 rebase할 수 있음
- `git rebase <basebranch> <topicbranch>` 형식을 사용

`git rebase master server`가 처리하는 일은 다음과 같음
- topic branch(`server`)를 ==대신 checkout==해 줌
- 그 작업을 base branch(`master`) 위에 replay함

"as shown in Rebasing your server branch on top of your master branch." 는 아래 그림을 보라는 내용

![[rebasing-008.png]]

> [!quote]
>
> Then, you can fast-forward the base branch (master):
>
> ```zsh
> $ git checkout master
> $ git merge server
> ```
>
> You can remove the client and server branches because all the work is integrated and you don’t need them anymore, leaving your history for this entire process looking like Final commit history:
>
> ```zsh
> $ git branch -d client
> $ git branch -d server
> ```

이제 base branch(`master`)를 *fast-forward*함
- `git checkout master`로 `master`로 이동
- `git merge server`로 합침 — 마찬가지로 pointer만 앞으로 이동함

모든 작업이 통합됐으므로 더 이상 필요 없는 `client`, `server` branch를 삭제함
- `git branch -d client`
- `git branch -d server`
- 그러면 전체 과정의 history가 Final commit history 그림처럼 ==선형으로== 남음

"Final commit history"는 아래 그림을 지칭함

![[rebasing-009.png]]
