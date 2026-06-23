---
tags:
  - books
  - pro-git
aliases:
  - 3.4 Git Branching - Branching Workflows
references:
  - https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows
---
# 3.4 Git Branching - Branching Workflows

## Branching Workflows

### Long-Running Branches

> [!quote]
>
> Because Git uses a simple three-way merge, merging from one branch into another multiple times over a long period is generally easy to do. This means you can have several branches that are always open and that you use for different stages of your development cycle; you can merge regularly from some of them into others.

Git의 merge가 [[books/pro-git/03-git-branching/03-02-basic-branching-and-merging/index#Basic Merging|three-way merge]] 기반이라, 같은 branch들 사이를 오랜 기간 여러 번 반복해서 merge해도 부담이 크지 않음
- 단기 작업 branch에서 장기 유지 branch로 주기적으로 merge하는 상황

그래서 작업이 끝나도 삭제하지 않고 계속 열어 두는 **long-running branch(장기 유지 branch)** 를 둘 수 있음
- 개발 주기의 단계별로 branch를 나눠 둠
- 한 branch에서 다른 branch로 ==주기적으로== merge하며 작업을 흘려보냄

> [!quote]
>
> Many Git developers have a workflow that embraces this approach, such as having only code that is entirely stable in their master branch — possibly only code that has been or will be released. They have another parallel branch named develop or next that they work from or use to test stability — it isn’t necessarily always stable, but whenever it gets to a stable state, it can be merged into master.

대표적인 패턴은 ==안정성 수준에 따라== branch를 나누는 방식임

`master`에는 완전히 안정된 code만 둠
- 이미 release됐거나 release될 code만 포함함

`develop`(또는 `next`)은 `master`와 ==나란히 유지되는== 작업용 branch임
- 실제 개발과 안정성 test가 이뤄지는 곳임
- 항상 안정적이지는 않음
- ==안정 상태에 도달할 때마다 `master`로 merge함==

> [!quote]
>
> It’s used to pull in topic branches (short-lived branches, like your earlier iss53 branch) when they’re ready, to make sure they pass all the tests and don’t introduce bugs.

`develop`은 **topic branch(단기 branch)** 가 준비되면 그것을 끌어와 합치는 통합 지점 역할도 함
- `iss53`처럼 짧게 쓰고 버리는 branch가 *topic branch*임
- *topic branch*를 먼저 `develop`에 합쳐, 모든 test를 통과하고 bug를 만들지 않는지 확인함
- 검증이 끝난 변경만 최종적으로 `master`로 흘러감

> [!quote]
>
> In reality, we’re talking about pointers moving up the line of commits you’re making. The stable branches are farther down the line in your commit history, and the bleeding-edge branches are farther up the history.

commit history를 한 줄로 세워 놓고 보면
- 안정된 branch일수록 history의 ==아래쪽(더 과거의 commit)==에 위치함
	- "farther down the line"
	- `master`처럼 안정된 branch는 아래쪽에 남아 천천히 따라 올라옴
- **bleeding-edge branch**(가장 앞서 있는 실험적 branch)일수록 history의 ==위쪽(더 최신 commit)==에 위치함
	- "farther up the history"
	- topic branch처럼 앞서 가는 branch는 위쪽에서 먼저 움직임

==새 작업을 할수록 commit이 위로 쌓이므로==, branch pointer가 그 commit 줄을 따라 위로 이동하는 모습으로 이해할 수 있음
- "moving up the line of commits"

![[branching-workflows-001.png]]

> [!quote]
>
> It’s generally easier to think about them as work silos, where sets of commits graduate to a more stable silo when they’re fully tested.

같은 구조를 commit 줄 위의 위치 대신 **work silo(작업 사일로)** 묶음으로 보면 더 직관적임

각 branch를 안정성 단계별 silo(칸)로 생각함
- 한 묶음의 commit이 ==충분히 test를 통과하면== 더 안정적인 silo로 ==승급(graduate)==함
- topic 단계 → `develop` 단계 → `master` 단계 순으로 올라감

선형 비유가 pointer가 한 줄 위를 위로 이동하는 그림이라면, silo 비유는 검증을 통과한 commit 묶음이 ==더 안정적인 칸으로 옮겨가는== 그림임
- 아래 그림 참고

![[branching-workflows-002.png]]

> [!quote]
>
> You can keep doing this for several levels of stability. Some larger projects also have a proposed or pu (proposed updates) branch that has integrated branches that may not be ready to go into the next or master branch. The idea is that your branches are at various levels of stability; when they reach a more stable level, they’re merged into the branch above them.

안정성 단계는 두 단계에 그치지 않고 ==여러 단계로== 늘릴 수 있음

큰 project는 `master`/`develop` 위에 `proposed`(또는 `pu`, proposed updates) branch를 더 두기도 함
- 여러 branch를 통합은 해 뒀지만 아직 `next`나 `master`로 보내기엔 이른 작업을 담는 단계임

핵심 아이디어는 각 branch가 ==서로 다른 안정성 수준==에 있고, 더 안정적인 수준에 도달하면 ==바로 위 branch로 merge==한다는 것임

> [!tip]
>
> long-running branch를 여러 개 두는 것이 필수는 아님. 다만 아주 크거나 복잡한 project에서는 단계별 분리가 자주 도움이 됨.

### Topic Branches

> [!quote]
>
> Topic branches, however, are useful in projects of any size. A topic branch is a short-lived branch that you create and use for a single particular feature or related work. This is something you’ve likely never done with a VCS before because it’s generally too expensive to create and merge branches. But in Git it’s common to create, work on, merge, and delete branches several times a day.

long-running branch와 달리 *topic branch*는 ==프로젝트 규모와 무관하게== 유용함

*topic branch*는 ==하나의 특정 기능이나 그와 관련된 작업 하나==를 위해 만들어 잠깐 쓰는 short-lived branch임

다른 VCS에서는 익숙하지 않은 방식임
- 대부분의 VCS는 branch 생성·merge ==비용이 커서== 이렇게 자주 만들기 어려움
- 반면 Git에서는 ==하루에도 여러 번== branch를 만들고, 작업하고, merge하고, 삭제하는 것이 흔함

> [!quote]
>
> You saw this in the last section with the iss53 and hotfix branches you created. You did a few commits on them and deleted them directly after merging them into your main branch. This technique allows you to context-switch quickly and completely — because your work is separated into silos where all the changes in that branch have to do with that topic, it’s easier to see what has happened during code review and such. You can keep the changes there for minutes, days, or months, and merge them in when they’re ready, regardless of the order in which they were created or worked on.

앞 절의 [[books/pro-git/03-git-branching/03-02-basic-branching-and-merging/index#Basic Branching|`iss53`, `hotfix`]]가 바로 *topic branch*의 예임
- 몇 개 commit만 올리고, main branch에 merge한 직후 바로 삭제함

*topic branch*의 핵심 이점은 ==빠르고 완전한 context switch==임
- 한 branch의 변경이 그 topic 하나에만 묶여 silo를 이룸
- 그래서 code review처럼 변경 내용을 살필 때 무슨 일이 있었는지 파악하기 쉬움

또 다른 이점은 merge 시점이 자유롭다는 것임
- 변경을 분·일·월 단위로 얼마든지 ==묵혀 둘 수 있음==
- 준비되면 merge하되, ==만들거나 작업한 순서와 무관하게== 준비된 것부터 합칠 수 있음

> [!quote]
>
> Consider an example of doing some work (on master), branching off for an issue (iss91), working on it for a bit, branching off the second branch to try another way of handling the same thing (iss91v2), going back to your master branch and working there for a while, and then branching off there to do some work that you’re not sure is a good idea (dumbidea branch). Your commit history will look something like this:

여러 갈래로 자유롭게 분기하는 작업 흐름을 예로 들 수 있음
- `master`에서 얼마간 작업함
- 어떤 이슈를 위해 `iss91` branch를 떼어 내 잠깐 작업함
- 같은 문제를 ==다른 방식으로 시도==하려고 `iss91`에서 다시 `iss91v2` branch를 떼어 냄
- 다시 `master`로 돌아가 한동안 작업함
- 좋은 생각인지 ==확신이 없는 작업==을 위해 `master`에서 `dumbidea` branch를 떼어 냄

이렇게 여기저기로 분기하고 오가도, 각 작업이 별도 topic branch로 분리돼 있어 commit history로 흐름을 따라갈 수 있음
- 아래 그림이 이 시점의 commit history 모습임
	- 그림에서 위로 갈수록 시간상 ==나중에== 생긴 commit

![[branching-workflows-003.png]]

> [!quote]
>
> Now, let’s say you decide you like the second solution to your issue best (iss91v2); and you showed the dumbidea branch to your coworkers, and it turns out to be genius. You can throw away the original iss91 branch (losing commits C5 and C6) and merge in the other two. Your history then looks like this:

이제 어떤 branch를 살리고 버릴지 결정하는 단계임

결정 내용은 다음과 같음
- 이슈 해결책으로는 두 번째 방식인 `iss91v2`가 가장 마음에 듦
- 동료에게 보여준 `dumbidea`도 좋은 아이디어로 판명됨
- 원래의 `iss91`은 버리기로 함

그래서 `iss91v2`와 `dumbidea` 두 branch만 `master`에 merge하고, `iss91`은 폐기함
- `iss91`을 버리면 그 branch에만 있던 commit `C5`, `C6`도 ==함께 사라짐==
- 즉 topic branch는 이렇게 ==필요한 것만 골라 merge하고 나머지는 버릴 수 있음==

![[branching-workflows-004.png]]

> [!quote]
>
> We will go into more detail about the various possible workflows for your Git project in Distributed Git, so before you decide which branching scheme your next project will use, be sure to read that chapter.

여기서 다룬 long-running branch와 topic branch는 가능한 여러 workflow 중 일부일 뿐임

다양한 branching scheme과 workflow는 뒤의 [[books/pro-git/05-distributed-git/05-01-distributed-workflows/index|Distributed Git]] 장에서 더 자세히 다룸
- 다음 project에 쓸 branching scheme을 정하기 전에 그 장을 먼저 읽어 두는 것이 좋음

> [!quote]
>
> It’s important to remember when you’re doing all this that these branches are completely local. When you’re branching and merging, everything is being done only in your Git repository — there is no communication with the server.

지금까지의 branch 생성·merge·삭제는 모두 ==완전히 local에서== 일어나는 작업임
- branch를 만들고 merge하는 동안 ==server와는 어떤 통신도 하지 않음==
- 모든 변경이 내 Git repository 안에서만 이뤄짐
