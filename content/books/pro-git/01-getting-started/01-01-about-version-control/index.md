---
tags:
  - books
  - pro-git
aliases:
  - 1.1 Getting Started - About Version Control
---
# 1.1 Getting Started - About Version Control

## About Version Control

> [!quote]
> 
> What is “version control”, and why should you care? Version control is a system that records changes to a file or set of files over time so that you can recall specific versions later.

**Version control**
- 시간에 따른 파일들의 변화를 기록하는 시스템

> [!quote]
> 
> If you are a graphic or web designer and want to keep every version of an image or layout (which you would most certainly want to), a Version Control System (VCS) is a very wise thing to use. It allows you to revert selected files back to a previous state, revert the entire project back to a previous state, compare changes over time, see who last modified something that might be causing a problem, who introduced an issue and when, and more. Using a VCS also generally means that if you screw things up or lose files, you can easily recover. In addition, you get all this for very little overhead.

version control 로 다음을 수행할 수 있음
- 파일을 이전 상태로 되돌리기
- 시간에 걸친 변화를 추적하기
- 누가 만든 변화가 문제가 있는지 찾기
- 작업물 복구

### Local Version Control Systems

> [!quote]
> 
>  Many people’s version-control method of choice is to copy files into another directory (perhaps a time-stamped directory, if they’re clever). This approach is very common because it is so simple, but it is also incredibly error prone. It is easy to forget which directory you’re in and accidentally write to the wrong file or copy over files you don’t mean to.

파일을 직접 복사하는 방식은 오류가 발생하기 쉬움
- "error prone"
- 실수하기 좋은 구조

> [!quote]
> 
> To deal with this issue, programmers long ago developed local VCSs that had a simple database that kept all the changes to files under revision control. One of the most popular VCS tools was a system called RCS, which is still distributed with many computers today. [RCS](https://www.gnu.org/software/rcs/) works by keeping patch sets (that is, the differences between files) in a special format on disk; it can then re-create what any file looked like at any point in time by adding up all the patches.
 
*Local version control* 의 예로 RCS가 있음

RCS의 동작은 아래와 같음
- 파일들의 차이(patch set)을 디스크에 저장
- 필요한 시점에 patch set을 순차적으로 적용해 특정 시점의 파일 모습을 복원

### Centralized Version Control Systems

> [!quote]
> 
> The next major issue that people encounter is that they need to collaborate with developers on other systems. To deal with this problem, Centralized Version Control Systems (CVCSs) were developed. These systems (such as CVS, Subversion, and Perforce) have a single server that contains all the versioned files, and a number of clients that check out files from that central place. For many years, this has been the standard for version control

CVCS의 동작은 아래와 같음
- 단일 서버에 모든 버젼 관리되는 파일을 저장
- 클라이언트는 단일 서버(중앙)의 파일을 자기 컴퓨터로 내려받아 작업

"check out"
- 중앙 서버에서 파일을 받아 작업 사본을 만드는 개념
	- 전체 히스토리가 아니라 현재 작업할 파일 상태를 가져옴

> [!quote]
> 
> This setup offers many advantages, especially over local VCSs. For example, everyone knows to a certain degree what everyone else on the project is doing. Administrators have fine-grained control over who can do what, and it’s far easier to administer a CVCS than it is to deal with local databases on every client.

CVCS의 장점
- 프로젝트 참여자들이 다른 사람들이 무엇을 하는지 알기 쉬움
	- 중앙 서버가 있으니까 변경 이력, 현재 상태를 **공유**하기 쉬움
- 중앙 서버의 관리자가 누가 무엇을 했는지 세밀하게 제어 가능

> [!quote]
> 
> However, this setup also has some serious downsides. The most obvious is the single point of failure that the centralized server represents. If that server goes down for an hour, then during that hour nobody can collaborate at all or save versioned changes to anything they’re working on. If the hard disk the central database is on becomes corrupted, and proper backups haven’t been kept, you lose absolutely everything — the entire history of the project except whatever single snapshots people happen to have on their local machines. Local VCSs suffer from this same problem — whenever you have the entire history of the project in a single place, you risk losing everything.

CVCS의 단점
- 단일 장애 지점
	- 중앙 서버가 다운되면 그 동안 협업 불가

### Distributed Version Control Systems

> [!quote]
> 
> This is where Distributed Version Control Systems (DVCSs) step in. In a DVCS (such as Git, Mercurial or Darcs), clients don’t just check out the latest snapshot of the files; rather, they fully mirror the repository, including its full history. Thus, if any server dies, and these systems were collaborating via that server, any of the client repositories can be copied back up to the server to restore it. Every clone is really a full backup of all the data.

DVCS의 동작은 아래와 같음
- 클라이언트는 리포지토리(히스토리 포함)을 전체 복사해서 가져옴
	- CVCS에서 클라이언트는 현재 작업할 파일 상태만 가져왔음
- 원격 서버가 다운되거나 날아가도, 누군가의 로컬에 리포지토리의 클론이 남아 있기에 복구할 수 있음

> [!quote]
> 
> Furthermore, many of these systems deal pretty well with having several remote repositories they can work with, so you can collaborate with different groups of people in different ways simultaneously within the same project. This allows you to set up several types of workflows that aren’t possible in centralized systems, such as hierarchical models.

DVCS는 **여러** 원격 리포지토리를 다룰 수 있음
- 같은 프로젝트 안에서도 서로 다른 그룹이 서로 다른 방식으로 협업할 수 있음
- CVCS에서는 불가능한 계층형 모델
