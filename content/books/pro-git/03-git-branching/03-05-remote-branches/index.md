---
tags:
  - books
  - pro-git
aliases:
  - 3.5 Git Branching - Remote Branches
references:
  - https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches
---
# 3.5 Git Branching - Remote Branches

## Remote Branches

> [!quote]
>
> Remote references are references (pointers) in your remote repositories, including branches, tags, and so on. You can get a full list of remote references explicitly with `git ls-remote <remote>`, or `git remote show <remote>` for remote branches as well as more information. Nevertheless, a more common way is to take advantage of remote-tracking branches.

**remote reference**는 remote repository 안에 있는 reference(pointer)임
- ==branch, tag 등이 모두 여기에 포함됨==

remote reference 전체 목록은 명령으로 직접 확인할 수 있음
- `git ls-remote <remote>`: remote의 reference 목록을 그대로 보여줌
- `git remote show <remote>`: remote branch 정보와 함께 더 많은 내용을 보여줌

하지만 ==더 흔히 쓰는 방법==은 **remote-tracking branch**를 활용하는 것임
- remote-tracking branch는 이어지는 내용에서 자세히 다룸

> [!quote]
>
> Remote-tracking branches are references to the state of remote branches. They’re local references that you can’t move; Git moves them for you whenever you do any network communication, to make sure they accurately represent the state of the remote repository. Think of them as bookmarks, to remind you where the branches in your remote repositories were the last time you connected to them.

*remote-tracking branch*는 remote branch의 ==상태를 가리키는== reference임

이 reference는 local에 있지만 ==내가 직접 옮길 수 없음==
- ==network 통신을 할 때마다 Git이 대신 옮겨 줌==
- 그래야 remote repository의 실제 상태를 정확히 반영할 수 있기 때문임

bookmark처럼 생각하면 이해하기 쉬움
- 마지막으로 remote에 연결했던 시점에 remote branch가 어디에 있었는지 표시해 두는 표식임

> [!quote]
>
> Remote-tracking branch names take the form `<remote>/<branch>`. For instance, if you wanted to see what the master branch on your origin remote looked like as of the last time you communicated with it, you would check the origin/master branch. If you were working on an issue with a partner and they pushed up an iss53 branch, you might have your own local iss53 branch, but the branch on the server would be represented by the remote-tracking branch origin/iss53.

*remote-tracking branch*의 이름은 `<remote>/<branch>` 형식을 가짐
- `<remote>`: remote 이름 (예: `origin`)
- `<branch>`: 그 remote에 있는 branch 이름

예를 들어 `origin/master`는 마지막으로 `origin`과 ==통신했을 때== 그 `master`가 어떤 상태였는지를 보여줌

동료가 server에 `iss53` branch를 push한 상황을 생각해 볼 수 있음
- 내 local에는 내 작업용 `iss53` branch가 따로 있을 수 있음
- server 쪽 branch는 remote-tracking branch인 `origin/iss53`으로 표현됨
- 즉 같은 `iss53`이라는 이름이라도 local `iss53`과 `origin/iss53`은 ==서로 다른 reference==임

> [!quote]
>
> This may be a bit confusing, so let’s look at an example. Let’s say you have a Git server on your network at git.ourcompany.com. If you clone from this, Git’s clone command automatically names it origin for you, pulls down all its data, creates a pointer to where its master branch is, and names it origin/master locally. Git also gives you your own local master branch starting at the same place as origin’s master branch, so you have something to work from.

network에 `git.ourcompany.com` Git server가 있다고 가정함

이 server에서 clone하면 `git clone`이 다음을 ==자동으로== 처리함
- remote 이름을 `origin`으로 붙임
- server의 모든 data를 내려받음
- server의 `master` 위치를 가리키는 pointer를 만들어 local에서 `origin/master`로 이름 붙임
- `origin`의 `master`와 ==같은 지점에서 시작하는== 내 local `master` branch도 함께 만들어 줌

