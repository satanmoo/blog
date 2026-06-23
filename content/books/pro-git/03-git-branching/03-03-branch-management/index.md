---
tags:
  - books
  - pro-git
aliases:
  - 3.3 Git Branching - Branch Management
references:
  - https://git-scm.com/book/en/v2/Git-Branching-Branch-Management
---
# 3.3 Git Branching - Branch Management

## Branch Management

> [!quote]
>
> The git branch command does more than just create and delete branches. If you run it with no arguments, you get a simple listing of your current branches:
>
> ```zsh
> $ git branch
>   iss53
> * master
>   testing
> ```
>
> Notice the * character that prefixes the master branch: it indicates the branch that you currently have checked out (i.e., the branch that HEAD points to). This means that if you commit at this point, the master branch will be moved forward with your new work.

`git branch`는 branch를 생성하거나 삭제하는 명령일 뿐 아니라, ==인자 없이 실행하면== 현재 local branch 목록을 보여줌

출력에서 `*`가 붙은 branch는 현재 checkout된 branch임
- 위 예시에서는 `master`가 현재 branch
- 즉 `HEAD`가 `master` branch를 가리키고 있음

따라서 이 상태에서 새 commit을 만들면 [[books/pro-git/03-git-branching/03-01-branches-in-a-nutshell/index#^commit-advances-current-branch|현재 checkout된 branch pointer]]인 `master`가 새 commit으로 앞으로 이동함

> [!quote]
>
> To see the last commit on each branch, you can run git branch -v:
>
> ```zsh
> $ git branch -v
>   iss53   93b412c Fix javascript issue
> * master  7a98805 Merge branch 'iss53'
>   testing 782fd34 Add scott to the author list in the readme
> ```

`git branch -v`는 branch 목록에 각 branch가 가리키는 최신 commit 정보를 함께 보여줌

각 줄은 다음처럼 읽을 수 있음
- branch 이름
	- `iss53`
- 해당 branch pointer가 가리키는 commit의 짧은 SHA-1
	- `93b412c`
- 그 commit의 message
	- `Fix javascript issue`

따라서 `git branch -v`는 단순히 branch 이름만 확인하는 것이 아니라, 각 branch가 ==어떤 작업 지점에 멈춰 있는지== 빠르게 확인할 때 사용함

> [!quote]
>
> The useful --merged and --no-merged options can filter this list to branches that you have or have not yet merged into the branch you’re currently on. To see which branches are already merged into the branch you’re on, you can run git branch --merged:
>
> ```zsh
> $ git branch --merged
>   iss53
> * master
> ```
>
> Because you already merged in iss53 earlier, you see it in your list. Branches on this list without the * in front of them are generally fine to delete with git branch -d; you’ve already incorporated their work into another branch, so you’re not going to lose anything.

`git branch --merged`는 ==현재 checkout된 branch==에 ==이미 merge된== branch만 보여줌

위 예시에서는 현재 checkout된 branch가 `master`이고, `iss53`은 이미 `master`에 merge된 상태임
- 그래서 `iss53`이 `--merged` 목록에 나타남
- `* master`는 현재 checkout된 branch를 표시함

이 목록에서 `*`가 없는 branch는 보통 `git branch -d`로 삭제해도 됨
- 그 branch의 작업 내용이 이미 현재 branch 쪽 history에 포함되어 있기 때문임
- 삭제되는 것은 branch pointer이지, 이미 merge된 commit 자체가 사라지는 것은 아님

> [!quote]
>
> To see all the branches that contain work you haven’t yet merged in, you can run git branch --no-merged:
>
> ```zsh
> $ git branch --no-merged
>   testing
> ```

`git branch --no-merged`는 ==현재 checkout된 branch==에 ==아직 merge되지 않은== branch만 보여줌

위 예시에서 `testing`이 표시되는 이유는 `testing` branch에 있는 작업이 아직 현재 branch에 포함되지 않았기 때문임
- `--merged`가 이미 들어온 branch를 보여준다면
- `--no-merged`는 아직 들어오지 않은 branch를 보여줌

따라서 이 목록에 있는 branch는 삭제하기 전에 그 branch의 작업이 정말 불필요한지 먼저 확인해야 함

> [!quote]
>
> This shows your other branch. Because it contains work that isn’t merged in yet, trying to delete it with git branch -d will fail:
>
> ```zsh
> $ git branch -d testing
> error: The branch 'testing' is not fully merged.
> If you are sure you want to delete it, run 'git branch -D testing'.
> ```
>
> If you really do want to delete the branch and lose that work, you can force it with -D, as the helpful message points out.

`testing`은 아직 현재 branch에 merge되지 않은 작업을 포함하므로, `git branch -d testing`으로는 삭제되지 않음
- Git이 막아줌

`git branch -d`는 아직 merge되지 않은 branch를 삭제하려고 하면 중단됨
- 실수로 branch pointer를 지워서 아직 합쳐지지 않은 작업을 잃지 않게 하는 ==보호 장치==임
- Git은 `not fully merged` 오류 메시지도 보여줌

정말 그 branch의 작업을 버려도 된다면 `git branch -D testing`으로 강제 삭제할 수 있음
- `-D`는 merge 여부와 관계없이 branch pointer를 삭제함
- 따라서 이 branch만 가리키던 작업은 일반적인 branch 목록에서 더 이상 찾기 어려워질 수 있음

> [!quote] tip
>
> The options described above, --merged and --no-merged will, if not given a commit or branch name as an argument, show you what is, respectively, merged or not merged into your current branch.
>
> You can always provide an additional argument to ask about the merge state with respect to some other branch without checking that other branch out first, as in, what is not merged into the master branch?
>
> ```zsh
> $ git checkout testing
> $ git branch --no-merged master
>   topicA
>   featureB
> ```

`--merged`와 `--no-merged`는 기준 commit이나 ==branch를 따로 주지 않으면== ==현재 checkout된 branch를 기준==으로 판단함

하지만 ==마지막 인자로 branch 이름을 넘기면==, 현재 checkout된 branch가 아니라 인자로 넘긴 branch를 기준으로 merge 여부를 확인함
- `git branch --no-merged master`는 `master`에 아직 merge되지 않은 branch를 보여줌
- 현재 `testing`을 checkout하고 있어도, 조회 기준은 `master`임

따라서 다른 branch로 직접 checkout하지 않아도 특정 branch 기준의 merge 상태를 확인할 수 있음

### Changing a branch name

> [!quote] caution
>
> Do not rename branches that are still in use by other collaborators. Do not rename a branch like master/main/mainline without having read the section Changing the master branch name.

다른 협업자가 아직 사용 중인 branch는 함부로 이름을 바꾸면 안 됨

branch 이름 변경은 내 local branch 이름만 바꾸는 문제가 아니라, 다른 사람이 fetch/pull/push 하던 기준 이름을 바꾸는 작업이 될 수 있음
- 협업자가 같은 branch 이름을 기준으로 작업 중이면 혼란이 생김
- 원격 branch, upstream 설정, CI/CD, 문서, 보호 규칙도 함께 영향을 받을 수 있음

특히 `master`, `main`, `mainline` 같은 기본 branch 이름은 더 조심해야 함
- repository의 기본 진입점처럼 쓰이는 이름이기 때문임
- 이런 branch를 바꾸기 전에는 `Changing the master branch name` 절을 먼저 읽고 절차를 따라야 함

> [!quote]
>
> Suppose you have a branch that is called bad-branch-name and you want to change it to corrected-branch-name, while keeping all history. You also want to change the branch name on the remote (GitHub, GitLab, other server). How do you do this?
>
> Rename the branch locally with the git branch --move command:
>
> ```zsh
> $ git branch --move bad-branch-name corrected-branch-name
> ```
>
> This replaces your bad-branch-name with corrected-branch-name, but this change is only local for now.

branch 이름을 바꿔도 기존 commit history는 그대로 유지됨

`git branch --move bad-branch-name corrected-branch-name`은 ==local에서== branch 이름을 바꾸는 명령임
- `bad-branch-name`이라는 이름을 제거하고
- 같은 branch history를 가리키는 새 이름 `corrected-branch-name`을 만듦

이 단계의 변경은 아직 ==local에만== 적용됨
- GitHub, GitLab 같은 remote server의 branch 이름은 아직 바뀌지 않음
- remote branch 이름까지 바꾸려면 별도 push/delete 절차가 필요함

> [!quote]
>
> To let others see the corrected branch on the remote, push it:
>
> ```zsh
> $ git push --set-upstream origin corrected-branch-name
> ```
>
> Now we’ll take a brief look at where we are now:
>
> ```zsh
> $ git branch --all
> * corrected-branch-name
>   main
>   remotes/origin/bad-branch-name
>   remotes/origin/corrected-branch-name
>   remotes/origin/main
> ```
>
> Notice that you’re on the branch corrected-branch-name and it’s available on the remote. However, the branch with the bad name is also still present there but you can delete it by executing the following command:
>
> ```zsh
> $ git push origin --delete bad-branch-name
> ```
>
> Now the bad branch name is fully replaced with the corrected branch name.

local에서 이름을 바꾼 branch를 ==다른 사람도 볼 수 있게 하려면== 새 branch 이름을 ==remote로 push==해야 함

`git push --set-upstream origin corrected-branch-name`은 두 가지 일을 함
- local의 `corrected-branch-name` branch를 `origin` remote에 push함
- local의 `corrected-branch-name` branch가 `origin/corrected-branch-name`을 upstream으로 추적하게 설정함

이후 `git branch --all`로 확인하면 local branch와 remote-tracking branch가 함께 보임
- `* corrected-branch-name`: 현재 checkout된 local branch
- `main`: local `main` branch
- `remotes/origin/corrected-branch-name`: 새 이름으로 remote에 생긴 branch
- `remotes/origin/bad-branch-name`: 아직 remote에 남아 있는 예전 이름의 branch

새 이름을 push해도 remote의 ==예전 branch 이름이 자동으로 사라지지는 않음==

그래서 `git push origin --delete bad-branch-name`으로 remote의 예전 branch를 삭제함
- 이 삭제까지 끝나야 remote에서도 `bad-branch-name`이 `corrected-branch-name`으로 완전히 교체된 상태가 됨

#### Changing the master branch name

> [!quote] warning
>
> Changing the name of a branch like master/main/mainline/default will break the integrations, services, helper utilities and build/release scripts that your repository uses. Before you do this, make sure you consult with your collaborators. Also, make sure you do a thorough search through your repo and update any references to the old branch name in your code and scripts.

`master`, `main`, `mainline`, `default` 같은 ==기본 branch 이름 변경==은 Git 안의 branch pointer 이름만 바꾸는 문제가 아님

이 이름은 repository 주변의 자동화와 운영 도구가 직접 참조하고 있을 수 있음
- integration
- service
- helper utility
- build/release script

그래서 실행 전에는 ==협업자와 먼저 합의==해야 하고, repo 전체에서 old branch name 참조를 검색해 코드와 스크립트까지 함께 갱신해야 함

> [!quote]
>
> Rename your local master branch into main with the following command:
>
> ```zsh
> $ git branch --move master main
> ```
>
> There’s no local master branch anymore, because it’s renamed to the main branch.

`git branch --move master main`은 local의 `master` branch 이름을 `main`으로 바꾸는 명령임

이 명령이 끝나면 local repository 안에는 더 이상 `master`라는 branch 이름이 남아 있지 않음
- 기존 `master`가 가리키던 commit history는 그대로 유지됨
- 달라지는 것은 그 history를 가리키는 branch 이름이 `main`으로 바뀐다는 점임

이 단계는 ==local branch 이름 변경==만 처리함

> [!quote]
>
> To let others see the new main branch, you need to push it to the remote. This makes the renamed branch available on the remote.
>
> ```zsh
> $ git push --set-upstream origin main
> ```
>
> Now we end up with the following state:
>
> ```zsh
> $ git branch --all
> * main
>   remotes/origin/HEAD -> origin/master
>   remotes/origin/main
>   remotes/origin/master
> ```
>
> Your local master branch is gone, as it’s replaced with the main branch. The main branch is present on the remote. However, the old master branch is still present on the remote. Other collaborators will continue to use the master branch as the base of their work, until you make some further changes.

다른 사람이 새 `main` branch를 볼 수 있게 하려면 local에서 이름을 바꾼 branch를 remote로 push해야 함

`git push --set-upstream origin main`은 두 가지 일을 함
- local의 `main` branch를 `origin` remote에 push해 `origin/main`을 만듦
- local `main` branch가 `origin/main`을 upstream으로 추적하게 설정함

이 시점의 상태는 local과 remote가 완전히 정리된 상태가 아님
- local에는 `master`가 사라지고 `main`만 남음
- remote에는 새 `origin/main`이 생김
- 하지만 예전 `origin/master`도 아직 남아 있음
- `remotes/origin/HEAD -> origin/master`는 ==remote의 기본 branch==가 여전히 `master`를 가리키고 있음을 보여줌
	- remote의 `HEAD`는 remote repository의 ==기본 branch==를 보여줌

따라서 여기까지 수행하면 협업자들은 여전히 `master`를 기준 branch로 사용할 수 있음

remote 기본 branch와 예전 `master` 정리는 ==별도의 추가 변경==으로 처리해야 함

> [!quote]
>
> Now you have a few more tasks in front of you to complete the transition:
>
> Any projects that depend on this one will need to update their code and/or configuration.
>
> Update any test-runner configuration files.
>
> Adjust build and release scripts.
>
> Redirect settings on your repo host for things like the repo’s default branch, merge rules, and other things that match branch names.
>
> Update references to the old branch in documentation.
>
> Close or merge any pull requests that target the old branch.
>
> After you’ve done all these tasks, and are certain the main branch performs just as the master branch, you can delete the master branch:
>
> ```zsh
> $ git push origin --delete master
> ```

`master`에서 `main`으로 전환하는 작업은 branch 이름을 push하는 것으로 끝나지 않음

remote의 예전 `master` branch를 삭제하기 전에 ==주변 의존성을 먼저 정리==해야 함
- 이 repository에 의존하는 다른 project의 code/configuration
- test-runner configuration file
- build/release script
- repository host의 default branch, merge rule, branch 이름을 기준으로 동작하는 설정
- documentation 안의 old branch reference
- old branch를 target으로 삼는 pull request

이 작업들이 끝나고 `main` branch가 기존 `master`와 같은 역할을 제대로 수행한다고 확신할 수 있을 때 `git push origin --delete master`를 실행함

이 명령은 remote에서 예전 `master` branch 이름을 제거함
- commit history를 지우는 것이 아니라, remote의 `master` branch reference를 제거하는 것임

> [!note]
>
> 아래는 책 본문에는 없는 추가 정리임. remote의 예전 `master` branch를 삭제해도 remote의 기본 branch(`remotes/origin/HEAD`)는 자동으로 바뀌지 않으므로, 마지막으로 이를 `main`으로 갱신함.

`git remote set-head origin main`은 remote repository의 기본 branch를 `main`으로 변경하는 명령임

```zsh
$ git remote set-head origin main
```

이 명령을 실행하면 `remotes/origin/HEAD`가 가리키는 대상이 갱신됨
- 기존 `remotes/origin/HEAD -> origin/master`가 `remotes/origin/HEAD -> origin/main`으로 바뀜
