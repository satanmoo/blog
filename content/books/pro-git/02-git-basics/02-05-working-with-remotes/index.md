---
tags:
  - books
  - pro-git
aliases:
  - 2.5 Git Basics - Working with Remotes
references:
  - https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes
---
# 2.5 Git Basics - Working with Remotes

## Working with Remotes

> [!quote]
> 
> To be able to collaborate on any Git project, you need to know how to manage your remote repositories. Remote repositories are versions of your project that are hosted on the Internet or network somewhere. You can have several of them, each of which generally is either read-only or read/write for you.

**Remote repository**
- 인터넷이나 네트워크 어딘가에 호스팅된 ==프로젝트의 사본==
- Git 프로젝트에서 ==협업하려면 *remote repository*를 다루는 법==을 알아야 함
- ==여러 개의 *Remote repository* 를 동시에== 둘 수 있음
	- 각 *remote*는 사용자에게 ==`read-only` 또는 `read/write`== 권한으로 노출됨

> [!quote]
> 
> Collaborating with others involves managing these remote repositories and pushing and pulling data to and from them when you need to share work. Managing remote repositories includes knowing how to add remote repositories, remove remotes that are no longer valid, manage various remote branches and define them as being tracked or not, and more.

협업의 두 축
- *remote repository* 관리
- *push* / *pull*로 데이터를 ==주고받기==

*remote repository* 관리에 포함되는 작업
- *remote repository* 추가
- ==유효하지 않은 *remote repository* 제거==
- 다양한 *remote branch* 관리
	- *tracked* 여부 정의 등

> [!quote] Note — Remote repositories can be on your local machine.
> 
> It is entirely possible that you can be working with a "remote" repository that is, in fact, on the same host you are. The word "remote" does not necessarily imply that the repository is somewhere else on the network or Internet, only that it is elsewhere. Working with such a remote repository would still involve all the standard pushing, pulling and fetching operations as with any other remote.

"remote"는 ==물리적 거리가 아닌 "다른 곳"==을 뜻함
- 네트워크/인터넷 너머에 있을 필요 없이, ==같은 호스트 안==의 다른 위치도 *remote*가 될 수 있음
- 그런 *remote*를 대상으로도 ==표준 동작이 동일하게 동작==
	- 표준 동작은 아래와 같음
		- *push*
		- *pull*
		- *fetch*

### Showing Your Remotes

> [!quote]
> 
> To see which remote servers you have configured, you can run the `git remote` command. It lists the shortnames of each remote handle you've specified. If you've cloned your repository, you should at least see `origin` — that is the default name Git gives to the server you cloned from:
> 
> ```
> $ git clone https://github.com/schacon/ticgit
> Cloning into 'ticgit'...
> remote: Reusing existing pack: 1857, done.
> remote: Total 1857 (delta 0), reused 0 (delta 0)
> Receiving objects: 100% (1857/1857), 374.35 KiB | 268.00 KiB/s, done.
> Resolving deltas: 100% (772/772), done.
> Checking connectivity... done.
> $ cd ticgit
> $ git remote
> origin
> ```

설정된 *remote repository*들을 보려면 `git remote` 실행
- 등록된 각 *remote handle*의 **shortname**을 나열

**origin**
- *clone*한 *repository*에서 ==Git이 원격 서버에 자동으로 붙이는 기본 *shortname*==
- 따라서 `git clone` 직후 `git remote` 결과는 최소 `origin`을 포함

> [!quote]
> 
> You can also specify `-v`, which shows you the URLs that Git has stored for the shortname to be used when reading and writing to that remote:
> 
> ```
> $ git remote -v
> origin	https://github.com/schacon/ticgit (fetch)
> origin	https://github.com/schacon/ticgit (push)
> ```

`-v` 플래그를 붙이면 각 *shortname*와 함께 저장된 ==URL이 함께== 표시됨
- 한 *shortname*에 ==읽기용(`fetch`) URL과 쓰기용(`push`) URL이 별개==로 저장됨
	- 보통은 같은 URL을 가리키지만, ==따로 지정도 가능==

