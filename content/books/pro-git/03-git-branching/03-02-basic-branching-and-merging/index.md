---
tags:
  - books
  - pro-git
aliases:
  - 3.2 Git Branching - Basic Branching and Merging
references:
  - https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging
---
# 3.2 Git Branching - Basic Branching and Merging

## Basic Branching and Merging

### Basic Branching

> [!quote]
>
> First, let’s say you’re working on your project and have a couple of commits already on the `master` branch.

`master` branch에 이미 몇 개의 commit이 쌓여 있는 상태에서 예시를 시작함

![[basic-branching-and-merging-001.png]]

이 commit(C2)가 이후 branch 작업의 기준점이 됨

> [!quote]
>
> You’ve decided that you’re going to work on issue #53 in whatever issue-tracking system your company uses. To create a new branch and switch to it at the same time, you can run the git checkout command with the -b switch:
>
> ```zsh
> $ git checkout -b iss53
> Switched to a new branch "iss53"
> ```
>
> This is shorthand for:
>
> ```zsh
> $ git branch iss53
> $ git checkout iss53
> ```

`git checkout -b iss53`은 `iss53` branch를 만들고 동시에 그 branch로 이동하는 shortcut임

위 명령어는 두 단계로 나눠 생각할 수 있음
- [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^create-branch-pointer|`git branch iss53`]]: 현재 commit에서 `iss53` branch pointer를 생성함
- [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^checkout-moves-head|`git checkout iss53`]]: `HEAD`를 새 branch로 이동함

![[basic-branching-and-merging-002.png]]

> [!quote]
>
> You work on your website and do some commits. Doing so moves the iss53 branch forward, because you have it checked out (that is, your HEAD is pointing to it):
>
> ```zsh
> $ vim index.html
> $ git commit -a -m 'Create new footer [issue 53]'
> ```

`iss53` branch를 checkout한 상태에서 새 commit을 만들면, [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^commit-advances-current-branch|`HEAD`가 가리키는 branch pointer]]인 `iss53`만 새 commit으로 이동함
- `master`는 기존 commit을 계속 가리킴
- 즉 새 commit은 현재 checkout된 branch의 history에만 추가됨
	- `HEAD`는 새로운 커밋을 가리킴

![[basic-branching-and-merging-003.png]]

> [!quote]
>
> Now you get the call that there is an issue with the website, and you need to fix it immediately. With Git, you don’t have to deploy your fix along with the iss53 changes you’ve made, and you don’t have to put a lot of effort into reverting those changes before you can work on applying your fix to what is in production. All you have to do is switch back to your master branch.

`master`로 checkout하면 `HEAD`와 *working directory*가 `master` branch의 snapshot 기준으로 돌아감
- [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^checkout-master-restores-snapshot|`master`로 switch]]

따라서 production 기준 hotfix를 `iss53` 작업과 분리해서 시작할 수 있음 ^seperation
- 작업하다가 언제나 돌아갈 수 있다는 점에서 "분리"

> [!quote]
>
> However, before you do that, note that if your working directory or staging area has uncommitted changes that conflict with the branch you’re checking out, Git won’t let you switch branches. It’s best to have a clean working state when you switch branches. There are ways to get around this (namely, stashing and commit amending) that we’ll cover later on, in Stashing and Cleaning. For now, let’s assume you’ve committed all your changes, so you can switch back to your master branch:
>
> ```zsh
> $ git checkout master
> Switched to branch 'master'
> ```
>
> At this point, your project working directory is exactly the way it was before you started working on issue #53, and you can concentrate on your hotfix. This is an important point to remember: when you switch branches, Git resets your working directory to look like it did the last time you committed on that branch. It adds, removes, and modifies files automatically to make sure your working copy is what the branch looked like on your last commit to it.

branch를 전환하려면 *working directory*와 *staging area* 가 목적지 branch와 ==깨끗하게 전환== 가능한 상태여야 함
- uncommitted changes가 checkout 대상 branch와 ==충돌==하면 Git은 branch 전환을 막음
	- 기본 원칙은 branch 전환 전에 clean working state를 만드는 것
		- 이 예시에서는 *commit* 으로 clean working state 달성
	- 변경을 ==임시로== 치워두는 방법은 [[books/pro-git/07-git-tools/07-03-stashing-and-cleaning/index|7.3 Git Tools - Stashing and Cleaning]]에서 다룸

