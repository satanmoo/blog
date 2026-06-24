---
tags:
  - books
  - pro-git
aliases:
  - 3.1 Git Branching - Branches in a Nutshell
references:
  - https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell
---
# 3.1 Git Branching - Branches in a Nutshell

## Branches in a Nutshell

> [!quote]
>
> When you make a commit, Git stores a commit object that contains a pointer to the snapshot of the content you staged. This object also contains the author’s name and email address, the message that you typed, and pointers to the commit or commits that directly came before this commit (its parent or parents): zero parents for the initial commit, one parent for a normal commit, and multiple parents for a commit that results from a merge of two or more branches.

Git에서 commit을 만들면 **commit object**가 저장됨

*commit object*가 포함하는 정보
- staging 한 content의 **snapshot**을 가리키는 pointer
- author name / email
- commit message
- 직전 commit을 가리키는 pointer
	- initial commit: parent 없음
	- normal commit: parent 1개
	- merge commit: parent 여러 개

> [!quote]
>
> To visualize this, let’s assume that you have a directory containing three files, and you stage them all and commit. Staging the files computes a checksum for each one (the SHA-1 hash we mentioned in What is Git?), stores that version of the file in the Git repository (Git refers to them as blobs), and adds that checksum to the staging area:

이 구조를 시각화하기 위해 세 개의 file이 있는 directory를 가정함

