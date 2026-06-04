---
tags:
  - books
  - pro-git
aliases:
  - 2.6 Git Basics - Tagging
references:
  - https://git-scm.com/book/en/v2/Git-Basics-Tagging
---
# 2.6 Git Basics - Tagging

## Tagging

> [!quote]
> 
> Like most VCSs, Git has the ability to tag specific points in a repository's history as being important. Typically, people use this functionality to mark release points (v1.0, v2.0 and so on). In this section, you'll learn how to list existing tags, how to create and delete tags, and what the different types of tags are.

**Tag**
- *repository*의 *history* 중 ==특정 시점을 "중요"하다고 표시==하는 기능
- 대부분의 *VCS*가 공통으로 제공하는 기능
- 전형적 용도: ==release point 표시== (`v1.0`, `v2.0` 등)

이 섹션에서 다룰 내용
- 기존 *tag* 목록 조회
- *tag* 생성과 삭제
- *tag*의 종류별 차이

### Listing Your Tags

> [!quote]
> 
> Listing the existing tags in Git is straightforward. Just type `git tag` (with optional `-l` or `--list`):
> 
> ```zsh
> $ git tag
> v1.0
> v2.0
> ```
> 
> This command lists the tags in alphabetical order; the order in which they are displayed has no real importance.

*tag* 목록 조회
- `git tag` 명령으로 ==기존 *tag* 전체==를 출력
- `-l` 또는 `--list` 플래그를 ==선택적==으로 붙일 수 있음 (동작 동일)
- 출력은 ==alphabetical order==로 정렬됨
	- 다만 정렬 순서 자체에 ==실질적 의미는 없음==

> [!quote]
> 
> You can also search for tags that match a particular pattern. The Git source repo, for instance, contains more than 500 tags. If you're interested only in looking at the 1.8.5 series, you can run this:
> 
> ```zsh
> $ git tag -l "v1.8.5*"
> v1.8.5
> v1.8.5-rc0
> v1.8.5-rc1
> v1.8.5-rc2
> v1.8.5-rc3
> v1.8.5.1
> v1.8.5.2
> v1.8.5.3
> v1.8.5.4
> v1.8.5.5
> ```
> 
> > [!note] Listing tag wildcards requires `-l` or `--list` option
> > 
> > If you want just the entire list of tags, running the command `git tag` implicitly assumes you want a listing and provides one; the use of `-l` or `--list` in this case is optional.
> > 
> > If, however, you're supplying a wildcard pattern to match tag names, the use of `-l` or `--list` is mandatory.

*tag* 필터링
- `git tag -l "<pattern>"` 형식으로 ==wildcard pattern과 일치하는 *tag*만 출력==
	- 예: `"v1.8.5*"` → `v1.8.5`, `v1.8.5-rc0`, `v1.8.5.1` 등 prefix 일치 *tag*만 나옴
- wildcard pattern을 줄 때는 ==`-l` / `--list` 옵션이 필수==
	- 인자 없이 `git tag`만 실행하면 `-l / --list`옵션 생략 가능
	- pattern을 인자로 주려면 ==반드시== 명시해야 함 (생략 시 의도대로 동작하지 않음)

### Creating Tags

> [!quote]
> 
> Git supports two types of tags: lightweight and annotated.

Git의 *tag*는 ==두 종류==
- **lightweight**
- **annotated**

> [!quote]
> 
> A lightweight tag is very much like a branch that doesn't change — it's just a pointer to a specific commit.

*Lightweight tag*
- ==움직이지 않는 *branch*==와 유사
- 즉, ==특정 *commit*을 가리키는 pointer==

> [!note]
> 
> *branch*가 commit마다 어떻게 "이동"하는지는 [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index|3.1 Branches in a Nutshell]]에서 다룸

> [!TODO] Branch in a Nutshell 링크 추가

> [!quote]
> 
> Annotated tags, however, are stored as full objects in the Git database. They're checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG).

*Annotated tag*
- *lightweight tag*와 달리 ==Git database에 full object로 저장==됨
- ==checksum이 계산==됨
- 포함하는 메타데이터
	- tagger name / email / date
	- tagging message