> [!quote]
> 
> If you have more than one remote, the command lists them all. For example, a repository with multiple remotes for working with several collaborators might look something like this.
> 
> ```
> $ cd grit
> $ git remote -v
> bakkdoor  https://github.com/bakkdoor/grit (fetch)
> bakkdoor  https://github.com/bakkdoor/grit (push)
> cho45     https://github.com/cho45/grit (fetch)
> cho45     https://github.com/cho45/grit (push)
> defunkt   https://github.com/defunkt/grit (fetch)
> defunkt   https://github.com/defunkt/grit (push)
> koke      git://github.com/koke/grit.git (fetch)
> koke      git://github.com/koke/grit.git (push)
> origin    git@github.com:mojombo/grit.git (fetch)
> origin    git@github.com:mojombo/grit.git (push)
> ```
> 
> This means we can pull contributions from any of these users pretty easily. We may additionally have permission to push to one or more of these, though we can't tell that here.

*remote repository*가 여러 개면 ==전부 나열==됨
- 예시는 여러 협업자(`bakkdoor`, `cho45`, `defunkt`, `koke`)의 저장소를 각각 *remote repository*로 등록한 모습
- `origin`은 통상 ==자신이 *clone*한 기준 저장소==

이 출력으로 알 수 있는 것
- 어떤 *remote*에서 변경을 ==*pull*로 쉽게 가져올 수 있는지==
	- `(fetch)` 표기로 알 수 있음
- 아마도("may additionally have permission") 어떤 *remote*에 *push* 할 수 있을 수 있다 정도

알 수 없는 것
- 어떤 *remote*에 ==*push* 권한이 있는지 여부==
	- 실제 시도하거나 권한 정보를 별도로 확인해야 *push* 권한이 있는지 알 수 있음

> [!quote]
> 
> Notice that these remotes use a variety of protocols; we'll cover more about this in [Getting Git on a Server](https://git-scm.com/book/en/v2/ch00/_getting_git_on_a_server).

예시 출력의 *remote repository*들은 ==서로 다른 프로토콜==을 사용
- `https://...` — HTTPS
- `git://...` — Git native protocol
- `git@github.com:...` — SSH
- 각 프로토콜의 차이와 선택 기준은 *Getting Git on a Server* 절에서 다룸

> [!TODO] Getting Git on a Server 문서 링크

### Adding Remote Repositories

> [!quote]
> 
> We've mentioned and given some demonstrations of how the `git clone` command implicitly adds the `origin` remote for you. Here's how to add a new remote explicitly. To add a new remote Git repository as a shortname you can reference easily, run `git remote add <shortname> <url>`:
> 
> ```
> $ git remote
> origin
> $ git remote add pb https://github.com/paulboone/ticgit
> $ git remote -v
> origin	https://github.com/schacon/ticgit (fetch)
> origin	https://github.com/schacon/ticgit (push)
> pb	https://github.com/paulboone/ticgit (fetch)
> pb	https://github.com/paulboone/ticgit (push)
> ```

`git clone`은 ==`origin` *remote repository*를 자동으로 등록==
- 명시적으로 새 *remote repository*를 추가하려면 `git remote add <shortname> <url>` 사용

예시 흐름
- 처음엔 `git remote` 결과가 `origin` 하나
- `git remote add pb https://github.com/paulboone/ticgit`로 ==`pb` 라는 *shortname*== 등록
- 이후 `git remote -v`는 `origin`과 `pb` ==두 *remote*를 모두== 보여줌

> [!quote]
> 
> Now you can use the string `pb` on the command line instead of the whole URL. For example, if you want to fetch all the information that Paul has but that you don't yet have in your repository, you can run `git fetch pb`:
> 
> ```
> $ git fetch pb
> remote: Counting objects: 43, done.
> remote: Compressing objects: 100% (36/36), done.
> remote: Total 43 (delta 10), reused 31 (delta 5)
> Unpacking objects: 100% (43/43), done.
> From https://github.com/paulboone/ticgit
>  * [new branch]      master     -> pb/master
>  * [new branch]      ticgit     -> pb/ticgit
> ```