세 file을 모두 stage하고 commit하면 다음 과정 발생
- staging 과정에서 각 file에 대해 checksum을 계산함
	- 여기서 checksum은 [[books/pro-git/01-getting-started/01-03-what-is-git/index#^sha-1-hash|SHA-1 hash]]
- Git은 각 file ==version(내용)==을 Git repository에 저장함
	- Git은 이렇게 저장된 file version을 **blob**이라고 부름
		- *blob*은 파일의 ==스냅샷==
		- SHA-1 hash는 *blob*을 식별하는 고유 주소
- staging area에는 file 내용 기반의 checksum이 추가됨

> [!quote]
>
> When you create the commit by running git commit, Git checksums each subdirectory (in this case, just the root project directory) and stores them as a tree object in the Git repository. Git then creates a commit object that has the metadata and a pointer to the root project tree so it can re-create that snapshot when needed.

`git commit`을 실행하면 Git은 directory 구조에 대해서도 checksum을 계산함
- 이 예시에서는 root project directory 하나만 해당
	- [[books/pro-git/01-getting-started/01-03-what-is-git/index#^sha-1-hash|SHA-1 hash]]에서 ==디렉토리 구조==를 기반으로 체크섬을 생성할 수 있다고 언급함
- Git은 이 directory 정보를 Git repository에 **tree object**로 저장함

그 다음 Git은 **commit object**를 생성함

*commit object*는 다음을 포함
- metadata
	- author name / email
	- commit message
	- parent commit 정보 등
- root project tree를 가리키는 pointer
	- root project directory를 표현하는 *tree object*
	- `.git`이 위치한 working directory의 최상위 디렉토리 구조 전체
	- Git은 나중에 이 root project tree를 기준으로 해당 ==commit의 snapshot==을 다시 구성할 수 있음

> [!quote]
>
> Your Git repository now contains five objects: three blobs (each representing the contents of one of the three files), one tree that lists the contents of the directory and specifies which file names are stored as which blobs, and one commit with the pointer to that root tree and all the commit metadata.

이 시점의 Git repository에는 다섯 개의 object가 존재함
- 세 개의 **blob**
	- 각 blob은 세 file 중 하나의 내용을 나타냄
- 하나의 **tree**
	- directory의 내용을 나열함
	- 어떤 file name이 어떤 blob으로 저장되어 있는지 기록함
- 하나의 **commit**
	- root tree를 가리키는 pointer를 가짐
	- commit metadata를 함께 가짐

![[branches-in-a-nutshell-001.png]]

그림에서 ==commit object도 체크섬== 값을 가짐을 확인할 수 있음

> [!quote]
>
> If you make some changes and commit again, the next commit stores a pointer to the commit that came immediately before it.

변경사항을 만들고 다시 commit하면, 새 commit은 바로 이전 commit을 가리키는 pointer를 저장함

이 pointer가 commit 사이의 부모-자식 관계를 만들고, Git history를 이어줌

![[branches-in-a-nutshell-002.png]]

> [!quote]
>
> A branch in Git is simply a lightweight movable pointer to one of these commits. The default branch name in Git is master. As you start making commits, you’re given a master branch that points to the last commit you made. Every time you commit, the master branch pointer moves forward automatically.

Git의 **branch**는 commit 중 하나를 가리키는 ==움직일 수 있는==, ==가벼운== pointer임
- 가볍다는 말은 branch가 file snapshot을 새로 복사하는 구조가 아니라는 뜻
- branch는 특정 commit을 가리키는 ==이름 있는 pointer==로 이해할 수 있음

기본 branch 이름은 `master`
- commit을 만들기 시작하면 `master` branch가 생김
- `master`는 마지막으로 만든 commit을 가리킴
- 새 commit을 만들 때마다 `master` branch pointer는 ==자동으로== 새 commit을 가리키도록 앞으로 이동함

### Creating a New Branch

> [!quote]
>
> What happens when you create a new branch? Well, doing so creates a new pointer for you to move around. Let’s say you want to create a new branch called testing. You do this with the git branch command:
>
> ```zsh
> $ git branch testing
> ```
>
> This creates a new pointer to the same commit you’re currently on.

`git branch testing`은 `testing`이라는 새 branch를 생성함

이때 ==새 commit이 만들어지는 것이 아니라==, 현재 위치한 commit을 가리키는 새 pointer가 만들어짐 ^create-branch-pointer
- 기존 branch pointer와 새 branch pointer가 같은 commit을 가리키는 상태
- 즉 branch 생성은 commit history를 복사하는 작업이 아니라, commit을 가리키는 이름 있는 pointer를 하나 더 만드는 작업

![[branches-in-a-nutshell-003.png]]

> [!quote]
>
> How does Git know what branch you’re currently on? It keeps a special pointer called HEAD. Note that this is a lot different than the concept of HEAD in other VCSs you may be used to, such as Subversion or CVS. In Git, this is a pointer to the local branch you’re currently on. In this case, you’re still on master. The git branch command only created a new branch — it didn’t switch to that branch.

Git은 현재 어떤 branch 위에 있는지 **HEAD**라는 특별한 pointer로 판단함

Git에서 *HEAD*는 ==현재 위치한 local branch==를 가리키는 pointer임
- 이 예시에서는 아직 `master` 위에 있음
- `git branch testing`은 `testing` branch를 ==생성만== 했음
- ==branch를 생성했다고 해서 자동으로 그 branch로 이동하지는 않음==

![[branches-in-a-nutshell-004.png]]

> [!quote]
>
> You can easily see this by running a simple git log command that shows you where the branch pointers are pointing. This option is called --decorate.
>
> ```zsh
> $ git log --oneline --decorate
> f30ab (HEAD -> master, testing) Add feature #32 - ability to add new formats to the central interface
> 34ac2 Fix bug #1328 - stack overflow under certain conditions
> 98ca9 Initial commit
> ```
>
> You can see the master and testing branches that are right there next to the f30ab commit.

branch pointer가 어디를 가리키는지는 `git log --oneline --decorate`로 확인할 수 있음

`--decorate` option은 commit log에 branch pointer 정보를 함께 표시함
- `f30ab (HEAD -> master, testing)`은 `master`와 `testing`이 모두 `f30ab` commit을 가리킨다는 뜻
- `HEAD -> master`는 현재 위치한 branch가 `master`라는 뜻
- 따라서 현재 상태는 `master`와 `testing`이 같은 commit에 있고, checkout된 branch는 `master`

### Switching Branches

> [!quote]
>
> To switch to an existing branch, you run the git checkout command. Let’s switch to the new testing branch:
>
> ```zsh
> $ git checkout testing
> ```
>
> This moves HEAD to point to the testing branch.

기존 branch로 이동하려면 `git checkout` 명령을 사용함

`git checkout testing`을 실행하면 *HEAD*가 `testing` branch를 가리키도록 이동함 ^checkout-moves-head
- branch pointer 자체가 새 commit으로 이동하는 것이 아님
- ==현재 작업 위치를 나타내는== *HEAD*가 `master`에서 `testing`으로 바뀌는 것

![[branches-in-a-nutshell-005.png]]

> [!quote]
>
> What is the significance of that? Well, let’s do another commit:
>
> ```zsh
> $ vim test.rb
> $ git commit -a -m 'Make a change'
> ```

`testing` branch로 이동한 상태에서 file을 수정하고 commit을 생성함

이때 새 commit은 현재 *HEAD*가 가리키는 branch인 `testing` 위에 만들어짐
- `git commit -a`는 tracked file의 수정사항을 staging하고 commit까지 수행함
- commit 이후 앞으로 이동하는 branch pointer는 `testing` ^commit-advances-current-branch

![[branches-in-a-nutshell-006.png]]

> [!quote]
>
> This is interesting, because now your testing branch has moved forward, but your master branch still points to the commit you were on when you ran git checkout to switch branches. Let’s switch back to the master branch:
>
> ```zsh
> $ git checkout master
> ```

새 commit 이후 `testing` branch는 앞으로 이동했지만, `master` branch는 그대로 남아 있음

이후 `git checkout master`를 실행하면 *HEAD*는 `master` branch가 가리키는 commit을 가리키게 됨

![[branches-in-a-nutshell-007.png]]

> [!quote] Note - git log doesn’t show all the branches all the time
>
> If you were to run git log right now, you might wonder where the "testing" branch you just created went, as it would not appear in the output.
>
> The branch hasn’t disappeared; Git just doesn’t know that you’re interested in that branch and it is trying to show you what it thinks you’re interested in. In other words, by default, git log will only show commit history below the branch you’ve checked out.
>
> To show commit history for the desired branch you have to explicitly specify it: git log testing. To show all of the branches, add --all to your git log command.

`git log`는 기본적으로 모든 branch의 history를 항상 보여주지는 않음

현재 `master`로 돌아온 상태에서 `git log`를 실행하면 `testing` branch의 새 commit이 보이지 않을 수 있음
- `testing` branch가 사라진 것은 아님
- Git이 기본적으로 현재 checkout된 branch 아래의 commit history를 보여주기 때문
	- 그림에서 `87ab2` 커밋 아래 `f30ab` 커밋이 위치

특정 branch의 history를 보려면 branch name을 명시
- `git log testing`

모든 branch의 history를 함께 보려면 `--all` option을 추가
- `git log --all`

> [!quote]
>
> That command did two things. It moved the HEAD pointer back to point to the master branch, and it reverted the files in your working directory back to the snapshot that master points to. This also means the changes you make from this point forward will diverge from an older version of the project. It essentially rewinds the work you’ve done in your testing branch so you can go in a different direction.

`git checkout master`는 두 가지 일을 함 ^checkout-master-restores-snapshot
- *HEAD* pointer를 다시 `master` branch로 이동시킴
- *working directory*의 file들을 `master`가 가리키는 snapshot 상태로 되돌림

이 상태에서 새로 변경하고 commit하면 `testing`에서 진행한 작업과 ==다른 방향==으로 history가 갈라짐
- `testing` branch에서 했던 작업 자체가 사라지는 것은 아님

> [!quote] Note - Switching branches changes files in your working directory
>
> It’s important to note that when you switch branches in Git, files in your working directory will change. If you switch to an older branch, your working directory will be reverted to look like it did the last time you committed on that branch. If Git cannot do it cleanly, it will not let you switch at all.

Git에서 branch를 전환하면 ==working directory의 file도 바뀔 수 있음==

이전 branch로 이동하면 *working directory*는 그 branch에서 마지막으로 commit했던 상태로 되돌아감
- branch 전환은 단순히 *HEAD* pointer만 바꾸는 작업이 아님
- checkout 대상 branch의 snapshot이 실제 file 상태(*working directory*)에도 반영됨

==Git이 이 변경을 깨끗하게 처리할 수 없으면 branch 전환 자체를 허용하지 않음==
- *working directory* 에 uncommitted changes가 있을 때
- conflict가 발생할 여지가 있을 때

> [!quote]
>
> Let’s make a few changes and commit again:
>
> ```zsh
> $ vim test.rb
> $ git commit -a -m 'Make other changes'
> ```
>
> Now your project history has diverged (see Divergent history). You created and switched to a branch, did some work on it, and then switched back to your main branch and did other work. Both of those changes are isolated in separate branches: you can switch back and forth between the branches and merge them together when you’re ready. And you did all that with simple branch, checkout, and commit commands.

다시 `master` branch 위에서 file을 수정하고 commit을 생성함

이제 project history는 갈라진 상태가 됨
- `testing` branch에서는 `testing`으로 이동한 뒤 만든 commit이 있음
- `master` branch에서는 다시 `master`로 돌아온 뒤 만든 다른 commit이 있음
- 두 변경은 각각 별도 branch에 분리되어 있음

이 상태에서는 branch 사이를 오가며 작업할 수 있고, 준비가 되면 merge로 다시 합칠 수 있음

![[branches-in-a-nutshell-008.png]]

> [!quote]
>
> You can also see this easily with the git log command. If you run git log --oneline --decorate --graph --all it will print out the history of your commits, showing where your branch pointers are and how your history has diverged.
>
> ```zsh
> $ git log --oneline --decorate --graph --all
> * c2b9e (HEAD, master) Make other changes
> | * 87ab2 (testing) Make a change
> |/
> * f30ab Add feature #32 - ability to add new formats to the central interface
> * 34ac2 Fix bug #1328 - stack overflow under certain conditions
> * 98ca9 Initial commit of my project
> ```

갈라진 history는 `git log --oneline --decorate --graph --all`로 확인할 수 있음

각 option의 역할
- `--oneline`: commit 하나를 한 줄로 표시
- `--decorate`: branch pointer와 *HEAD* 위치를 함께 표시
- `--graph`: commit history의 갈라짐을 ASCII graph로 표시
- `--all`: 현재 branch뿐 아니라 모든 branch의 history를 표시

출력 해석
- `c2b9e (HEAD, master)`는 현재 `master` branch가 `c2b9e` commit을 가리킨다는 뜻
- `87ab2 (testing)`은 `testing` branch가 별도의 `87ab2` commit을 가리킨다는 뜻
- `|/` 아래의 `f30ab`는 두 branch가 갈라지기 전의 공통 commit

> [!quote]
>
> Because a branch in Git is actually a simple file that contains the 40 character SHA-1 checksum of the commit it points to, branches are cheap to create and destroy. Creating a new branch is as quick and simple as writing 41 bytes to a file (40 characters and a newline).

Git의 branch는 실제로는 자신이 가리키는 commit의 40-character SHA-1 checksum을 담은 단순한 file임
- 체크섬은 커밋의 식별자(주소)

그래서 branch 생성과 삭제 비용이 낮음
- 새 branch를 만든다는 것은 commit snapshot 전체를 복사하는 일이 아님
- 40자 checksum과 newline을 file에 기록하는 정도의 작업
- 따라서 branch 생성은 41 bytes를 file에 쓰는 것처럼 빠르고 단순함

> [!quote]
>
> This is in sharp contrast to the way most older VCS tools branch, which involves copying all of the project’s files into a second directory. This can take several seconds or even minutes, depending on the size of the project, whereas in Git the process is always instantaneous. Also, because we’re recording the parents when we commit, finding a proper merge base for merging is automatically done for us and is generally very easy to do. These features help encourage developers to create and use branches often.

Git branch의 가벼움은 오래된 VCS의 branch 방식과 대조됨
- 많은 오래된 VCS는 branch를 만들 때 project file 전체를 두 번째 directory로 복사함
- project 크기에 따라 몇 초에서 몇 분까지 걸릴 수 있음
- Git에서는 branch 생성이 항상 거의 즉시 끝남

Git은 commit할 때 parent 정보를 함께 기록함
- 그래서 merge할 때 적절한 merge base를 찾기 쉬움
	- 이 과정은 Git이 자동으로 처리함

branch 생성 비용이 낮고 merge base 계산도 쉬우므로, Git은 branch를 자주 만들고 사용하는 workflow를 장려함

> [!quote] Note - Creating a new branch and switching to it at the same time
>
> It’s typical to create a new branch and want to switch to that new branch at the same time — this can be done in one operation with `git checkout -b <newbranchname>`.

새 branch를 만들면서 동시에 그 branch로 이동하는 경우가 많음

이 작업은 `git checkout -b <newbranchname>`으로 한 번에 처리할 수 있음
- 새 branch 생성
- 생성한 branch로 checkout

> [!quote] Note - git switch
>
> From Git version 2.23 onwards you can use git switch instead of git checkout to:
>
> Switch to an existing branch: `git switch testing-branch`.
>
> Create a new branch and switch to it: `git switch -c new-branch`. The `-c` flag stands for create, you can also use the full flag: --create.
>
> Return to your previously checked out branch: `git switch -`.

Git 2.23부터는 branch 전환에 `git checkout` 대신 `git switch`를 사용할 수 있음

`git switch` 사용 예
- 기존 branch로 이동: `git switch testing-branch`
- 새 branch를 만들고 이동: `git switch -c new-branch`
	- `-c`는 create를 뜻함
	- 긴 option은 `--create`
- 직전에 checkout했던 branch로 돌아가기: `git switch -`