이 예시에서는 모든 변경을 commit했다고 가정하고 `git checkout master`를 실행함

`master`로 돌아오면 *working directory*는 issue #53 작업을 시작하기 전 상태와 같아짐
- Git이 file을 자동으로 추가, 제거, 수정해서 `master` branch의 마지막 commit snapshot과 working copy를 맞춤
	- 그래서 hotfix 작업에 집중할 수 있음
	- 위에서 언급한 [[#^seperation |분리]]

> [!quote]
>
> Next, you have a hotfix to make. Let’s create a hotfix branch on which to work until it’s completed:
>
> ```zsh
> $ git checkout -b hotfix
> Switched to a new branch 'hotfix'
> $ vim index.html
> $ git commit -a -m 'Fix broken email address'
> [hotfix 1fb7853] Fix broken email address
>  1 file changed, 2 insertions(+)
> ```

`master` 상태에서 `git checkout -b hotfix`를 실행해 hotfix 전용 branch를 생성함
- [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^create-branch-pointer|현재 `master` commit을 가리키는 `hotfix` branch pointer를 만들고]]
- [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^checkout-moves-head|`HEAD`를 `hotfix` branch로 이동함]]

이후 다음 작업을 수행
- `index.html`을 수정하고 
	- working directory 변경
- `git commit -a -m 'Fix broken email address'` 실행
	- [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^commit-advances-current-branch|현재 checkout된 branch pointer]]인 `hotfix`가 새 commit `1fb7853`(C4)으로 이동함

