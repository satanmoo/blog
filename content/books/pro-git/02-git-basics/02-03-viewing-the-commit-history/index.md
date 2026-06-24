---
tags:
  - books
  - pro-git
aliases:
  - 2.3 Git Basics - Viewing the Commit History
references:
  - https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History
---
# 2.3 Git Basics - Viewing the Commit History

## Viewing the Commit History

> [!quote]
> 
> After you have created several commits, or if you have cloned a repository with an existing commit history, you’ll probably want to look back to see what has happened. The most basic and powerful tool to do this is the `git log` command.

commit history를 보는 가장 기본 도구가 `git log`
- history는 다음 결과로 얻을 수 있음
	- 작업하고 commit 쌓기
		- commit을 쌓으면 그 기록이 [[books/pro-git/01-getting-started/01-03-what-is-git/index#Git Generally Only Adds Data|history로 계속 남음]]
	- repository clone
		- clone한 repository는 [[books/pro-git/02-git-basics/02-01-getting-a-git-repository/index#Cloning an Existing Repository|history를 그대로 가져옴]]

> [!quote]
> 
> By default, with no arguments, `git log` lists the commits made in that repository in reverse chronological order; that is, the most recent commits show up first. As you can see, this command lists each commit with its SHA-1 checksum, the author's name and email, the date written, and the commit message.

인자 없이 `git log`를 실행하면 다음과 같이 동작
- repository의 commit들을 역순(reverse chronological order)으로 나열
	- 최신 commit이 가장 위에 표시됨
- 각 commit에 대해 다음 정보를 표시
	- SHA-1 checksum
	- author 이름 / email
	- 작성 날짜
	- commit message

> [!quote]
> 
> One of the more helpful options is `-p` or `--patch`, which shows the difference (the patch output) introduced in each commit. You can also limit the number of log entries displayed, such as using `-2` to show only the last two entries.

`git log`의 자주 쓰는 옵션
- `-p` 또는 `--patch`
	- 각 commit이 ==도입한 변경(diff)을 함께 표시==
	- 출력 형식은 [[books/pro-git/02-git-basics/02-02-recording-changes-to-the-repository/index#Viewing Your Staged and Unstaged Changes|git diff]]와 동일
- `-N` (숫자)
	- 표시할 log 항목 수 제한
	- 예: `-2`는 최근 두 commit만 표시

```zsh
$ git log -p -2
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    Change version number

diff --git a/Rakefile b/Rakefile
index a874b73..8f94139 100644
--- a/Rakefile
+++ b/Rakefile
@@ -5,7 +5,7 @@ require 'rake/gempackagetask'
 spec = Gem::Specification.new do |s|
     s.platform  =   Gem::Platform::RUBY
     s.name      =   "simplegit"
-    s.version   =   "0.1.0"
+    s.version   =   "0.1.1"
     s.author    =   "Scott Chacon"
     s.email     =   "schacon@gee-mail.com"
     s.summary   =   "A simple gem for using Git in Ruby code."

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    Remove unnecessary test

diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index a0a60ae..47c6340 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -18,8 +18,3 @@ class SimpleGit
     end

 end
-
-if $0 == __FILE__
-  git = SimpleGit.new
-  puts git.show
-end
```

> [!quote]
> 
> This option displays the same information but with a diff directly following each entry. This is very helpful for code review or to quickly browse what happened during a series of commits that a collaborator has added.

`git log -p`의 활용
- code review
- 협업자가 쌓아놓은 일련의 commit을 빠르게 훑어보기

> [!quote]
> 
> You can also use a series of summarizing options with `git log`. For example, if you want to see some abbreviated stats for each commit, you can use the `--stat` option:

`git log`은 요약(summarizing) 옵션들도 제공
- `--stat`
	- 각 commit에 대해 ==축약된 통계(abbreviated stats)를 표시==

```zsh
$ git log --stat
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    Change version number

 Rakefile | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    Remove unnecessary test

 lib/simplegit.rb | 5 -----
 1 file changed, 5 deletions(-)

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    Initial commit

 README           |  6 ++++++
 Rakefile         | 23 +++++++++++++++++++++++
 lib/simplegit.rb | 25 +++++++++++++++++++++++++
 3 files changed, 54 insertions(+)
```

> [!quote]
> 
> As you can see, the `--stat` option prints below each commit entry a list of modified files, how many files were changed, and how many lines in those files were added and removed. It also puts a summary of the information at the end.

`--stat` 출력 형식
- 각 commit 항목 아래에 다음 정보를 표시
	- 수정된 file 목록
	- 변경된 file 수
	- 각 file에서 추가/삭제된 line 수
- 마지막에 전체 요약

> [!quote]
> 
> Another really useful option is `--pretty`. This option changes the log output to formats other than the default. A few prebuilt option values are available for you to use. The `oneline` value for this option prints each commit on a single line, which is useful if you're looking at a lot of commits. In addition, the `short`, `full`, and `fuller` values show the output in roughly the same format but with less or more information, respectively:

- `--pretty`
	- ==log 출력 형식을 default 외 다른 형식으로 변경==
	- prebuilt option value
		- `oneline`
			- 각 commit을 한 줄로 출력
			- 많은 commit을 훑어볼 때 유용
		- `short` / `full` / `fuller`
			- `oneline`과 유사한 형식, 정보량의 정도가 다름

```zsh
$ git log --pretty=oneline
ca82a6dff817ec66f44342007202690a93763949 Change version number
085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 Remove unnecessary test
a11bef06a3f659402fe7563abf99ad00de2209e6 Initial commit
```

> [!quote]
> 
> The most interesting option value is `format`, which allows you to specify your own log output format. This is especially useful when you're generating output for machine parsing — because you specify the format explicitly, you know it won't change with updates to Git:

- `format` 옵션
	- ==직접 log 출력 형식을 지정==
	- 형식을 명시적으로 지정하므로 Git update에도 출력 결과가 바뀌지 않음
		- `git log`의 기본 출력 형식은 Git 버전 사이에서 바뀔 수 있음
	- **machine parsing** 용도에 유용
		- 사람이 읽는 게 아니라 프로그램(스크립트)이 출력을 파싱해 처리하는 용도

```zsh
$ git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 6 years ago : Change version number
085bb3b - Scott Chacon, 6 years ago : Remove unnecessary test
a11bef0 - Scott Chacon, 6 years ago : Initial commit
```

> [!quote] Table 1. Useful specifiers for `git log --pretty=format`
> 
> Useful specifiers for `git log --pretty=format` lists some of the more useful specifiers that `format` takes.

| Specifier | Description |
|---|---|
| `%H` | Commit hash |
| `%h` | Abbreviated commit hash |
| `%T` | Tree hash |
| `%t` | Abbreviated tree hash |
| `%P` | Parent hashes |
| `%p` | Abbreviated parent hashes |
| `%an` | Author name |
| `%ae` | Author email |
| `%ad` | Author date (format respects the `--date=` option) |
| `%ar` | Author date, relative |
| `%cn` | Committer name |
| `%ce` | Committer email |
| `%cd` | Committer date |
| `%cr` | Committer date, relative |
| `%s` | Subject |

> [!quote]
> 
> You may be wondering what the difference is between author and committer. The author is the person who originally wrote the work, whereas the committer is the person who last applied the work. So, if you send in a patch to a project and one of the core members applies the patch, both of you get credit — you as the author, and the core member as the committer. We'll cover this distinction a bit more in Distributed Git.

**author**
- 작업을 ==처음 작성한 사람==

**committer**
- 작업을 ==마지막으로 적용한 사람==

예: 누군가 project에 patch를 보내고 core member가 patch를 적용한 경우
- patch를 보낸 사람 → *author*
- patch를 적용한 core member → *committer*
- 두 사람 모두 credit을 받음

> [!note]
> 
> 이 차이는 Distributed Git 챕터에서 더 자세히 다룸

> [!quote]
> 
> The `oneline` and `format` option values are particularly useful with another log option called `--graph`. This option adds a nice little ASCII graph showing your branch and merge history:

- `--graph`
	- ==branch와 merge history를 ASCII graph로 표시==
	- `oneline`, `format`과 조합해 사용하면 유용

```zsh
$ git log --pretty=format:"%h %s" --graph
* 2d3acf9 Ignore errors from SIGCHLD on trap
*  5e3ee11 Merge branch 'master' of https://github.com/dustin/grit.git
|\
| * 420eac9 Add method for getting the current branch
* | 30e367c Timeout code and tests
* | 5a09431 Add timeout protection to grit
* | e1193f8 Support for heads with slashes in them
|/
* d6016bc Require time for xmlschema
*  11d191e Merge branch 'defunkt' into local
```

> [!quote]
> 
> This type of output will become more interesting as we go through branching and merging in the next chapter.

다음 챕터에서 **branching·merging**을 다루면 이 출력 형식의 활용도가 더 커짐

> [!quote] Table 2. Common options to `git log`
> 
> Those are only some simple output-formatting options to `git log` — there are many more. Common options to `git log` lists the options we've covered so far, as well as some other common formatting options that may be useful, along with how they change the output of the log command.

흔하게 사용하는 옵션 cheat sheet는 아래와 같음

| Option | Description |
|---|---|
| `-p` | Show the patch introduced with each commit. |
| `--stat` | Show statistics for files modified in each commit. |
| `--shortstat` | Display only the changed/insertions/deletions line from the `--stat` command. |
| `--name-only` | Show the list of files modified after the commit information. |
| `--name-status` | Show the list of files affected with added/modified/deleted information as well. |
| `--abbrev-commit` | Show only the first few characters of the SHA-1 checksum instead of all 40. |
| `--relative-date` | Display the date in a relative format (for example, "2 weeks ago") instead of using the full date format. |
| `--graph` | Display an ASCII graph of the branch and merge history beside the log output. |
| `--pretty` | Show commits in an alternate format. Option values include `oneline`, `short`, `full`, `fuller`, and `format` (where you specify your own format). |
| `--oneline` | Shorthand for `--pretty=oneline --abbrev-commit` used together. |

### Limiting Log Output

> [!quote]
> 
> In addition to output-formatting options, `git log` takes a number of useful limiting options; that is, options that let you show only a subset of commits. You've seen one such option already — the `-2` option, which displays only the last two commits. In fact, you can do `-<n>`, where n is any integer to show the last `n` commits. In reality, you're unlikely to use that often, because Git by default pipes all output through a pager so you see only one page of log output at a time.

앞에서 다룬 옵션은 모두 출력 형식과 관련된 것들이었음

`git log`은 **limiting option**도 제공
- ==commit의 일부 집합만 표시==하는 옵션
- 일반화 형식: `-<n>` — `n`은 임의의 정수, 최근 `n`개 commit만 표시
	- 앞에서 본 `-2`도 이 형식의 예

> [!note]
> 
> `-<n>`은 실제로는 자주 쓰이지 않음
> 
> Git이 기본적으로 출력을 **pager**(긴 출력을 페이지 단위로 보여주는 프로그램; `less`, `more` 등)로 파이프하므로 한 페이지씩만 보이게 됨 — 굳이 `-<n>`으로 제한할 필요가 적음

> [!quote]
> 
> However, the time-limiting options such as `--since` and `--until` are very useful. For example, this command gets the list of commits made in the last two weeks:
> 
> `$ git log --since=2.weeks`
> 
> This command works with lots of formats — you can specify a specific date like "2008-01-15", or a relative date such as "2 years 1 day 3 minutes ago".

*limiting option* 중 **time-limiting option**은 자주 쓰임
- `--since` / `--until`

```zsh
$ git log --since=2.weeks
```

다양한 날짜 형식을 인식
- 절대 날짜: `"2008-01-15"`
- 상대 날짜: `"2.weeks"`, `"2 years 1 day 3 minutes ago"` 등

> [!quote]
> 
> You can also filter the list to commits that match some search criteria. The `--author` option allows you to filter on a specific author, and the `--grep` option lets you search for keywords in the commit messages.

검색 조건으로 commit list를 필터링 가능
- `--author`
	- 특정 *author*만 필터링
- `--grep`
	- commit message에서 키워드 검색

> [!quote] Note
> 
> You can specify more than one instance of both the `--author` and `--grep` search criteria, which will limit the commit output to commits that match any of the `--author` patterns and any of the `--grep` patterns; however, adding the `--all-match` option further limits the output to just those commits that match all `--grep` patterns.

`--author` / `--grep` 조합 동작
- 각 옵션을 여러 번 지정 가능
- 한 옵션 내 여러 패턴은 ==하나라도 매칭==되면 포함 (OR)
- `--all-match` 추가 시
	- `--grep` 패턴 ==모두 매칭==되는 commit만 포함 (AND)

특정 *author*의 commit만 표시
```zsh
$ git log --author="Scott Chacon"
```

commit message에 `"fix"`를 포함한 commit만 표시
```zsh
$ git log --grep="fix"
```

`--grep`을 여러 번 지정 (기본 OR)
```zsh
$ git log --grep="fix" --grep="bug"
```
- `"fix"` 또는 `"bug"` 중 하나라도 포함한 commit이 모두 표시됨

`--grep`과 `--author` 조합 (서로 다른 옵션 사이는 AND)

```zsh
$ git log --grep="fix" --author="Scott Chacon"
```

`--all-match` 추가 (AND)
```zsh
$ git log --grep="fix" --grep="bug" --all-match
```
- `"fix"`와 `"bug"`를 ==모두 포함==하는 commit만 표시됨

> [!quote]
> 
> Another really helpful filter is the `-S` option (colloquially referred to as Git's "pickaxe" option), which takes a string and shows only those commits that changed the number of occurrences of that string. For instance, if you wanted to find the last commit that added or removed a reference to a specific function, you could call:
> 
> `$ git log -S function_name`

- `-S` 옵션 (속칭 **pickaxe**)
	- 인자로 문자열을 받음
	- ==그 문자열의 등장 횟수(occurrences)가 변한 commit만 표시==
	- 활용 예: 특정 함수에 대한 참조가 추가/제거된 시점 찾기

```zsh
$ git log -S function_name
```

> [!quote]
> 
> The last really useful option to pass to `git log` as a filter is a path. If you specify a directory or file name, you can limit the log output to commits that introduced a change to those files. This is always the last option and is generally preceded by double dashes (`--`) to separate the paths from the options:
> 
> `$ git log -- path/to/file`

path filter
- directory 또는 file name을 지정
- ==해당 path에 변경을 도입한 commit만 표시==
- 항상 명령 끝에 위치
- `--` (double dash)를 앞에 붙여 option과 path를 구분 ^double-dash-separator

```zsh
$ git log -- path/to/file
```

> [!quote] Table 3. Options to limit the output of `git log`
> 
> In Options to limit the output of `git log` we'll list these and a few other common options for your reference.

| Option | Description |
|---|---|
| `-<n>` | Show only the last `n` commits. |
| `--since`, `--after` | Limit the commits to those made after the specified date. |
| `--until`, `--before` | Limit the commits to those made before the specified date. |
| `--author` | Only show commits in which the author entry matches the specified string. |
| `--committer` | Only show commits in which the committer entry matches the specified string. |
| `--grep` | Only show commits with a commit message containing the string. |
| `-S` | Only show commits adding or removing code matching the string. |

> [!tip] Preventing the display of merge commits
> 
> Depending on the workflow used in your repository, it's possible that a sizable percentage of the commits in your log history are just merge commits, which typically aren't very informative. To prevent the display of merge commits cluttering up your log history, simply add the log option `--no-merges`.

- `--no-merges`
	- ==merge commit을 출력에서 제외==
	- merge commit은 정보량이 적은 경우가 많아 log를 어지럽힐 수 있음