등록 후엔 명령어에서 ==URL 대신 *shortname* (`pb`)== 사용 가능
- 예: `git fetch pb` — Paul의 저장소에서 ==내가 아직 갖고 있지 않은 정보를 전부 가져옴==

출력 해석
- `[new branch] master -> pb/master`
	- Paul의 *remote*에 있는 `master` *branch*를 ==로컬에 `pb/master`로== 가져왔다는 뜻
- `[new branch] ticgit -> pb/ticgit`
	- 마찬가지로 `ticgit` *branch*를 `pb/ticgit`으로 가져옴

`git fetch` 의 동작은 다음 섹션인 [[#Fetching and Pulling from Your Remotes]]에서 다룸

> [!quote]
> 
> Paul's `master` branch is now accessible locally as `pb/master` — you can merge it into one of your branches, or you can check out a local branch at that point if you want to inspect it. We'll go over what branches are and how to use them in much more detail in [Git Branching](https://git-scm.com/book/en/v2/ch00/ch03-git-branching).

Paul의 `master`는 이제 로컬에서 `pb/master`로 접근 가능
- 활용 방법
	- 내 *branch* 중 하나에 ==*merge*==
	- 로컬로 받아온 *branch* 에 ==*checkout*해 들여다보기==
- *branch*의 개념과 사용법은 *Git Branching* 챕터에서 ==본격적으로 다룸==

> [!TODO] Git Branching 문서 링크

### Fetching and Pulling from Your Remotes

> [!quote]
> 
> As you just saw, to get data from your remote projects, you can run:
> 
> ```
> $ git fetch <remote>
> ```
> 
> The command goes out to that remote project and pulls down all the data from that remote project that you don't have yet. After you do this, you should have references to all the branches from that remote, which you can merge in or inspect at any time.

`git fetch <remote>`로 *remote repository*(프로젝트)의 데이터를 가져옴
- 해당 *remote repository*에서 ==내 로컬에 아직 없는 데이터를== 모두 내려받음
- 실행 후엔 해당 *remote*의 ==모든 *branch*에 대한 *reference*==를 가지게 됨
	- 이후 원할 때 다음과 같이 활용 가능
		- *merge*
		- *inspect* 가능

> [!quote]
> 
> If you clone a repository, the command automatically adds that remote repository under the name "origin". So, `git fetch origin` fetches any new work that has been pushed to that server since you cloned (or last fetched from) it. It's important to note that the `git fetch` command only downloads the data to your local repository — it doesn't automatically merge it with any of your work or modify what you're currently working on. You have to merge it manually into your work when you're ready.

`git clone <target-remote-repository-url>`은 자동으로 *remote repository*를 `origin`으로 등록함
- `git remote add origin <target-remote-repository-url>`로 등록하는 것과 동일함

`git clone` 이후 `git fetch origin`을 수행하면 마지막 *clone* 또는 *fetch* 이후 ==그 서버에 *push*된 새 작업을 가져옴==
- 여기서 "그 서버"는 `origin` (`<target-remote-repository-url>`과 동일함)

`git fetch`의 중요한 특징
- ==다운로드만== 수행 — 로컬 *repository*에 데이터를 가져올 뿐
- ==자동으로 *merge*하지 않음== — 현재 작업 중인 내용에 영향 없음
	- 적용하려면 ==직접 *merge*== 해야 함

> [!quote]
> 
> If your current branch is set up to track a remote branch (see the next section and [Git Branching](https://git-scm.com/book/en/v2/ch00/ch03-git-branching) for more information), you can use the `git pull` command to automatically fetch and then merge that remote branch into your current branch.

현재 *branch*가 *remote branch*를 *tracking*하도록 설정돼 있으면 `git pull` 사용 가능
- `git pull` = ==`git fetch` + `git merge`==를 한 번에 
	- *remote branch* 추적 조건은 `git merge` 때문
	- `git fetch`는 *remote branch* 추적과 무관함
- 즉 *remote*에서 데이터를 가져와 ==현재 *branch*에 바로 *merge*==
- *tracking* 설정 방법은 다음 절과 *Git Branching* 챕터에서 다룸

> [!quote]
> 
> by default, the `git clone` command automatically sets up your local `master` branch to track the remote `master` branch (or whatever the default branch is called) on the server you cloned from.

기본적으로 `git clone` 시점에 Git이 ==자동으로 *branch* *tracking* 관계를 설정==
- 로컬 `master` ←→ 원격 `master`
	- 서버의 default branch 이름이 다르면 그 이름 그대로 — 예: 로컬 `master` ←→ 원격 `main`
- 따라서 *clone*한 *repository*에서는 별도 설정 없이 ==`git pull`이 곧바로 동작==

지금까지 확인된 `git clone`의 기능
- `git remote add origin <target-remote-repository-url>`로 등록하는 것과 동일한 *remote repository* 등록 기능
- 로컬 `master` *branch*가 *clone* 대상의 원격 `master` *branch*를 추적

> [!TODO] Git Branching 문서 링크

> [!quote] Note
> 
> From Git version 2.27 onward, `git pull` will give a warning if the `pull.rebase` variable is not set. Git will keep warning you until you set the variable.
> 
> If you want the default behavior of Git (fast-forward if possible, else create a merge commit): `git config --global pull.rebase "false"`
> 
> If you want to rebase when pulling: `git config --global pull.rebase "true"`

Git 2.27부터 `pull.rebase` 변수가 ==미설정이면 `git pull` 실행 시 경고==
- 설정 전까지 ==계속 경고==가 출력됨
- `git pull`이 `git merge` 기능을 포함하기 때문

선택지
- `git config --global pull.rebase "false"` — Git의 ==기본 동작==
	- 가능하면 *fast-forward*, 아니면 *merge commit* 생성
- `git config --global pull.rebase "true"` — *pull* 시 *rebase* 수행

*rebase* vs *merge*의 차이는 *Git Branching* 챕터에서 다룸

> [!TODO] Git Branching 문서 링크

### Pushing to Your Remotes

> [!quote]
> 
> When you have your project at a point that you want to share, you have to push it upstream. The command for this is simple: `git push <remote> <branch>`. If you want to push your `master` branch to your `origin` server (again, cloning generally sets up both of those names for you automatically), then you can run this to push any commits you've done back up to the server:
> 
> ```
> $ git push origin master
> ```

프로젝트를 ==공유할 준비==가 된 시점에 *push*로 ==업스트림으로 올림==
- 명령 형식: `git push <remote> <branch>`
- `git clone`이 다음을 수행하기에 별도의 추가적인 작업 없이 *push* 가능
	- `origin` *remote repository* 추가
	- 로컬 *branch*가 `master`(또는 서버의 default branch) *remote branch*를 추적
- 예: `git push origin master` — 로컬 `master`의 *commit*들을 `origin` 서버로 올림

> [!quote]
> 
> This command works only if you cloned from a server to which you have write access and if nobody has pushed in the meantime.

`git push origin master`가 동작하려면 ==두 조건이 모두 충족==돼야 함
- *clone*한 서버에 ==write 권한==이 있을 것
- 그 사이 ==다른 사람이 *push*하지 않았을== 것

> [!quote]
> 
> If you and someone else clone at the same time and they push upstream and then you push upstream, your push will rightly be rejected. You'll have to fetch their work first and incorporate it into yours before you'll be allowed to push. See [Git Branching](https://git-scm.com/book/en/v2/ch00/ch03-git-branching) for more detailed information on how to push to remote servers.

같이 *clone*한 사람이 ==먼저 *push*한 뒤 내가 *push*를 시도하면 거절==됨
- 해결 순서
	- 먼저 `git fetch`로 ==상대 작업을 가져옴==
	- 내 작업에 통합(예: *merge* / *rebase*)
		- 위 두 작업을 한 번에 하는 것이 `git pull`
	- 그 후 *push*
- *push*의 상세한 활용은 *Git Branching* 챕터에서 다룸

> [!TODO] Git Branching 문서 링크

### Inspecting a Remote

> [!quote]
> 
> If you want to see more information about a particular remote, you can use the `git remote show <remote>` command. If you run this command with a particular shortname, such as `origin`, you get something like this:
> 
> ```
> $ git remote show origin
> * remote origin
>   Fetch URL: https://github.com/schacon/ticgit
>   Push  URL: https://github.com/schacon/ticgit
>   HEAD branch: master
>   Remote branches:
>     master                               tracked
>     dev-branch                           tracked
>   Local branch configured for 'git pull':
>     master merges with remote master
>   Local ref configured for 'git push':
>     master pushes to master (up to date)
> ```

`git remote show <remote>`로 특정 *remote*의 ==상세 정보== 조회

출력 항목
- `Fetch URL` / `Push URL` — 각각 읽기/쓰기에 쓰이는 URL
- `HEAD branch` — 서버의 ==default branch==
- `Remote branches` — 그 *remote*가 가진 *branch* 목록과 ==*tracked* 여부==
- `Local branch configured for 'git pull'` — 어떤 로컬 *branch*가 ==어떤 원격 *branch*와 *merge*== 되도록 설정됐는지
- `Local ref configured for 'git push'` — 어떤 로컬 *ref*가 ==어디로 *push*== 되도록 설정됐는지
	- 괄호 안 `(up to date)`로 ==현재 동기화 상태==도 표시

> [!quote]
> 
> It lists the URL for the remote repository as well as the tracking branch information. The command helpfully tells you that if you're on the `master` branch and you run `git pull`, it will automatically merge the remote's `master` branch into the local one after it has been fetched. It also lists all the remote references it has pulled down.

이 출력이 알려주는 것
- *remote*의 URL과 *tracking branch* 정보
- 현재 위치가 로컬 `master`일 때 `git pull`을 실행하면 ==원격 `master`를 *fetch* 후 로컬 `master`에 자동 *merge*==한다는 사실
- 지금까지 ==가져온 모든 원격 *reference*== 목록
	- `git remote show origin` 출력에서 "Remote branches" 블록을 말함
	- 마지막 *fetch* 시점 기준으로 `origin`에서 받아서 로컬에 들고 있는 *branch ref* 목록
		- 즉 [[#Fetching and Pulling from Your Remotes]]의 `git fetch` 기본 동작

> [!quote]
> 
> That is a simple example you're likely to encounter. When you're using Git more heavily, however, you may see much more information from `git remote show`:
> 
> ```
> $ git remote show origin
> * remote origin
>   URL: https://github.com/my-org/complex-project
>   Fetch URL: https://github.com/my-org/complex-project
>   Push  URL: https://github.com/my-org/complex-project
>   HEAD branch: master
>   Remote branches:
>     master                           tracked
>     dev-branch                       tracked
>     markdown-strip                   tracked
>     issue-43                         new (next fetch will store in remotes/origin)
>     issue-45                         new (next fetch will store in remotes/origin)
>     refs/remotes/origin/issue-11     stale (use 'git remote prune' to remove)
>   Local branches configured for 'git pull':
>     dev-branch merges with remote dev-branch
>     master     merges with remote master
>   Local refs configured for 'git push':
>     dev-branch                     pushes to dev-branch                     (up to date)
>     markdown-strip                 pushes to markdown-strip                 (up to date)
>     master                         pushes to master                         (up to date)
> ```
> 
> This command shows which branch is automatically pushed to when you run `git push` while on certain branches. It also shows you which remote branches on the server you don't yet have, which remote branches you have that have been removed from the server, and multiple local branches that are able to merge automatically with their remote-tracking branch when you run `git pull`.

Git을 더 깊게 쓰면 ==`git remote show`가 훨씬 많은 정보를== 보여줌

`Remote branches` 블록의 ==상태 표기==
- `tracked` — 로컬이 ==정상 추적 중==인 원격 *branch*
- `new (next fetch will store in remotes/origin)` — 서버에 ==새로 생긴 *branch*==, 아직 로컬에 *fetch*되지 않음
	- "which remote branches on the server you don't yet have"
- `stale (use 'git remote prune' to remove)` — 로컬엔 *ref*가 남아 있지만 ==서버에선 이미 삭제==됨
	- "which remote branches you have that have been removed from the server"
	- `git remote prune`으로 정리 가능

이 출력으로 알 수 있는 것
- ==특정 로컬 *branch*에서 `git push`했을 때 어디 *branch*로== 올라가는지
	- "Local refs configured for 'git push'"
- 서버에 있지만 ==내가 아직 못 가져온 원격 *branch*== (`new`)
- 서버에서 ==삭제됐는데 내 로컬에 ref만 남은 *branch*== (`stale`)
- 여러 로컬 *branch* 중 ==`git pull` 시 원격 *branch*와 자동 *merge*되도록== 설정된 것들
	- "Local branches configured for 'git pull'"

### Renaming and Removing Remotes

> [!quote]
> 
> You can run `git remote rename` to change a remote's shortname. For instance, if you want to rename `pb` to `paul`, you can do so with `git remote rename`:
> 
> ```
> $ git remote rename pb paul
> $ git remote
> origin
> paul
> ```

`git remote rename <old> <new>`로 ==*remote*의 *shortname* 변경==
- 예: `pb` → `paul`로 이름 바꾸기
- 이후 `git remote` 출력에서 `pb` 자리에 `paul`이 표시됨

> [!quote]
> 
> It's worth mentioning that this changes all your remote-tracking branch names, too. What used to be referenced at `pb/master` is now at `paul/master`.

**Remote-tracking branch**
- 로컬에 저장된 ==원격 *branch*에 대한 *reference*==
- `git fetch` 시 받아오는 원격 *branch* 목록
	- 각 *branch* 이름에 접두사를 붙여서 로컬에서 식별함
	- 이름은 `<remote>/<branch>` 형식 — 예: `pb/master`, `origin/master`

`git remote rename`은 ==`<remote>` 부분이 모두 새 이름으로 갱신==됨
- 예: `pb/master` → `paul/master`, `pb/ticgit` → `paul/ticgit`
- 즉 *shortname* 한 줄 수정이 ==그에 딸린 모든 *remote-tracking branch* 이름까지 함께== 바꿈

> [!quote]
> 
> If you want to remove a remote for some reason — you've moved the server or are no longer using a particular mirror, or perhaps a contributor isn't contributing anymore — you can either use `git remote remove` or `git remote rm`:
> 
> ```
> $ git remote remove paul
> $ git remote
> origin
> ```

==더 이상 필요 없는 *remote*를 제거==할 때 `git remote remove <shortname>` 또는 `git remote rm <shortname>` 사용
- 두 명령은 ==동일한 동작==
- 사용 시나리오 예시
	- 서버를 옮긴 경우
	- 더 이상 쓰지 않는 mirror
	- 협업이 끝난 사람의 저장소
- 실행 후 `git remote` 결과에서 해당 *shortname*이 ==사라짐==

> [!quote]
> 
> Once you delete the reference to a remote this way, all remote-tracking branches and configuration settings associated with that remote are also deleted.

*remote*를 제거하면 그에 묶인 ==모든 부수 정보도 함께 삭제==됨
- 해당 *remote*의 모든 *remote-tracking branch* (`<shortname>/*`)
	- `git fetch` 한 결과인 원격 *branch* 목록도 사라짐
- 해당 *remote*와 연결된 ==config 설정==
	- 예: `branch.<name>.remote`, `branch.<name>.merge` 등 *tracking* 관련 항목