commit 명령어의 출력은 [[books/pro-git/02-git-basics/02-02-recording-changes-to-the-repository/index#^commit-output-summary|commit output]] 형식임
- `[hotfix 1fb7853]`는 `hotfix` branch 위에 `1fb7853` commit(C4)이 생성됐다는 뜻
- `Fix broken email address`는 commit message
- `1 file changed, 2 insertions(+)`는 1개 file에 2줄이 추가된 요약 통계
- `-a` option은 [[books/pro-git/02-git-basics/02-02-recording-changes-to-the-repository/index#^git-commit-a-auto-stage|이미 tracked 상태인 file 변경을 자동 staging]]한다는 뜻

![[basic-branching-and-merging-004.png]]

이 hotfix commit은 `iss53` 작업과 분리되어 있고, production 기준인 `master`에서 갈라져 나온 수정임

> [!quote]
>
> You can run your tests, make sure the hotfix is what you want, and finally merge the `hotfix` branch back into your `master` branch to deploy to production. You do this with the `git merge` command:
>
> ```zsh
> $ git checkout master
> $ git merge hotfix
> Updating f42c576..3a0874c
> Fast-forward
>  index.html | 2 ++
>  1 file changed, 2 insertions(+)
> ```
>
> You’ll notice the phrase “fast-forward” in that merge. Because the commit `C4` pointed to by the branch `hotfix` you merged in was directly ahead of the commit `C2` you’re on, Git simply moves the pointer forward. To phrase that another way, when you try to merge one commit with a commit that can be reached by following the first commit’s history, Git simplifies things by moving the pointer forward because there is no divergent work to merge together — this is called a “fast-forward.”

hotfix 검증이 끝나면 production 배포를 위해 `master`로 돌아와 `hotfix`를 merge함

`Updating f42c576..3a0874c`는 merge 전후 commit 범위를 보여주는 출력
- 자세한 해석은 아래 fast-forward 설명에서 다룸

이 경우 `hotfix`가 가리키는 commit `C4`(`3a0874c`)가 현재 `master`가 가리키는 commit `C2`(`f42c576`)의 바로 앞쪽 history에 있음
- `master`와 `hotfix` 사이에 서로 갈라진 divergent work가 없음
	- 따라서 Git이 두 branch의 snapshot을 합치는 별도 merge commit을 만들 필요가 없음
- 이럴 때 Git은 `master` branch pointer만 `hotfix`가 가리키는 commit으로 앞으로 ==이동==시킴

이처럼 한 commit이 다른 commit의 history를 따라 도달 가능한 위치에 있을 때 pointer만 앞으로 이동하는 merge를 **fast-forward merge**라고 함 ^fast-forward-merge

> [!note] 책 예시의 SHA 불일치
>
> 같은 실제 실행 흐름이라면 hotfix commit 출력의 SHA와 merge/delete 출력의 hotfix tip SHA는 일치해야 함
>
> 이 책 예시에서는 `1fb7853`과 `3a0874c`가 모두 설명상 `C4` 역할로 쓰였지만 서로 다른 SHA prefix임
>
> 따라서 Git의 정상 동작이 아니라, 책의 예시 출력이 설명용으로 편집되며 SHA가 불일치하게 남은 것으로 보는 편이 자연스러움

결과적으로 hotfix 변경은 `master`가 가리키는 snapshot에 포함되고, 그 상태를 production에 배포할 수 있음

"when you try to merge **one commit** with **a commit** that can be reached by following **the first commit's** history" 표현에 대해서
- one commit : master 브랜치가 가리키는 C2
- a commit : hotfix 브랜치가 가리키는 C4
- the first commit: master 브랜치가 가리키는 C2 (문장에서 먼저 언급한 그 커밋)

![[basic-branching-and-merging-005.png]]

> [!quote]
>
> After your super-important fix is deployed, you’re ready to switch back to the work you were doing before you were interrupted. However, first you’ll delete the `hotfix` branch, because you no longer need it — the `master` branch points at the same place. You can delete it with the `-d` option to `git branch`:
>
> ```zsh
> $ git branch -d hotfix
> Deleted branch hotfix (3a0874c).
> ```
>
> Now you can switch back to your work-in-progress branch on issue #53 and continue working on it.
>
> ```zsh
> $ git checkout iss53
> Switched to branch "iss53"
> $ vim index.html
> $ git commit -a -m 'Finish the new footer [issue 53]'
> [iss53 ad82d7a] Finish the new footer [issue 53]
>  1 file changed, 1 insertion(+)
> ```

hotfix를 production에 배포한 뒤에는 중단했던 issue #53 작업으로 돌아갈 수 있음

돌아가기 전 먼저 `hotfix` branch를 삭제함
- fast-forward merge 이후 `master`가 이미 `hotfix`와 같은 commit(C4)을 가리키고 있음
	- 따라서 `hotfix` branch pointer는 더 이상 별도로 유지할 필요가 없음
- `git branch -d hotfix`는 branch pointer만 삭제할 뿐, `hotfix` 그리고 `master`가 가리키는 commit 자체를 지우는 작업이 아님

그 다음 `git checkout iss53`으로 다시 작업 중이던 branch로 돌아감

`iss53`로 checkout하면 `HEAD`와 *working directory*가 issue #53 작업 branch의 snapshot(C3)으로 전환
- 여기서 새 commit을 만들면 `iss53` branch pointer가 `ad82d7a` commit (C5)으로 앞으로 이동함

*commit* 출력도 [[books/pro-git/02-git-basics/02-02-recording-changes-to-the-repository/index#^commit-output-summary|commit output]] 형식으로 읽을 수 있음
- `[iss53 ad82d7a]`는 `iss53` branch 위에 `ad82d7a` commit이 생성됐다는 뜻
- `Finish the new footer [issue 53]`는 commit message
- `1 file changed, 1 insertion(+)`는 1개 file에 1줄이 추가된 요약 통계
- `-a` option은 [[books/pro-git/02-git-basics/02-02-recording-changes-to-the-repository/index#^git-commit-a-auto-stage|이미 tracked 상태인 file 변경을 자동 staging]]한다는 뜻

![[basic-branching-and-merging-006.png]]

> [!quote]
>
> It’s worth noting here that the work you did in your hotfix branch is not contained in the files in your iss53 branch. If you need to pull it in, you can merge your master branch into your iss53 branch by running git merge master, or you can wait to integrate those changes until you decide to pull the iss53 branch back into master later.

`hotfix` 작업은 `master`에는 반영됐지만, `iss53` branch에는 자동으로 반영되지 않음
- `iss53`는 hotfix를 만들기 전 commit(C2)에서 갈라져 나온 branch
	- `iss53`로 돌아와 만든 commit(C5)의 history는 `C2 -> C3 -> C5` 흐름
	- hotfix commit(C4)은 위 흐름과 별개
		- `iss53` branch의 file snapshot에도 hotfix에서 수정한 내용이 들어있지 않음

issue #53 작업을 계속하는 동안 hotfix 변경이 필요하다면 `iss53` branch 위에서 `git merge master`를 실행해 현재 `master`의 변경(C4)을 당겨올 수 있음

지금 당장 hotfix 변경이 필요하지 않다면 `iss53` 작업을 그대로 진행하고, 나중에 `iss53`를 `master`로 merge할 때 hotfix 변경과 issue #53 변경을 함께 통합해도 됨

### Basic Merging

> [!quote]
>
> Suppose you’ve decided that your issue #53 work is complete and ready to be merged into your master branch. In order to do that, you’ll merge your iss53 branch into master, much like you merged your hotfix branch earlier. All you have to do is check out the branch you wish to merge into and then run the git merge command:
>
> ```zsh
> $ git checkout master
> Switched to branch 'master'
> $ git merge iss53
> Merge made by the 'recursive' strategy.
> index.html |    1 +
> 1 file changed, 1 insertion(+)
> ```

issue #53 작업이 완료됐다고 판단하면 `iss53` branch를 `master` branch에 merge함

`git merge <source>`
- 현재 checkout된 branch가 *destination*
	- `git checkout master`는 merge 결과를 받을 branch를 `master`로 정하는 작업
- `git merge` 뒤에 붙인 branch가 *source*
	- `git merge iss53`는 `iss53` branch의 변경을 현재 branch인 `master`로 통합하는 작업

`Merge made by the 'recursive' strategy.`
- Git이 두 branch의 변경을 [[topics/git/reference/git-merge/index#MERGE STRATEGIES|recursive]] merge strategy로 합쳐 merge 결과를 만들었다는 뜻

`index.html |    1 +`는 `index.html`에 1줄 추가가 반영됐다는 file별 요약

`1 file changed, 1 insertion(+)`는 전체 merge 결과의 변경 통계

> [!quote]
>
> This looks a bit different than the hotfix merge you did earlier. In this case, your development history has diverged from some older point. Because the commit on the branch you’re on isn’t a direct ancestor of the branch you’re merging in, Git has to do some work. In this case, Git does a simple three-way merge, using the two snapshots pointed to by the branch tips and the common ancestor of the two.

`hotfix` merge는 [[#^fast-forward-merge|fast-forward merge]]로 branch pointer만 앞으로 옮기면 됌
- 현재 branch가 merge 대상 branch의 direct ancestor였기 때문

이번에는 pointer 이동만으로 merge를 표현할 수 없음
- `master`와 `iss53`가 공통 조상 이후 서로 다른 commit을 만들었기 때문
- *fast-forward merge* 불가능

Git은 이때 **three-way merge**를 수행함
- 현재 branch tip이 가리키는 snapshot
	- C4
- merge 대상 branch tip이 가리키는 snapshot
	- C5
- 두 branch의 common ancestor snapshot
	- C2
- 세 snapshot을 비교해 새 merge 결과를 계산한다는 뜻

![[basic-branching-and-merging-007.png]]

> [!quote]
>
> Instead of just moving the branch pointer forward, Git creates a new snapshot that results from this three-way merge and automatically creates a new commit that points to it. This is referred to as a merge commit, and is special in that it has more than one parent.

*three-way merge*에서는 branch pointer만 옮기지 않음

Git은 세 snapshot을 비교해 나온 결과를 새 snapshot으로 만들고, 그 snapshot을 가리키는 새 commit을 자동으로 생성함

이 commit을 **merge commit**이라고 함
- 일반 commit은 parent가 하나
- merge commit은 parent가 둘 이상
	- 이 예시에서는 parent는 다음과 같음
		- `master` 쪽 tip commit(C4)
		- `iss53` 쪽 tip commit(C5)
			- "tip"은 끝점을 의미함

"special in that A" : A라는 점에서 특별하다는 뜻

![[basic-branching-and-merging-008.png]]

> [!quote]
>
> Now that your work is merged in, you have no further need for the iss53 branch. You can close the issue in your issue-tracking system, and delete the branch:
>
> ```zsh
> $ git branch -d iss53
> ```

`iss53` 작업이 `master`에 merge됐으므로 issue tracking system에서는 해당 issue를 닫을 수 있음

Git에서는 더 이상 `iss53` branch pointer를 유지할 필요가 없으므로 `git branch -d iss53`로 삭제함

### Basic Merge Conflicts

> [!quote]
>
> Occasionally, this process doesn’t go smoothly. If you changed the same part of the same file differently in the two branches you’re merging, Git won’t be able to merge them cleanly. If your fix for issue #53 modified the same part of a file as the hotfix branch, you’ll get a merge conflict that looks something like this:
>
> ```zsh
> $ git merge iss53
> Auto-merging index.html
> CONFLICT (content): Merge conflict in index.html
> Automatic merge failed; fix conflicts and then commit the result.
> ```

**merge conflict**
- 두 branch에서 같은 file의 같은 부분을 서로 다르게 수정한 상태
	- Git이 자동으로 하나의 결과를 고를 수 없음

`git merge iss53` 실행 결과는 conflict 발생 지점을 알려줌
- `Auto-merging index.html`은 Git이 `index.html` merge를 시도했다는 뜻
- `CONFLICT (content): Merge conflict in index.html`은 `index.html` 내용에서 conflict가 났다는 뜻
- `Automatic merge failed; fix conflicts and then commit the result.`는 사용자가 conflict를 직접 해결한 뒤 merge 결과를 commit해야 한다는 뜻

> [!quote]
>
> Git hasn’t automatically created a new merge commit. It has paused the process while you resolve the conflict. If you want to see which files are unmerged at any point after a merge conflict, you can run git status:
>
> ```zsh
> $ git status
> On branch master
> You have unmerged paths.
>   (fix conflicts and run "git commit")
>
> Unmerged paths:
>   (use "git add <file>..." to mark resolution)
>
>     both modified:      index.html
>
> no changes added to commit (use "git add" and/or "git commit -a")
> ```

conflict가 발생하면 Git은 merge commit을 자동으로 만들지 않고 merge 과정을 멈춤

사용자가 conflict를 해결한 뒤 직접 commit해야 merge가 완료됨

`git status`는 conflict 때문에 아직 merge되지 않은 file을 보여줌
- `You have unmerged paths.`는 아직 해결되지 않은 merge 대상 file이 있다는 뜻
- `both modified: index.html`은 양쪽 branch가 모두 `index.html`을 수정했고, 그 결과 conflict 상태라는 뜻
- `use "git add <file>..." to mark resolution`은 conflict를 해결한 file을 staging해서 해결 완료로 표시하라는 뜻

> [!quote]
>
> Anything that has merge conflicts and hasn’t been resolved is listed as unmerged. Git adds standard conflict-resolution markers to the files that have conflicts, so you can open them manually and resolve those conflicts. Your file contains a section that looks something like this:
>
> ```html
> <<<<<<< HEAD:index.html
> <div id="footer">contact : email.support@github.com</div>
> =======
> <div id="footer">
>  please contact us at support@github.com
> </div>
> >>>>>>> iss53:index.html
> ```
>
> This means the version in HEAD (your master branch, because that was what you had checked out when you ran your merge command) is the top part of that block (everything above the =======), while the version in your iss53 branch looks like everything in the bottom part. In order to resolve the conflict, you have to either choose one side or the other or merge the contents yourself. For instance, you might resolve this conflict by replacing the entire block with this:
>
> ```html
> <div id="footer">
> please contact us at email.support@github.com
> </div>
> ```

아직 해결되지 않은 conflict file은 `unmerged` 상태로 표시됨

Git은 conflict가 난 file 안에 **conflict-resolution marker**를 직접 삽입함
- `<<<<<<< HEAD:index.html` 아래는 현재 checkout된 branch, 이 예시에서는 `master` 쪽 내용
	- `HEAD`로 식별할 수 있음
- `=======`는 두 branch의 내용을 나누는 구분선
- `>>>>>>> iss53:index.html` 위는 merge하려는 branch, 이 예시에서는 `iss53` 쪽 내용

사용자는 file을 열어 두 내용 중 하나를 선택하거나 새 내용으로 합친 뒤 marker를 제거해야 함

> [!quote]
>
> This resolution has a little of each section, and the <<<<<<<, =======, and >>>>>>> lines have been completely removed. After you’ve resolved each of these sections in each conflicted file, run git add on each file to mark it as resolved. Staging the file marks it as resolved in Git.
>
> If you want to use a graphical tool to resolve these issues, you can run git mergetool, which fires up an appropriate visual merge tool and walks you through the conflicts:
>
> ```zsh
> $ git mergetool
>
> This message is displayed because 'merge.tool' is not configured.
> See 'git mergetool --tool-help' or 'git help config' for more details.
> 'git mergetool' will now attempt to use one of the following tools:
> opendiff kdiff3 tkdiff xxdiff meld tortoisemerge gvimdiff diffuse diffmerge ecmerge p4merge araxis bc3 codecompare vimdiff emerge
> Merging:
> index.html
>
> Normal merge conflict for 'index.html':
>   {local}: modified file
>   {remote}: modified file
> Hit return to start merge resolution tool (opendiff):
> ```
>
> If you want to use a merge tool other than the default (Git chose opendiff in this case because the command was run on macOS), you can see all the supported tools listed at the top after “one of the following tools.” Just type the name of the tool you’d rather use.

conflict를 해결한 file에는 `<<<<<<<`, `=======`, `>>>>>>>` marker가 ==남아 있으면 안 됨==

각 conflict file의 내용을 정리한 뒤 `git add <file>`을 실행하면 Git은 그 file을 resolved 상태로 표시함
- 여기서 staging은 단순히 다음 commit에 포함한다는 뜻뿐 아니라, conflict 해결 완료를 Git에 알려주는 역할도 함

직접 file을 수정하는 대신 `git mergetool`로 graphical merge tool을 실행할 수도 있음
- `merge.tool`이 설정되어 있지 않으면 Git은 사용할 수 있는 tool 목록을 보여줌
- 이 예시에서는 macOS에서 실행했기 때문에 기본 tool로 `opendiff`를 선택함
- 다른 tool을 쓰고 싶으면 목록에 나온 tool 이름을 지정하면 됨

> [!quote] Note
>
> If you need more advanced tools for resolving tricky merge conflicts, we cover more on merging in Advanced Merging.

까다로운 merge conflict를 다루는 고급 도구와 절차는 [[books/pro-git/07-git-tools/07-08-advanced-merging/index|7.8 Git Tools - Advanced Merging]]에서 이어서 다룸

> [!quote]
>
> After you exit the merge tool, Git asks you if the merge was successful. If you tell the script that it was, it stages the file to mark it as resolved for you. You can run git status again to verify that all conflicts have been resolved:
>
> ```zsh
> $ git status
> On branch master
> All conflicts fixed but you are still merging.
>   (use "git commit" to conclude merge)
>
> Changes to be committed:
>
>     modified:   index.html
> ```

`git mergetool` 종료 후 Git은 병합이 성공했는지 유저에게 물어봄
- 프롬프트 형태

유저가 성공했다고 답하면 스크립트는 파일을 staging 하고 resolved 상태로 표시
- "script": mergetool을 실행시키는 Git 내부의 스크립트
	- 이 스크립트가 유저에게 병합 성공 여부 프롬프트를 띄움

`git status`의 `All conflicts fixed but you are still merging.`는 conflict 해결은 끝났지만 merge 자체는 아직 commit으로 마무리되지 않았다는 뜻

> [!quote]
>
> If you’re happy with that, and you verify that everything that had conflicts has been staged, you can type git commit to finalize the merge commit. The commit message by default looks something like this:
>
> ```text
> Merge branch 'iss53'
>
> Conflicts:
>     index.html
> #
> # It looks like you may be committing a merge.
> # If this is not correct, please remove the file
> #	.git/MERGE_HEAD
> # and try again.
>
>
> # Please enter the commit message for your changes. Lines starting
> # with '#' will be ignored, and an empty message aborts the commit.
> # On branch master
> # All conflicts fixed but you are still merging.
> #
> # Changes to be committed:
> #	modified:   index.html
> #
> ```
>
> If you think it would be helpful to others looking at this merge in the future, you can modify this commit message with details about how you resolved the merge and explain why you did the changes you made if these are not obvious.

다음 조건일 때 `git commit`으로 merge commit을 완성
- 유저가 머지 충돌 해결 결과에 만족
- conflict가 있던 file이 모두 staged 상태임을 확인

기본 merge commit message에는 다음 내용이 포함됨
- 병합 대상 branch
	- `Merge branch 'iss53'`
- conflict가 있었던 file
	- `Conflicts: ...`
- editor buffer에는 실제 commit message가 될 non-comment 줄과 Git 안내용 `#` comment 줄이 함께 들어 있음
	- `Merge branch 'iss53'`, `Conflicts:`, `index.html` 같은 non-comment 줄은 기본 message에 포함될 수 있음
	- `#`으로 시작하는 줄만 commit message에서 제외됨
	- 기본 message를 그대로 쓰지 않고 conflict를 어떻게 해결했는지와 왜 그렇게 바꿨는지를 commit message에 보강하면 나중에 히스토리를 볼 사람에게 유용함