- ==GPG로 서명·검증== 가능

> [!note]
> 
> Git object 구조 (`blob` / `tree` / `commit` / `tag`)의 내부와 checksum 메커니즘은 [[books/pro-git/10-git-internals/10-02-git-objects/index|10.2 Git Objects]]에서 다룸

> [!quote]
> 
> It's generally recommended that you create annotated tags so you can have all this information; but if you want a temporary tag or for some reason don't want to keep the other information, lightweight tags are available too.

용도별 권장
- 기본은 *annotated tag* ==권장==
	- 모든 메타데이터(tagger / date / message / 서명)를 ==함께 보존== 가능
- *lightweight tag*가 적합한 경우
	- ==임시 *tag*==가 필요할 때
	- 메타데이터를 ==보존하지 않으려== 할 때

### Annotated Tags

> [!quote]
> 
> Creating an annotated tag in Git is simple. The easiest way is to specify `-a` when you run the `tag` command:
> 
> ```zsh
> $ git tag -a v1.4 -m "my version 1.4"
> $ git tag
> v0.1
> v1.3
> v1.4
> ```

*Annotated tag* 생성
- 명령: `git tag -a <tagname>`
	- `-a` (annotate) 플래그가 ==*annotated tag* 생성을 지정==
	- *commit* 인자 생략 시 ==현재 HEAD에 tag==가 걸림 ([[#Tagging Later]]에서 과거 *commit* 지정 방법 다룸)
- 생성 후 `git tag`로 신규 *tag*가 ==목록에 추가됨을 확인==

> [!quote]
> 
> The `-m` specifies a tagging message, which is stored with the tag. If you don't specify a message for an annotated tag, Git launches your editor so you can type it in.

`-m` 옵션
- `-m "<message>"`로 ==tagging message를 인라인 지정==
- 지정한 message는 ==*tag* object에 함께 저장==됨
- *annotated tag* 생성 시 ==생략하면 Git이 editor를 자동 실행==하여 message를 입력받음

> [!quote]
> 
> You can see the tag data along with the commit that was tagged by using the `git show` command:
> 
> ```zsh
> $ git show v1.4
> tag v1.4
> Tagger: Ben Straub <ben@straub.cc>
> Date:   Sat May 3 20:19:12 2014 -0700
> 
> my version 1.4
> 
> commit ca82a6dff817ec66f44342007202690a93763949
> Author: Scott Chacon <schacon@gee-mail.com>
> Date:   Mon Mar 17 21:52:11 2008 -0700
> 
>     Change version number
> ```
> 
> That shows the tagger information, the date the commit was tagged, and the annotation message before showing the commit information.

*tag* 정보 조회
- `git show <tagname>`으로 ==*tag* 데이터와 가리키는 *commit*을 함께== 조회
- 출력 구성 (순서대로)
	- tagger 정보 (name / email)
	- tagging date
	- annotation message
	- 이어서 ==tagged된 *commit* 정보== (hash / author / date / commit message)

### Lightweight Tags

> [!quote]
> 
> Another way to tag commits is with a lightweight tag. This is basically the commit checksum stored in a file — no other information is kept. To create a lightweight tag, don't supply any of the `-a`, `-s`, or `-m` options, just provide a tag name:
> 
> ```zsh
> $ git tag v1.4-lw
> $ git tag
> v0.1
> v1.3
> v1.4
> v1.4-lw
> v1.5
> ```

*Lightweight tag* 생성
- 명령: `git tag <tagname>` — ==`-a` / `-s` / `-m` 옵션 없이== tag name만 지정
- 저장 형태: ==*commit* checksum 하나가 파일에 기록==됨
	- 그 외 정보(tagger / date / message)는 ==저장되지 않음==

> [!quote]
> 
> This time, if you run `git show` on the tag, you don't see the extra tag information. The command just shows the commit:
> 
> ```zsh
> $ git show v1.4-lw
> commit ca82a6dff817ec66f44342007202690a93763949
> Author: Scott Chacon <schacon@gee-mail.com>
> Date:   Mon Mar 17 21:52:11 2008 -0700
> 
>     Change version number
> ```

*Lightweight tag*에 `git show`
- 가리키는 ==*commit* 정보만 그대로== 출력
	- *annotated tag*의 출력에 있던 ==tag header (tagger / date / message) 블록이 빠짐==
- 저장된 메타데이터가 없으므로 ==보여줄 extra 정보 자체가 없음==

### Tagging Later

> [!quote]
> 
> You can also tag commits after you've moved past them. Suppose your commit history looks like this:
> 
> ```zsh
> $ git log --pretty=oneline
> 15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'experiment'
> a6b4c97498bd301d84096da251c98a07c7723e65 Create write support
> 0d52aaab4479697da7686c15f77a3d64d9165190 One more thing
> 6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
> 0b7434d86859cc7b8c3d5e1dddfed66ff742fcbc Add commit function
> 4682c3261057305bdd616e23b64b0857d832627b Add todo file
> 166ae0c4d3f420721acbb115cc33848dfcc2121a Create write support
> 9fceb02d0ae598e95dc970b74767f19372d61af8 Update rakefile
> 964f16d36dfccde844893cac5b347e7b3d44abbc Commit the todo
> 8a5cbc430f1a9c3d00faaeffd07798508422908a Update readme
> ```
> 
> Now, suppose you forgot to tag the project at v1.2, which was at the "Update rakefile" commit. You can add it after the fact. To tag that commit, you specify the commit checksum (or part of it) at the end of the command:
> 
> ```zsh
> $ git tag -a v1.2 9fceb02
> ```

과거 *commit*에 *tag* 걸기
- 명령 끝에 ==*commit* checksum (또는 prefix 일부)==을 인자로 추가
	- 형식: `git tag -a <tagname> <commit-hash>`
	- 예: `git tag -a v1.2 9fceb02` — `9fceb02d0...` 의 ==prefix만으로도 식별 가능==
- HEAD가 아니어도 ==이미 지나간 *commit*에 소급해서 *tag*== 부착 가능

> [!quote]
> 
> You can see that you've tagged the commit:
> 
> ```zsh
> $ git tag
> v0.1
> v1.2
> v1.3
> v1.4
> v1.4-lw
> v1.5
> 
> $ git show v1.2
> tag v1.2
> Tagger: Scott Chacon <schacon@gee-mail.com>
> Date:   Mon Feb 9 15:32:16 2009 -0800
> 
> version 1.2
> commit 9fceb02d0ae598e95dc970b74767f19372d61af8
> Author: Magnus Chacon <mchacon@gee-mail.com>
> Date:   Sun Apr 27 20:43:35 2008 -0700
> 
>     Update rakefile
> ...
> ```

뒤늦게 단 *tag* 확인
- `git tag` 목록에 ==새 *tag*가 알파벳 순으로 삽입==됨
	- 예: `v1.2`를 뒤늦게 달았어도 `v0.1`과 `v1.3` ==사이에 위치==
	- [[#Listing Your Tags]]에서 본 ==정렬 순서가 생성 시점과 무관==하다는 점의 구체적 예시
- `git show v1.2` 출력 구조
	- ==Tagger / Date==: *tag*를 ==건 시점==의 정보 (예: `Mon Feb 9 15:32:16 2009`)
	- ==commit Author / Date==: 원래 *commit* 시점의 정보 (예: `Sun Apr 27 20:43:35 2008`)
- 즉 뒤늦게 단 *tag*도 ==일반 *annotated tag*와 동일한 구조==를 가지며, 두 시점이 ==따로 보존==됨

### Sharing Tags

> [!quote]
> 
> By default, the `git push` command doesn't transfer tags to remote servers. You will have to explicitly push tags to a shared server after you have created them. This process is just like sharing remote branches — you can run `git push origin <tagname>`.
> 
> ```zsh
> $ git push origin v1.5
> Counting objects: 14, done.
> Delta compression using up to 8 threads.
> Compressing objects: 100% (12/12), done.
> Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
> Total 14 (delta 3), reused 0 (delta 0)
> To git@github.com:schacon/simplegit.git
>  * [new tag]         v1.5 -> v1.5
> ```

*tag* push
- ==기본적으로 `git push`는 *tag*를 전송하지 않음==
- 공유하려면 ==명시적으로 push== 필요
	- 명령: `git push origin <tagname>`
	- remote *branch* push와 ==동일한 형식==
- 성공 시 remote에 `* [new tag] <tagname> -> <tagname>` 메시지 표시

> [!quote]
> 
> If you have a lot of tags that you want to push up at once, you can also use the `--tags` option to the `git push` command. This will transfer all of your tags to the remote server that are not already there.
> 
> ```zsh
> $ git push origin --tags
> Counting objects: 1, done.
> Writing objects: 100% (1/1), 160 bytes | 0 bytes/s, done.
> Total 1 (delta 0), reused 0 (delta 0)
> To git@github.com:schacon/simplegit.git
>  * [new tag]         v1.4 -> v1.4
>  * [new tag]         v1.4-lw -> v1.4-lw
> ```

*tag* 일괄 push
- `git push origin --tags`로 ==한 번에 여러 *tag* push==
- ==remote에 아직 없는 모든 *tag*만== 전송됨
- *annotated tag* / *lightweight tag* ==구분 없이== 함께 전송됨
	- 예시 출력에서 `v1.4` (annotated)와 `v1.4-lw` (lightweight)가 한 번에 push

> [!quote]
> 
> Now, when someone else clones or pulls from your repository, they will get all your tags as well.

*tag*의 전파
- push된 *tag*는 ==다른 사용자가 `git clone` / `git pull`할 때 자동으로 함께== 받음
- 즉 한번 push로 ==협업자 전원에게 공유==됨

> [!quote] Note — `git push` pushes both types of tags
> 
> `git push <remote> --tags` will push both lightweight and annotated tags. There is currently no option to push only lightweight tags, but if you use `git push <remote> --follow-tags` only annotated tags will be pushed to the remote.

push 옵션별 전송되는 *tag* 종류
- `git push <remote> --tags`
	- *lightweight* / *annotated* ==둘 다 push==됨
- `git push <remote> --follow-tags`
	- ==*annotated tag*만== push됨
- ==*lightweight tag*만 골라서 push하는 옵션은 없음==

### Deleting Tags

> [!quote]
> 
> To delete a tag on your local repository, you can use `git tag -d <tagname>`. For example, we could remove our lightweight tag above as follows:
> 
> ```zsh
> $ git tag -d v1.4-lw
> Deleted tag 'v1.4-lw' (was e7d5add)
> ```

local *tag* 삭제
- 명령: `git tag -d <tagname>`
	- `-d` (delete) 플래그가 ==삭제 동작을 지정==
- 출력: `Deleted tag '<tagname>' (was <hash>)` — ==삭제 전 가리키던 *commit* hash==를 함께 표시

> [!quote]
> 
> Note that this does not remove the tag from any remote servers.

삭제 범위
- ==local repository에서만 삭제==됨
- remote에 push되어 있던 *tag*는 ==그대로 남음== — 제거하려면 별도 처리 필요

> [!quote]
> 
> There are two common variations for deleting a tag from a remote server.
> 
> The first variation is `git push <remote> :refs/tags/<tagname>`:
> 
> ```zsh
> $ git push origin :refs/tags/v1.4-lw
> To /git@github.com:schacon/simplegit.git
>  - [deleted]         v1.4-lw
> ```
> 
> The way to interpret the above is to read it as the null value before the colon is being pushed to the remote tag name, effectively deleting it.

remote *tag* 삭제 — 첫 번째 방법
- 명령: `git push <remote> :refs/tags/<tagname>`
- 의미: ==콜론 앞의 null value를 콜론 뒤의 remote *tag* ref에 push== → 결과적으로 ==해당 ref가 사라짐==
	- "아무것도 없는 값"을 remote ref에 덮어쓰는 trick
- 출력: `- [deleted] <tagname>`

> [!quote]
> 
> The second (and more intuitive) way to delete a remote tag is with:
> 
> ```zsh
> $ git push origin --delete <tagname>
> ```

remote *tag* 삭제 — 두 번째 방법
- 명령: `git push <remote> --delete <tagname>`
- 첫 번째 방법(`:refs/tags/...`)과 ==결과는 동일==
- `--delete` flag로 ==삭제 의도가 명시적으로 드러남==
	- 책에서도 "more intuitive"라고 표현

### Checking out Tags

> [!quote]
> 
> If you want to view the versions of files a tag is pointing to, you can do a `git checkout` of that tag, although this puts your repository in "detached HEAD" state, which has some ill side effects:
> 
> ```zsh
> $ git checkout v2.0.0
> Note: switching to 'v2.0.0'.
> 
> You are in 'detached HEAD' state. You can look around, make experimental
> changes and commit them, and you can discard any commits you make in this
> state without impacting any branches by performing another checkout.
> 
> If you want to create a new branch to retain commits you create, you may
> do so (now or later) by using -c with the switch command. Example:
> 
>   git switch -c <new-branch-name>
> 
> Or undo this operation with:
> 
>   git switch -
> 
> Turn off this advice by setting config variable advice.detachedHead to false
> 
> HEAD is now at 99ada87... Merge pull request #89 from schacon/appendix-final
> 
> $ git checkout v2.0-beta-0.1
> Previous HEAD position was 99ada87... Merge pull request #89 from schacon/appendix-final
> HEAD is now at df3f601... Add atlas.json and cover image
> ```

*tag* checkout
- `git checkout <tagname>`으로 ==해당 *tag*가 가리키는 *commit*의 파일 상태==를 working directory에 적용
- 그 결과 **detached HEAD** 상태 진입
	- ==어떤 *branch*에도 속하지 않은 채== 특정 *commit*에 HEAD가 직접 위치
	- 이 상태에서 만든 새 *commit*은 ==어떤 *branch*에도 속하지 않음==
	- 다른 곳으로 checkout하면 ==그 *commit*들의 참조를 잃어 사실상 분실==됨
- 작업을 ==보존하려면 새 *branch*를 만들어야== 함
	- `git switch -c <new-branch-name>` 또는 `git checkout -b <new-branch-name>`

> [!quote]
> 
> In "detached HEAD" state, if you make changes and then create a commit, the tag will stay the same, but your new commit won't belong to any branch and will be unreachable, except by the exact commit hash.

*detached HEAD*에서 *commit*을 만들었을 때
- 기준이 된 *tag* 자체는 ==그대로== 
	- 새 *commit*이 기준이 된 *tag*에 영향을 주지 않음
- 새 *commit*은 ==어떤 *branch*에도 속하지 않음==
	- ==exact commit hash로만 reach 가능== — name(branch / tag)으로는 unreachable
		- 즉 hash를 잃거나 GC 대상이 되면 사실상 분실

> [!quote]
> 
> Thus, if you need to make changes — say you're fixing a bug on an older version, for instance — you will generally want to create a branch:
> 
> ```zsh
> $ git checkout -b version2 v2.0.0
> Switched to a new branch 'version2'
> ```

*tag* 시점에서 작업이 필요할 때
- `git checkout -b <new-branch-name> <tagname>` 명령어를 실행하면 됨
- ==새 *branch*를 만들면서 동시에 그 *branch*로 switch==, 시작점은 *tag*가 가리키던 *commit*
	- 전형적으로 사용하는 상황은 ==이전 버전의 bug fix==
- 이후 *commit*은 새 *branch*에 누적 
	-  ==*detached HEAD* 문제는 회피==

> [!quote]
> 
> If you do this and make a commit, your version2 branch will be slightly different than your v2.0.0 tag since it will move forward with your new changes, so do be careful.

새 *branch*에서 *commit*하면 ==*branch* pointer가 새 *commit*을 가리키도록 이동(advance)==, 원래 *tag*는 ==그 *commit*에 고정==
- 그 결과 새 *branch*와 기준 *tag*가 ==더 이상 동일 *commit*을 가리키지 않음==
- 따라서 두 ref의 상태 차이를 ==항상 인지하고 작업== 
	- 책에서 "so do be careful" 라고 표현