이렇게 clone 직후부터 작업할 기준 branch(`master`)와 remote 상태를 가리키는 reference(`origin/master`)를 둘 다 갖게 됨

> [!quote] note
>
> “origin” is not special
>
> Just like the branch name “master” does not have any special meaning in Git, neither does “origin”. While “master” is the default name for a starting branch when you run git init which is the only reason it’s widely used, “origin” is the default name for a remote when you run git clone. If you run git clone -o booyah instead, then you will have booyah/master as your default remote branch.

`origin`은 ==특별한 이름이 아님==

`master`가 Git에서 특별한 의미를 갖지 않는 것과 같은 맥락임
- `master`는 `git init` 시 시작 branch에 붙는 ==기본 이름==일 뿐이고, 그래서 널리 쓰일 뿐임
- `origin`도 `git clone` 시 remote에 붙는 ==기본 이름==일 뿐임

이 기본 이름은 옵션으로 바꿀 수 있음
- `git clone -o booyah`로 clone하면 기본 remote 이름이 `booyah`가 됨
- 이때 기본 remote branch는 `booyah/master`가 됨

![[remote-branches-001.png]]

> [!quote]
>
> If you do some work on your local master branch, and, in the meantime, someone else pushes to git.ourcompany.com and updates its master branch, then your histories move forward differently. Also, as long as you stay out of contact with your origin server, your origin/master pointer doesn’t move.

clone 이후에는 내 작업과 server의 작업이 ==서로 독립적으로== 진행될 수 있음

내가 local `master`에서 작업하는 동안 다른 사람이 `git.ourcompany.com`에 push해 server의 `master`를 갱신했다고 가정함
- 그러면 두 history가 ==서로 다르게 앞으로 나아감==

이때 핵심은 `origin/master`가 어떻게 동작하는지임
- origin server와 ==연락하지 않는 한== `origin/master` pointer는 움직이지 않음
- 즉 `origin/master`는 server의 최신 상태가 아니라, ==마지막으로 통신했던 시점의 상태==에 머물러 있음

![[remote-branches-002.png]]

> [!quote]
>
> To synchronize your work with a given remote, you run a `git fetch <remote>` command (in our case, git fetch origin). This command looks up which server “origin” is (in this case, it’s git.ourcompany.com), fetches any data from it that you don’t yet have, and updates your local database, moving your origin/master pointer to its new, more up-to-date position.

remote와 내 작업을 ==동기화==하려면 `git fetch <remote>`를 실행함 (여기서는 `git fetch origin`)

`git fetch origin`은 다음을 처리함
- `origin`이 가리키는 server가 어디인지 찾음 (여기서는 `git.ourcompany.com`)
- ==아직 갖고 있지 않은 data를 내려받음==
	- 통신
- local database를 갱신하고 `origin/master` pointer를 ==더 최신 위치로 옮김==

이렇게 fetch를 해야 비로소 `origin/master`가 server의 최신 상태를 반영함
- fetch 같은 통신 전에는 `origin/master`가 ==옛 위치에 머물러 있음==에 주의할 것

![[remote-branches-003.png]]

> [!quote]
>
> To demonstrate having multiple remote servers and what remote branches for those remote projects look like, let’s assume you have another internal Git server that is used only for development by one of your sprint teams. This server is at git.team1.ourcompany.com. You can add it as a new remote reference to the project you’re currently working on by running the git remote add command as we covered in [[books/pro-git/02-git-basics/02-01-getting-a-git-repository/index|Git Basics]]. Name this remote teamone, which will be your shortname for that whole URL.

remote server는 ==하나만 둘 수 있는 게 아님==

예를 들어 sprint team 하나가 개발용으로만 쓰는 또 다른 내부 Git server가 있다고 가정함
- 주소는 `git.team1.ourcompany.com`

