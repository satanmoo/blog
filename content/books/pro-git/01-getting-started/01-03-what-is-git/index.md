---
tags:
  - books
  - pro-git
aliases:
  - 1.3 Getting Started - What is Git?
---
# 1.3 Getting Started - What is Git?

## What is Git?

### Snapshots, Not Differences

> [!quote]
> 
> The major difference between Git and any other VCS (Subversion and friends included) is the way Git thinks about its data. Conceptually, most other systems store information as a list of file-based changes. These other systems (CVS, Subversion, Perforce, and so on) think of the information they store as a set of files and the changes made to each file over time (this is commonly described as _delta-based_ version control).

다른 대부분의 VCS는 아래와 같이 동작
- 파일을 기준으로 시간에 따라 생긴 차이(delta)를 저장

![[what-is-git-001.png]]

> [!quote]
> 
> Git doesn’t think of or store its data this way. Instead, Git thinks of its data more like a series of snapshots of a miniature filesystem. With Git, every time you commit, or save the state of your project, Git basically takes a picture of what all your files look like at that moment and stores a reference to that snapshot. To be efficient, if files have not changed, Git doesn’t store the file again, just a link to the previous identical file it has already stored. Git thinks about its data more like a **stream of snapshots**.

Git은 아래와 같이 동작
- 스냅샷을 저장
	- 순간의 모든 파일의 모습을 스냅샷으로 찍고, 그 스냅샷에 대한 참조를 저장
- file이 변경되지 않았으면, file을 다시 저장하지 않음
	- 효율성 때문
	- 대신 이전에 저장해둔 스냅샷에 링크

![[what-is-git-002.png]]

### Nearly Every Operation Is Local

> [!quote]
> 
> Most operations in Git need only local files and resources to operate — generally no information is needed from another computer on your network. If you’re used to a CVCS where most operations have that network latency overhead, this aspect of Git will make you think that the gods of speed have blessed Git with unworldly powers. Because you have the entire history of the project right there on your local disk, most operations seem almost instantaneous.

Git에서 대부분 작업은 로컬 파일, 리소스만 필요함
- CVCS에 속하는 다른 VCS에 비해 네트워크 지연 오버헤드가 없음
- 오프라인 작업 가능

### Git Has Integrity

> [!quote]
> 
> Everything in Git is checksummed before it is stored and is then referred to by that checksum. This means it’s impossible to change the contents of any file or directory without Git knowing about it. This functionality is built into Git at the lowest levels and is integral to its philosophy. You can’t lose information in transit or get file corruption without Git being able to detect it.

Git은 데이터를 저장할 때 checksum을 계산하고, 그 checksum을 기준으로 식별함
- 파일 내용, commit 내용을 바탕으로 해시값 생성
	- 내용을 바탕으로 해시값을 만듦
	- 내용이 바뀌면 Git이 알 수밖에 없음
		- 전송 중 파일이 손상되어도 알 수 있음

> [!quote]
> 
> The mechanism that Git uses for this checksumming is called a SHA-1 hash. This is a 40-character string composed of hexadecimal characters (0–9 and a–f) and calculated based on the contents of a file or directory structure in Git.

SHA-1은 해시 알고리듬 ^sha-1-hash
- Git은 ==파일의 내용==과 ==디렉토리 구조==를 기반으로 체크섬 생성

> [!quote]
>  
> Git stores everything in its database not by file name but by the hash value of its contents.
  
Git은 ==내용에 기반한== 해시값을 데이터베이스에 저장함

### Git Generally Only Adds Data

> [!quote]
> 
> When you do actions in Git, nearly all of them only _add_ data to the Git database. It is hard to get the system to do anything that is not undoable or to make it erase data in any way. As with any VCS, you can lose or mess up changes you haven’t committed yet, but after you commit a snapshot into Git, it is very difficult to lose, especially if you regularly push your database to another repository.

Git에서 작업을 하면, 거의 모든 작업은 Git database에 데이터를 추가
- 여기서 Git database는 `.git` 안의 object database

Git은 기존 데이터를 지우지 않고 새 데이터를 추가하는 방식
- 한 번 기록된 것을 되돌리거나 찾아내기 쉬움
- 위 내용은 커밋한 스냅샷에 적용됨
	- 커밋하지 않은 변경사항은 잃어버릴 수 있음
- 커밋을 다른 리포지토리에 주기적으로 푸시하면 더 잃어버리기 힘듦

### The Three States

Git이 관리하는 파일은 3가지 상태를 가짐
- **Modified**
- **Staged**
- **Committed**

> [!quote]
> 
> Modified means that you have changed the file but have not committed it to your database yet.

Modified:
- 변경사항은 있지만, 커밋하지 않아서 object database에 저장되지 않은 상태

> [!quote] 
> 
> Staged means that you have marked a modified file in its current version to go into your next commit snapshot.

Staged:
- 변경사항의 현재 버전을 다음 commit snapshot에 들어가도록 표시하는 개념

> [!quote]
> 
> Committed means that the data is safely stored in your local database.

Committed:
- snapshot으로 안전하게 local object database에 저장된 상태

![[what-is-git-003.png]]

> [!quote]
> 
> The working tree is a single checkout of one version of the project. These files are pulled out of the compressed database in the Git directory and placed on disk for you to use or modify.

**working tree(working directory)**:
- 특정 버전의 프로젝트를 checkout한 결과
	- 압축 해제된 파일이 사용하거나 수정 가능한 상태로 disk에 배치됨

checkout: ^checkout
- 특정 commit이 가리키는 파일 상태를 읽고, 압축 해제 후, 현재 디렉터리에 실제 파일들로 펼쳐놓는 동작
	- 이 파일 상태는 Git의 database에 object로 저장됨

> [!quote] [git-checkout](https://git-scm.com/docs/git-checkout)
> 
> git-checkout - Switch branches or restore working tree files

reference에서 위와 같은 checkout의 역할을 확인할 수 있음
- "restore"라는 용어가 특정 상태를 working directory의 실제 파일에 반영하는 개념

> [!quote]
> 
> The staging area is a file, generally contained in your Git directory, that stores information about what will go into your next commit. Its technical name in Git parlance is the “index”, but the phrase “staging area” works just as well.

**staging area**:
- 다음 커밋에 포함될 것들의 정보를 담은 파일
- `.git` 폴더에 위치함
- Git 용어로 **index** 라고 표현하기도 함

> [!quote]
> 
> The Git directory is where Git stores the metadata and object database for your project. This is the most important part of Git, and it is what is copied when you _clone_ a repository from another computer.

`.git` 디렉터리는 repository의 본체
- meta data 포함
- object database 포함
