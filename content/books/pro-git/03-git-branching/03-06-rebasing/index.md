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
- `C4'`가 가리키는 snapshot은 [[#^merge-example|merge 예시]]의 `C5`가 가리키던 것과 정확히 동일함
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
> - ==출발지==: 어디 이후의 commit을 가져올지 자르는 기준 (HEAD가 가리키는 branch가 갈라져 나온 지점)
> - ==목적지==: 가져온 commit을 새로 얹는 토대 (Git 용어로 **new base**)
>
> 기본 rebase는 한 branch가 두 역할을 겸함
> - `experiment`에서 `git rebase master`를 실행하면 `master`가 출발지이자 목적지임
> 	- `experiment`는 `master`에서 갈라져나왔음
>   - `master`와 `experiment`의 공통 조상 이후 commit을 골라 `master` 끝에 얹음
> - 그래서 평소에는 둘을 구분할 일이 없음
>
> `--onto`는 이 ==출발지와 목적지를 따로 지정==함
> - 책에서 말하는 "rebase target branch"는 평소 `git rebase <X>`의 `<X>` 자리(출발지 겸 목적지)를 가리킴
> - `--onto`를 쓰면 출발지는 그대로 두고 ==목적지만 다른 base로== 바꿔 replay할 수 있음

예를 들어 다음 history를 가정함
- server-side 기능을 추가하려고 topic branch `server`를 분기해 commit 하나(C3)를 만듦
	- `master` 브랜치에서 갈라졌음
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
- `server`: ==출발지==
	- `server`와 `client`의 ==공통 조상== 이후 commit만 가져옴
- `client`: 실제로 옮길 branch
	- 출발지인 `server`에서 옮길 대상인 `client`가 ==갈라져 나옴==

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

아래 그림은 `server` branch의 작업이 `master` 위로 replay된 결과를 보여줌

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
- `client`, `server`는 작업이 이미 통합돼 `-d`로 안전하게 삭제됨 — [[books/pro-git/03-git-branching/03-03-branch-management/index#Branch Management|Branch Management]]에서 다룸
- 그러면 전체 과정의 history가 아래 그림처럼 ==선형으로== 남음

![[rebasing-009.png]]

### The Perils of Rebasing

> [!quote]
>
> Do not rebase commits that exist outside your repository and that people may have based work on.
>
> If you follow that guideline, you’ll be fine. If you don’t, people will hate you, and you’ll be scorned by friends and family.

"exist outside your repository"
- 직역으로는 내 repository 밖으로 나간
- 외부에 공개한 repository
	- 누군가 이 repository를 remote로 사용할 수 있음
- 예시는 다음과 같음
	- 본인이 remote에 push해서 노출한 경우
	- 누군가 repository를 clone해 local에 가지고 있는 경우

rebase에는 반드시 지켜야 할 한 가지 규칙
- 이미 ==내 repository 밖으로 나간== commit은 rebase하지 말 것
	- 즉 ==남들이 그 위에 작업을 쌓았을 수 있는== commit

> [!quote]
>
> When you rebase stuff, you’re abandoning existing commits and creating new ones that are similar but different. If you push commits somewhere and others pull them down and base work on them, and then you rewrite those commits with git rebase and push them up again, your collaborators will have to re-merge their work and things will get messy when you try to pull their work back into yours.

rebase는 기존 commit을 ==버리고== 비슷하지만 ==다른 새 commit==을 만드는 작업임
- 내용은 비슷해 보여도 서로 ==별개의 commit==(다른 해시)임
- 그래서 위의 그림에서도 C4' 와 같이 `'`로 다르다는 표기를 함

문제가 생기는 시나리오는 다음과 같음
- 내가 commit을 push함
- 다른 사람이 그 commit을 pull해 그 위에 작업을 쌓음
- 내가 그 commit을 `git rebase`로 다시 쓰고(rewrite) 다시 push함
- 그러면 협업자는 자기 작업을 ==다시 merge==해야 함
- 내가 그들의 작업을 다시 pull해 가져올 때 ==history가 엉킴==

> [!quote]
>
> Let’s look at an example of how rebasing work that you’ve made public can cause problems. Suppose you clone from a central server and then do some work off that. Your commit history looks like this:

이미 ==공개한(public) 작업==을 rebase하면 어떤 문제가 생기는지 다음 상황을 가정
- 중앙 서버에서 clone함
- 그 위에서 작업을 진행해 커밋함

아래 그림처럼 로컬에 C2, C3 커밋이 생김

![[rebasing-010.png]]

> [!quote]
>
> Now, someone else does more work that includes a merge, and pushes that work to the central server. You fetch it and merge the new remote branch into your work, making your history look something like this:

이번에는 다른 사람이 작업을 더 진행함
- 그 작업에는 ==merge가 포함==되어 있음
	- C6
- 다른 사람이 merge 결과를 중앙 서버에 push함

나는 그것을 가져와 내 작업에 통합함
- `fetch`로 `teamone/master` 라는 remote-tracking-branch 갱신
- 그 `teamone/master`를 내 작업에 ==merge==함

아래 그림처럼 로컬에 C7 커밋이 생김
- three-way-merge 결과

![[rebasing-011.png]]

> [!quote]
>
> Next, the person who pushed the merged work decides to go back and rebase their work instead; they do a git push --force to overwrite the history on the server. You then fetch from that server, bringing down the new commits.

그런데 merge한 작업을 push했던 그 사람이 마음을 바꿈
- merge 대신 자기 작업을 ==rebase==하기로 결정함
- `git push --force`로 서버의 history를 ==덮어씀(overwrite)==

그 다음 내가 그 서버에서 fetch하면 새 commit들을 받아옴
- 서버에는 이미 rebase로 다시 쓰인 commit이 올라가 있음
	- C5, C4'

![[rebasing-012.png]]

> [!quote] Someone pushes rebased commits, abandoning commits you’ve based your work on
>
> Now you’re both in a pickle. If you do a git pull, you’ll create a merge commit which includes both lines of history, and your repository will look like this:

"you're both in a pickle"은 둘 다 곤란한 처지에 빠졌다는 뜻

내가 `git pull`을 하면 Git은 ==두 갈래의 history를 모두 포함하는 merge commit==을 만듦 ^pickle
- C8

이제 나와 상대방 ==둘 다 곤란한 상황==이 됨
- 상대방은 버린 커밋 C4, C6이 남아서 곤란
- 나도 두 갈래 history를 모두 merge해서 곤란

![[rebasing-013.png]]

> [!quote]
>
> If you run a git log when your history looks like this, you’ll see two commits that have the same author, date, and message, which will be confusing. Furthermore, if you push this history back up to the server, you’ll reintroduce all those rebased commits to the central server, which can further confuse people. It’s pretty safe to assume that the other developer doesn’t want C4 and C6 to be in the history; that’s why they rebased in the first place.

이 상태에서 `git log`를 보면 혼란스러움
- ==author, date, message가 똑같은== commit이 두 개씩 보임
	- 내용은 같은데 Git에서 별개의 commit(C4 ↔ C4', C6 ↔ C6')으로 취급

이 history를 다시 서버로 push하면 문제가 더 커짐
- 상대방이 ==rebase로 없앴던 commit들을 중앙 서버에 다시 끌어올림(reintroduce)==
	- C4, C6
- 다른 사람들까지 더 혼란스러워짐

상대방이 애초에 rebase를 한 이유 자체가 ==C4, C6을 history에서 빼고 싶었기== 때문임
- 그런데 내 pull → push가 그 commit들을 ==도로 살려놓는 셈==

### Rebase When You Rebase

> [!quote]
>
> If you do find yourself in a situation like this, Git has some further magic that might help you out. If someone on your team force pushes changes that overwrite work that you’ve based work on, your challenge is to figure out what is yours and what they’ve rewritten.

이런 상황에 처하더라도 Git에는 도움이 될 만한 ==추가 메커니즘==이 있음
- 팀원 누군가가 force push로 내가 토대로 삼았던 작업을 ==덮어쓴== 경우
- ==무엇이 내 것이고 무엇을 상대가 다시 쓴(rewrite) 것인지== 가려내는 것이 어려움

> [!note] "구분이 어려운 상황"이 어디서 생기나
>
> [[#The Perils of Rebasing|직전 섹션]]의 bad `git pull` 직후 내 branch에는 다음이 뒤섞여 있음
> - 오롯이 내 작업 → `C2`, `C3`
> - 동료가 rebase로 ==버린 옛 commit== → `C4`, `C6`
> - 동료가 rebase로 만든 짝 → `C4'`, `C6'`
> - 이 둘을 묶은 merge commit → `C8`
>
> 여기서 "구분이 어려운 상황"은 구체적으로 다음과 같음
> - SHA-1만 보면 `C4`(내 옛것) ≠ `C4'`(동료가 다시 쓴 것) — 해시가 다르니 ==별개 commit==으로 보임
> - 그래서 "`C4`와 `C4'`가 사실 같은 변경"임을 SHA로는 알 수 없음
> 	- 내 것/상대 것 구분이 막힘
>
> 아래의 **patch-id**가 바로 이 지점을 풀어줌
> - `C4`와 `C4'`는 ==도입한 diff가 같아 patch-id가 동일==함
> - 덕분에 Git이 `C4`를 "이미 `C4'`로 들어가 있다"고 보고 건너뛸 수 있음

> [!quote]
>
> It turns out that in addition to the commit SHA-1 checksum, Git also calculates a checksum that is based just on the patch introduced with the commit. This is called a “patch-id”.
>
> If you pull down work that was rewritten and rebase it on top of the new commits from your partner, Git can often successfully figure out what is uniquely yours and apply them back on top of the new branch.

Git은 commit SHA-1 checksum 외에 또 하나의 checksum을 계산함
- 그 commit이 ==도입한 patch(변경 내용)만을 기준==으로 계산한 checksum임
- 이를 **patch-id**라고 부름

동일한 *patch-id*를 가진 중복 commit은 ==이미 적용된 것으로 보고== 건너뛸 수 있음

> [!quote]
>
> For instance, in the previous scenario, if instead of doing a merge when we’re at Someone pushes rebased commits, abandoning commits you’ve based your work on we run git rebase teamone/master, Git will:
>
> - Determine what work is unique to our branch (C2, C3, C4, C6, C7)
> - Determine which are not merge commits (C2, C3, C4)
> - Determine which have not been rewritten into the target branch (just C2 and C3, since C4 is the same patch as C4')
> - Apply those commits to the top of teamone/master

앞 시나리오에서 [[#The Perils of Rebasing|"누군가 rebase한 commit을 push한"]] 상태에서 merge 후 C8 커밋이 생겼음

대신 `git rebase teamone/master`를 실행하면 Git은 다음 순서로 처리함

1. ==내 branch에만 있는 작업==을 추려냄
	- `C2`, `C3`, `C4`, `C6`, `C7`
2. 그중 ==merge commit이 아닌 것==만 남김
	- `C2`, `C3`, `C4`
	- merge commit(`C7` 등)은 제외됨
3. 그중 ==target branch로 이미 다시 쓰이지 않은 것==만 남김
	- `C2`, `C3`만 남음
	- `C4`는 `C4'`와 ==patch가 같아== 이미 반영된 것으로 보고 제외됨
4. 남은 commit(`C2`, `C3`)을 `teamone/master` ==끝에 적용==함

> [!quote]
>
> So instead of the result we see in You merge in the same work again into a new merge commit, we would end up with something more like Rebase on top of force-pushed rebase work.

그 결과 history 모양이 달라짐
- merge 시나리오는 같은 작업이 ==중복 commit + merge commit(`C8`)==으로 엉킨 상태가 됐음
	- "You merge in the same work again into a new merge commit"
- rebase 시나리오는 ==중복 없이 내 작업(`C2`, `C3`)만 `teamone/master` 위에 깔끔하게== 얹힌 모습이 됨
	- "rebase on top of force-pushed rebase work"
		- 여기서 "force-pushed rebase work"는 C5, C4' 커밋

![[rebasing-014.png]]

> [!quote]
>
> This only works if C4 and C4' that your partner made are almost exactly the same patch. Otherwise the rebase won’t be able to tell that it’s a duplicate and will add another C4-like patch (which will probably fail to apply cleanly, since the changes would already be at least somewhat there).

단 이 방식은 동료가 만든 `C4`와 `C4'`가 ==거의 정확히 같은 patch==일 때만 통함
- patch가 같아야 *patch-id*가 같고, 그래야 Git이 ==중복으로 인식==해 건너뜀

만약 둘이 충분히 같지 않으면 문제가 생김
- Git이 ==중복임을 알아채지 못함==
- 따라서 `C4`와 비슷한 patch를 ==하나 더 추가하려 함==
	- 그런데 그 변경의 일부는 이미 들어가 있는 상태라, ==깔끔하게 적용되지 못하고(conflict) 실패할 가능성이 큼==

> [!quote]
>
> You can also simplify this by running a git pull --rebase instead of a normal git pull. Or you could do it manually with a git fetch followed by a git rebase teamone/master in this case.

이 과정은 더 간단히 처리할 수도 있음
- 평범한 `git pull` 대신 `git pull --rebase`를 실행하면 됨
	- pull의 merge 단계를 ==rebase로 대체==함
- 또는 직접 나눠서 `git fetch` 후 `git rebase teamone/master`를 실행해도 됨

여기서 "평범한 `git pull`"이란 앞의 [[#^pickle|pickle 시점]]에서 했던 그 `git pull`을 말함
- 평범한 `git pull` = `git fetch` + `git merge`
	- 그래서 두 갈래 history를 묶는 ==merge commit(`C8`)이 생기고 중복 commit이 남음==

> [!quote]
>
> If you are using git pull and want to make --rebase the default, you can set the pull.rebase config value with something like git config --global pull.rebase true.

`git pull`을 주로 쓰는데 `--rebase`를 ==기본 동작으로== 만들고 싶다면 config를 설정하면 됨
- `pull.rebase` 값을 `true`로 지정함
- 예: `git config --global pull.rebase true`
	- 이후로는 그냥 `git pull`만 해도 ==merge 대신 rebase==로 동작함

> [!quote]
>
> If you only ever rebase commits that have never left your own computer, you’ll be just fine. If you rebase commits that have been pushed, but that no one else has based commits from, you’ll also be fine. If you rebase commits that have already been pushed publicly, and people may have based work on those commits, then you may be in for some frustrating trouble, and the scorn of your teammates.
>
> If you or a partner does find it necessary at some point, make sure everyone knows to run git pull --rebase to try to make the pain after it happens a little bit simpler.

rebase가 안전한지는 ==그 commit이 어디까지 나갔는가==로 갈림

| 상황                                                      | 결과                 |
| ------------------------------------------------------- | ------------------ |
| 내 컴퓨터를 ==한 번도 떠난 적 없는== commit만 rebase                  | 안전함                |
| push는 됐지만 ==아무도 그 위에 작업을 쌓지 않은== commit을 rebase         | 안전함                |
| ==이미 공개로 push==됐고 ==남들이 그 위에 작업했을 수== 있는 commit을 rebase | 골치 아픈 문제 + 팀원들의 비난 |

그럼에도 나나 동료가 ==불가피하게== rebase를 해야 하는 순간이 온다면
- ==모두==가 `git pull --rebase`를 쓰도록 미리 알려둘 것

### Rebase vs. Merge

> [!quote]
>
> Now that you’ve seen rebasing and merging in action, you may be wondering which one is better. Before we can answer this, let’s step back a bit and talk about what history means.
>
> One point of view on this is that your repository’s commit history is a record of what actually happened. It’s a historical document, valuable in its own right, and shouldn’t be tampered with. From this angle, changing the commit history is almost blasphemous; you’re lying about what actually transpired. So what if there was a messy series of merge commits? That’s how it happened, and the repository should preserve that for posterity.

첫 번째 관점은 repository의 *commit history*를 ==실제로 일어난 일을 기록한 역사 문서==로 보는 것
- 이 관점에서는 ==history 자체가 보존할 가치==가 있음
	- "shouldn't be tampered with"
- 따라서 이미 만들어진 commit history를 바꾸는 것은 실제로 일어난 일을 왜곡하는 것에 가까움
	- "blasphemous"
		- 신성모독, 불경스러운
- merge commit이 지저분하게 이어졌더라도 그것이 실제 진행 과정이었다면 그대로 남겨야 함
	- 나중에 보는 사람에게도 "그 일이 실제로 그렇게 일어났다"는 기록을 보존해야 하기 때문

> [!quote]
>
> The opposing point of view is that the commit history is the story of how your project was made. You wouldn’t publish the first draft of a book, so why show your messy work? When you’re working on a project, you may need a record of all your missteps and dead-end paths, but when it’s time to show your work to the world, you may want to tell a more coherent story of how to get from A to B. People in this camp use tools like rebase and filter-branch to rewrite their commits before they’re merged into the mainline branch. They use tools like rebase and filter-branch, to tell the story in the way that’s best for future readers.

반대 관점은 *commit history*를 project가 만들어진 과정을 설명하는 ==이야기==로 보는 것
- "the story of how your project was made"
- 책의 초고를 그대로 출판하지 않듯이, 작업 중의 지저분한 과정을 모두 보여줄 필요는 없다는 입장
- 작업하는 동안에는 실수, 막다른 길, 중간 시도까지 기록이 필요할 수 있음
- 작업을 세상에 보여줄 때는 `A`에서 `B`로 어떻게 도달했는지 ==더 일관된 이야기==로 정리할 수 있음

이 관점의 사람들은 mainline branch에 merge되기 전에 commit을 다시 정리함
- `rebase`, `filter-branch` 같은 도구를 사용해 commit을 rewrite함
- 목적은 과거를 그대로 보존하는 것이 아니라 ==미래의 독자가 이해하기 좋은 history==를 만드는 것

> [!quote]
>
> Now, to the question of whether merging or rebasing is better: hopefully you’ll see that it’s not that simple. Git is a powerful tool, and allows you to do many things to and with your history, but every team and every project is different. Now that you know how both of these things work, it’s up to you to decide which one is best for your particular situation.
>
> You can get the best of both worlds: rebase local changes before pushing to clean up your work, but never rebase anything that you’ve pushed somewhere.

merge와 rebase 중 어느 쪽이 더 나은지는 단순히 정할 수 없음
- Git은 history를 여러 방식으로 다룰 수 있지만, team과 project마다 필요한 기록 방식이 다름
- 두 방식이 어떻게 동작하는지 이해한 뒤 ==현재 상황에 맞는 방식==을 선택해야 함

> [!note] 실용적인 절충안
>
> push하기 전 local 변경은 `rebase`로 정리함
> - 아직 내 컴퓨터 안에 있는 commit이므로 history를 다듬어도 다른 사람에게 영향을 주지 않음
>
> 이미 어딘가에 push한 commit은 절대 rebase하지 않음
> - 다른 사람이 그 commit 위에 작업을 쌓았을 수 있기 때문