이 server를 현재 작업 중인 project에 ==새 remote reference로 추가==할 수 있음
- [[books/pro-git/02-git-basics/02-05-working-with-remotes/index#Adding Remote Repositories|git remote add]] 명령으로 등록함
- 이 remote 이름을 `teamone`으로 지정함
	- `teamone`은 긴 URL을 대신 가리키는 ==shortname==임

![[remote-branches-004.png]]

> [!quote]
>
> Now, you can run git fetch teamone to fetch everything the remote teamone server has that you don’t have yet. Because that server has a subset of the data your origin server has right now, Git fetches no data but sets a remote-tracking branch called teamone/master to point to the commit that teamone has as its master branch.

이제 `git fetch teamone`으로 `teamone` server가 가진 것 중 ==아직 내게 없는 것을 모두== 받아올 수 있음

이 예시에서 `teamone`의 data는 현재 `origin`이 가진 data의 ==부분집합(subset)==임
- 그래서 Git이 실제로 새로 내려받는 data는 없음
- 대신 `teamone/master`라는 remote-tracking branch를 만들어 `teamone`의 `master`가 가리키는 commit을 가리키게 함

즉 fetch는 ==받을 새 data가 없어도== 그 remote의 상태를 가리키는 remote-tracking branch는 만들어 줌

![[remote-branches-005.png]]

### Pushing

> [!quote]
>
> When you want to share a branch with the world, you need to push it up to a remote to which you have write access. Your local branches aren’t automatically synchronized to the remotes you write to — you have to explicitly push the branches you want to share. That way, you can use private branches for work you don’t want to share, and push up only the topic branches you want to collaborate on.

branch를 다른 사람과 ==공유==하려면 ==write 권한이 있는== remote에 push해야 함

local branch는 내가 쓰는 remote와 ==자동으로 동기화되지 않음==
- 공유하고 싶은 branch는 ==직접 명시적으로 push==해야 함

이 동작 덕분에 branch를 선택적으로 공개할 수 있음
- 공유하고 싶지 않은 작업은 push하지 않고 ==private branch==로 남겨 둠
- 협업하려는 topic branch만 골라서 push함

> [!quote]
>
> If you have a branch named serverfix that you want to work on with others, you can push it up the same way you pushed your first branch. Run `git push <remote> <branch>`:
>
> ```zsh
> $ git push origin serverfix
> Counting objects: 24, done.
> Delta compression using up to 8 threads.
> Compressing objects: 100% (15/15), done.
> Writing objects: 100% (24/24), 1.91 KiB | 0 bytes/s, done.
> Total 24 (delta 2), reused 0 (delta 0)
> To https://github.com/schacon/simplegit
>  * [new branch]      serverfix -> serverfix
> ```

함께 작업할 `serverfix` branch가 있으면, [[books/pro-git/02-git-basics/02-05-working-with-remotes/index#Pushing to Your Remotes|처음 branch를 push했던 것과 같은 방식]]으로 push함

`git push <remote> <branch>` 형식을 사용함
- `git push origin serverfix`는 local의 `serverfix` branch를 `origin`에 push함
- 출력의 `* [new branch]      serverfix -> serverfix`는 remote에 `serverfix` branch가 ==새로 생성==됐다는 뜻임

> [!quote]
>
> This is a bit of a shortcut. Git automatically expands the serverfix branchname out to refs/heads/serverfix:refs/heads/serverfix, which means, “Take my serverfix local branch and push it to update the remote’s serverfix branch.” We’ll go over the refs/heads/ part in detail in [[books/pro-git/10-git-internals/10-01-plumbing-and-porcelain/index|Git Internals]], but you can generally leave it off. You can also do git push origin serverfix:serverfix, which does the same thing — it says, “Take my serverfix and make it the remote’s serverfix.” You can use this format to push a local branch into a remote branch that is named differently. If you didn’t want it to be called serverfix on the remote, you could instead run git push origin serverfix:awesomebranch to push your local serverfix branch to the awesomebranch branch on the remote project.

`git push origin serverfix`는 사실 ==축약형==임
- Git이 내부적으로 `refs/heads/serverfix:refs/heads/serverfix`로 ==확장==함
- 의미: "내 local `serverfix` branch를 가져다 remote의 `serverfix` branch를 갱신하라"
	- `refs/heads/` 부분은 [[books/pro-git/10-git-internals/10-01-plumbing-and-porcelain/index|Git Internals]] 장 참고

같은 동작을 `git push origin serverfix:serverfix`로도 쓸 수 있음
- `<src>:<dst>` 형식임 — "내 `serverfix`를 가져다 remote의 `serverfix`로 만들어라"

이 형식을 쓰면 local branch를 ==서로 이름이 다른== remote branch로 push할 수 있음
- `git push origin serverfix:awesomebranch`는 local `serverfix`를 remote에서는 `awesomebranch`라는 이름으로 push함

> [!quote] note
>
> Don’t type your password every time
>
> If you’re using an HTTPS URL to push over, the Git server will ask you for your username and password for authentication. By default it will prompt you on the terminal for this information so the server can tell if you’re allowed to push.
>
> If you don’t want to type it every single time you push, you can set up a “credential cache”. The simplest is just to keep it in memory for a few minutes, which you can easily set up by running git config --global credential.helper cache.
>
> For more information on the various credential caching options available, see [[books/pro-git/07-git-tools/07-14-credential-storage/index|Credential Storage]].

HTTPS URL로 push하면 Git server가 인증을 위해 username과 password를 물어봄
- 기본적으로 terminal에서 입력을 받아 push 권한이 있는지 확인함

매번 입력하기 번거로우면 **credential cache**를 설정할 수 있음
- 가장 간단한 방법은 password를 ==몇 분 동안 메모리에 보관==하는 것임
- `git config --global credential.helper cache`로 설정함

더 다양한 credential 저장 옵션은 [[books/pro-git/07-git-tools/07-14-credential-storage/index|Credential Storage]] 절에서 다룸

> [!quote]
>
> The next time one of your collaborators fetches from the server, they will get a reference to where the server’s version of serverfix is under the remote branch origin/serverfix:
>
> ```zsh
> $ git fetch origin
> remote: Counting objects: 7, done.
> remote: Compressing objects: 100% (2/2), done.
> remote: Total 3 (delta 0), reused 3 (delta 0)
> Unpacking objects: 100% (3/3), done.
> From https://github.com/schacon/simplegit
>  * [new branch]      serverfix    -> origin/serverfix
> ```
>
> It’s important to note that when you do a fetch that brings down new remote-tracking branches, you don’t automatically have local, editable copies of them. In other words, in this case, you don’t have a new serverfix branch — you have only an origin/serverfix pointer that you can’t modify.

동료가 다음에 server에서 fetch하면, server의 `serverfix` 위치를 가리키는 reference를 `origin/serverfix`라는 remote-tracking branch로 받게 됨
- 출력의 `* [new branch]      serverfix    -> origin/serverfix`가 이 과정을 보여줌

여기서 ==중요한 점==이 있음
- fetch로 새 remote-tracking branch를 받아도, 그것의 ==local에서 수정 가능한 복사본이 자동으로 생기지는 않음==
- 이 경우 작업용 `serverfix` (local) branch가 새로 생기는 것이 아님
- ==수정할 수 없는== `origin/serverfix` pointer 하나만 갖게 됨

> [!quote]
>
> To merge this work into your current working branch, you can run `git merge origin/serverfix`. If you want your own serverfix branch that you can work on, you can base it off your remote-tracking branch:
>
> ```zsh
> $ git checkout -b serverfix origin/serverfix
> Branch serverfix set up to track remote branch serverfix from origin.
> Switched to a new branch 'serverfix'
> ```
>
> This gives you a local branch that you can work on that starts where origin/serverfix is.

받은 remote-tracking branch의 작업을 쓰는 방법은 두 가지임

현재 작업 중인 branch에 그대로 합치려면 `git merge origin/serverfix`를 실행함
- `HEAD`가 가리키는 local branch

직접 작업할 `serverfix` branch가 필요하면 remote-tracking branch를 ==기반으로== 새 local branch를 만듦
- `git checkout -b serverfix origin/serverfix`
- `origin/serverfix`가 가리키는 ==지점에서 시작하는== local `serverfix` branch가 생김
- 출력의 `Branch serverfix set up to track remote branch serverfix from origin`은 이 local branch가 `origin/serverfix`를 ==추적(track)==하도록 설정됐다는 뜻임
	- 이렇게 ==추적==이 설정된 branch는 이어지는 내용에서 자세히 다룸

### Tracking Branches

> [!quote]
>
> Checking out a local branch from a remote-tracking branch automatically creates what is called a “tracking branch” (and the branch it tracks is called an “upstream branch”). Tracking branches are local branches that have a direct relationship to a remote branch. If you’re on a tracking branch and type git pull, Git automatically knows which server to fetch from and which branch to merge in.

remote-tracking branch에서 local branch를 checkout하면 자동으로 **tracking branch**가 만들어짐
- tracking branch가 추적하는 remote branch는 **upstream branch**라고 부름

*tracking branch*는 remote branch와 ==직접적인 관계==를 가진 local branch임

이 관계 덕분에 명령이 간단해짐
- *tracking branch*에서 `git pull`을 실행하면 Git이 ==어느 server에서 fetch하고 어느 branch를 merge할지== 자동으로 앎
	- [[books/pro-git/02-git-basics/02-05-working-with-remotes/index#Fetching and Pulling from Your Remotes|git pull]] 참고

> [!quote]
>
> When you clone a repository, it generally automatically creates a master branch that tracks origin/master. However, you can set up other tracking branches if you wish — ones that track branches on other remotes, or don’t track the master branch. The simple case is the example you just saw, running `git checkout -b <branch> <remote>/<branch>`. This is a common enough operation that Git provides the `--track` shorthand:
>
> ```zsh
> $ git checkout --track origin/serverfix
> Branch serverfix set up to track remote branch serverfix from origin.
> Switched to a new branch 'serverfix'
> ```

clone하면 보통 `origin/master`를 추적하는 `master` branch가 자동으로 만들어짐
- `master`가 *tracking branch*
- `origin/master`가 *upstream branch*
	- `master`의 upstream이 `origin/master`임

원하면 다른 *tracking branch*도 직접 설정할 수 있음
- 다른 remote의 branch를 추적하는 tracking branch
	- 예: `teamone/server-fix`를 upstream으로 하는 local branch
- `master`가 아닌 branch를 추적하는 tracking branch

앞에서 본 `git checkout -b <branch> <remote>/<branch>`가 이런 tracking branch를 만드는 가장 단순한 예임

*upstream branch*와 *tracking branch*의 이름을 통일하는 동작이 워낙 흔해서 Git은 `--track` ==축약형==을 제공함
- `git checkout --track origin/serverfix`
- 같은 이름(`serverfix`)의 local tracking branch를 만들어 줌

> [!quote]
>
> In fact, this is so common that there’s even a shortcut for that shortcut. If the branch name you’re trying to checkout (a) doesn’t exist and (b) exactly matches a name on only one remote, Git will create a tracking branch for you:
>
> ```zsh
> $ git checkout serverfix
> Branch serverfix set up to track remote branch serverfix from origin.
> Switched to a new branch 'serverfix'
> ```

이 동작이 너무 흔해서 ==축약형의 축약형==까지 있음

checkout하려는 branch 이름이 다음 두 조건을 모두 만족하면 Git이 tracking branch를 자동으로 만들어 줌
- (a) 같은 이름의 local branch가 아직 ==없음==
- (b) 단 하나의 remote에 ==인자와 정확히 같은 이름==의 branch가 존재함

그러면 `git checkout serverfix`처럼 branch 이름만 적어도 됨
- Git이 `origin/serverfix`를 추적하는 local `serverfix` tracking branch를 만들어 checkout함
- 같은 이름이 ==여러 remote에 있으면== 어느 remote인지 모호해 이 축약은 동작하지 않음

> [!quote]
>
> To set up a local branch with a different name than the remote branch, you can easily use the first version with a different local branch name:
>
> ```zsh
> $ git checkout -b sf origin/serverfix
> Branch sf set up to track remote branch serverfix from origin.
> Switched to a new branch 'sf'
> ```
>
> Now, your local branch sf will automatically pull from origin/serverfix.

remote branch와 ==이름이 다른== local branch를 만들고 싶으면, 앞의 방식(`git checkout -b`)에 다른 local 이름을 주면 됨
- `git checkout -b sf origin/serverfix`
- `origin/serverfix`를 추적하는 local `sf` branch가 생김
	- `sf`는 *tracking branch*
	- `origin/serverfix`는 *upstream branch*
- 이후 local `sf`에서 pull하면 자동으로 `origin/serverfix`에서 받아옴

> [!quote]
>
> If you already have a local branch and want to set it to a remote branch you just pulled down, or want to change the upstream branch you’re tracking, you can use the -u or --set-upstream-to option to git branch to explicitly set it at any time.
>
> ```zsh
> $ git branch -u origin/serverfix
> Branch serverfix set up to track remote branch serverfix from origin.
> ```

지금까지의 방법이 branch를 만들면서 추적을 설정했다면, ==이미 있는 local branch==에도 나중에 upstream을 설정하거나 바꿀 수 있음
- 다음 상황에서 사용
	- 방금 받아온 remote branch를 추적 대상으로 지정하고 싶을 때
	- 추적 중인 upstream branch를 다른 것으로 바꾸고 싶을 때

`git branch`의 `-u`(또는 `--set-upstream-to`) 옵션으로 ==명시적으로== 설정함
- `git branch -u origin/serverfix`는 현재 `HEAD`가 가리키는 branch가 `origin/serverfix`를 추적하도록 설정함

> [!quote] note
>
> Upstream shorthand
>
> When you have a tracking branch set up, you can reference its upstream branch with the @{upstream} or @{u} shorthand. So if you’re on the master branch and it’s tracking origin/master, you can say something like git merge @{u} instead of git merge origin/master if you wish.

tracking branch가 설정돼 있으면 그 upstream branch를 `@{upstream}` 또는 `@{u}` ==축약==으로 가리킬 수 있음

예를 들어 `master`에 있고 그 branch가 `origin/master`를 추적 중이라면
- `git merge origin/master` 대신 `git merge @{u}`라고 써도 됨
- `@{u}`가 현재 branch의 upstream인 `origin/master`를 가리키기 때문임

> [!quote]
>
> If you want to see what tracking branches you have set up, you can use the -vv option to git branch. This will list out your local branches with more information including what each branch is tracking and if your local branch is ahead, behind or both.
>
> ```zsh
> $ git branch -vv
>   iss53     7e424c3 [origin/iss53: ahead 2] Add forgotten brackets
>   master    1ae2a45 [origin/master] Deploy index fix
> * serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] This should do it
>   testing   5ea463a Try something new
> ```

설정된 tracking 관계를 한눈에 보려면 `git branch -vv`를 사용함
- local branch 목록을 ==더 자세한 정보==와 함께 보여줌
- 각 branch가 무엇을 추적하는지, upstream 대비 ==ahead/behind==인지 표시함

> [!quote]
>
> So here we can see that our iss53 branch is tracking origin/iss53 and is “ahead” by two, meaning that we have two commits locally that are not pushed to the server. We can also see that our master branch is tracking origin/master and is up to date. Next we can see that our serverfix branch is tracking the server-fix-good branch on our teamone server and is ahead by three and behind by one, meaning that there is one commit on the server we haven’t merged in yet and three commits locally that we haven’t pushed. Finally we can see that our testing branch is not tracking any remote branch.

각 줄은 다음처럼 읽음
- branch 이름 + 가리키는 commit의 짧은 SHA-1 + 최신 commit message
- `[...]` 안에 추적 중인 upstream branch와 ahead/behind 상태
	- `iss53` → `origin/iss53` 추적, ==ahead 2== (아직 push하지 않은 commit 2개)
	- `master` → `origin/master` 추적, 차이 표시 없음 (동기 상태)
	- `serverfix` → `teamone/server-fix-good` 추적, ==ahead 3, behind 1== (push하지 않은 3개 + pull(merge)하지 않은 1개)
	- `testing` → `[...]`가 없음 = ==upstream이 설정되지 않은== branch
- `*`는 현재 checkout된 branch (여기서는 `serverfix`)
	- `HEAD`가 가리킴

> [!quote]
>
> It’s important to note that these numbers are only since the last time you fetched from each server. This command does not reach out to the servers, it’s telling you about what it has cached from these servers locally. If you want totally up to date ahead and behind numbers, you’ll need to fetch from all your remotes right before running this. You could do that like this:
>
> ```zsh
> $ git fetch --all; git branch -vv
> ```

여기서 ==중요한 주의점==이 있음
- `git branch -vv`의 ahead/behind 숫자는 ==마지막으로 fetch한 시점 기준==임
- 이 명령은 server에 직접 접속하지 않고, local에 ==cache된 정보==만 보여줌

따라서 완전히 최신인 ahead/behind를 보려면 실행 직전에 모든 remote에서 fetch해야 함
- `git fetch --all; git branch -vv`
- `git fetch --all`로 모든 remote를 갱신한 뒤 곧바로 `git branch -vv`를 실행함

### Pulling

> [!quote]
>
> While the git fetch command will fetch all the changes on the server that you don’t have yet, it will not modify your working directory at all. It will simply get the data for you and let you merge it yourself. However, there is a command called git pull which is essentially a git fetch immediately followed by a git merge in most cases. If you have a tracking branch set up as demonstrated in the last section, either by explicitly setting it or by having it created for you by the clone or checkout commands, git pull will look up what server and branch your current branch is tracking, fetch from that server and then try to merge in that remote branch.

`git fetch`는 server의 새 변경을 받아오기만 하고, ==working directory는 전혀 건드리지 않음==
- data만 가져오고, merge는 ==내가 직접== 해야 함

`git pull`은 대부분의 경우 `git fetch` 직후 `git merge`를 이어서 실행하는 것과 같음
- tracking branch가 설정돼 있으면 
	- 아래 이유로 인해 설정됨
		- 직접 설정
		- clone/checkout이 자동으로 생성
- `git pull`이 현재 branch가 추적하는 server·branch를 찾아 fetch한 뒤, 그 remote branch를 merge함

### Deleting Remote Branches

> [!quote]
>
> Suppose you’re done with a remote branch — say you and your collaborators are finished with a feature and have merged it into your remote’s master branch (or whatever branch your stable codeline is in). You can delete a remote branch using the --delete option to git push. If you want to delete your serverfix branch from the server, you run the following:
>
> ```zsh
> $ git push origin --delete serverfix
> To https://github.com/schacon/simplegit
>  - [deleted]         serverfix
> ```
>
> Basically all this does is to remove the pointer from the server. The Git server will generally keep the data there for a while until a garbage collection runs, so if it was accidentally deleted, it’s often easy to recover.

어떤 remote branch를 ==더 이상 쓸 일이 없을 때== 삭제할 수 있음
- 예: 기능 작업을 끝내 remote의 `master`(또는 안정 codeline branch)에 merge한 뒤

remote branch는 `git push`의 `--delete` 옵션으로 삭제함
- `git push origin --delete serverfix`는 server에서 `serverfix` branch를 삭제함
- 출력의 `- [deleted]         serverfix`가 삭제됐음을 보여줌

이 명령이 하는 일은 사실상 ==server에서 그 pointer를 제거하는 것==뿐임
- commit data 자체는 garbage collection이 돌기 전까지 server에 한동안 남아 있음
	- 그래서 실수로 지웠어도 ==보통 쉽게 복구==할 수 있음
