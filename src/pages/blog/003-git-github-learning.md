---

layout: ../../layouts/BlogPost.astro
title: "Git · GitHub 학습 정리 — Commit부터 Branch, Merge, 복구까지"
description: "Git과 GitHub의 기본 개념부터 Commit, Branch, Merge, Pull Request, 충돌 해결, 복구, Stash, Secret 관리와 Tag까지 정리합니다."
date: "2026-08-18"
------------------

# Git · GitHub 학습 정리

Git과 GitHub를 이용한 기본적인 개발 흐름과 주요 명령어를 정리한다.

## 목차

**기본 개념**
- [Git과 GitHub](#git과-github)
- [Repository](#repository)
- [Working Directory와 Staging Area](#working-directory-staging-area-repository)
- [Status / Diff / Add / Commit](#git-status)

**원격 저장소**
- [Clone / Push / Fetch / Pull](#git-clone)
- [main과 origin/main](#main과-originmain)

**Branch와 협업**
- [Branch와 Merge](#branch)
- [Merge Conflict](#merge-conflict)
- [GitHub Issue와 Pull Request](#github-issue)

**복구와 작업 관리**
- [Restore / Reset / Revert](#git-restore)
- [Reflog](#git-reflog)
- [Stash](#git-stash)
- [Commit Amend](#git-commit-amend)

**보안과 선택적 작업**
- [.gitignore와 Secret 관리](#gitignore)
- [Cherry-pick](#git-cherry-pick)

**버전 관리**
- [Tag와 GitHub Release](#git-tag)

---

## Git과 GitHub

### Git

Git은 파일의 변경 이력을 관리하는 **분산 버전 관리 시스템**이다.

파일을 수정한 뒤 Commit을 만들면 특정 시점의 변경사항을 기록할 수 있다.

```text
파일 수정
   ↓
git add
   ↓
git commit
   ↓
변경 이력 저장
```

Git은 로컬 컴퓨터만으로도 사용할 수 있다.

### GitHub

GitHub는 Git Repository를 원격으로 저장하고 공유할 수 있는 서비스다.

```text
Local Repository
       ↕
   push / pull
       ↕
GitHub Repository
```

Git과 GitHub의 역할을 구분하면 다음과 같다.

```text
Git
= 버전 관리

GitHub
= Git 저장소의 원격 보관 및 협업
```

---

## Repository

Repository는 프로젝트 파일과 Git 변경 이력을 관리하는 저장소다.

GitHub에 있는 저장소는 **Remote Repository**, 컴퓨터에 Clone한 저장소는 **Local Repository**라고 볼 수 있다.

```text
GitHub
Remote Repository
       ↕
Local Repository
Windows PC
```

두 저장소는 항상 자동으로 동기화되는 것이 아니다.

변경사항은 `push`, `fetch`, `pull` 등을 통해 주고받는다.

---

## Working Directory, Staging Area, Repository

Git에서 변경사항은 바로 Commit되지 않는다.

기본 흐름은 다음과 같다.

```text
Working Directory
       ↓
    git add
       ↓
Staging Area
       ↓
  git commit
       ↓
Repository
```

### Working Directory

현재 직접 수정하고 있는 프로젝트 파일의 상태다.

### Staging Area

다음 Commit에 포함할 변경사항을 골라 놓는 중간 영역이다.

### Repository

Commit이 만들어지고 변경 이력이 기록되는 영역이다.

---

## Git Status

현재 Git 상태를 확인한다.

```powershell
git status
```

확인할 수 있는 내용:

* 현재 Branch
* 수정된 파일
* 새로 생성된 파일
* Staging된 파일
* Commit할 내용
* 원격 Branch와의 차이

Git 작업 전후에 가장 자주 사용하는 확인 명령어다.

---

## Git Diff

아직 Commit하지 않은 변경사항을 확인한다.

```powershell
git diff
```

두 Branch의 변경사항도 비교할 수 있다.

```powershell
git diff main..feature/example
```

---

## Git Add

변경된 파일을 Staging Area에 올린다.

```powershell
git add 파일명
```

예:

```powershell
git add README.md
```

여러 파일을 수정했더라도 원하는 파일만 골라 다음 Commit에 포함시킬 수 있다.

---

## Commit

Commit은 특정 시점의 변경사항을 Git 이력에 기록하는 작업이다.

```powershell
git commit -m "commit message"
```

Commit은 게임의 **세이브 포인트**처럼 이해할 수 있다.

```text
A ── B ── C
```

각 Commit에는 고유한 ID가 생성된다.

예:

```text
9b6616e
```

Commit 이력 확인:

```powershell
git log --oneline
```

Branch 구조까지 함께 확인:

```powershell
git log --oneline --decorate --graph --all
```
---

## Git Clone

GitHub에 있는 Repository를 내 컴퓨터로 복제할 때 사용한다.

```powershell
git clone <repository-url>
```

Clone을 하면 프로젝트 파일뿐 아니라 Git의 Commit 이력과 Branch 정보도 함께 가져온다.

```text
GitHub Repository
       ↓
   git clone
       ↓
Local Repository
```

---

## Git Push

로컬에서 만든 Commit을 GitHub의 원격 Repository로 전송한다.

```powershell
git push
```

처음 만든 Branch를 GitHub에 올리면서 upstream까지 연결할 때는 다음과 같이 사용할 수 있다.

```powershell
git push -u origin <branch-name>
```

`-u` 옵션으로 로컬 Branch와 원격 Branch가 연결되면 이후에는 같은 Branch에서 다음 명령만 사용해도 된다.

```powershell
git push
```

```text
Local Repository
       ↓
     push
       ↓
GitHub Repository
```

---

## Git Fetch

GitHub의 최신 Commit과 Branch 정보를 가져온다.

```powershell
git fetch origin
```

중요한 점은 `fetch`만 실행하면 **현재 작업 중인 로컬 Branch나 파일은 변경되지 않는다는 것**이다.

```text
GitHub main
     ↓
 git fetch
     ↓
origin/main 갱신

local main은 그대로
```

원격 저장소에 어떤 변경이 생겼는지 먼저 확인하고 싶을 때 사용할 수 있다.

---

## Git Pull

원격 저장소의 최신 변경사항을 가져와 현재 로컬 Branch에 반영한다.

```powershell
git pull
```

개념적으로는 다음과 같이 이해할 수 있다.

```text
원격 변경사항 확인
       ↓
로컬 Branch에 반영
```

`fetch`와 비교하면 차이가 명확하다.

```text
git fetch
= 원격의 최신 정보를 가져옴
  현재 Branch는 그대로

git pull
= 원격의 최신 정보를 가져오고
  현재 Branch에도 반영
```

---

## main과 origin/main

Git을 사용할 때 `main`과 `origin/main`은 서로 다른 의미를 가진다.

### main

내 컴퓨터에 존재하는 **로컬 Branch**다.

```text
main
= 내 PC에서 실제로 작업하는 Branch
```

### origin/main

내 컴퓨터가 알고 있는 **원격 저장소 main의 상태를 나타내는 Remote-tracking Branch**다.

```text
origin
= 연결된 원격 저장소의 기본 이름

origin/main
= 마지막으로 확인한 원격 저장소의 main 상태
```

GitHub의 `main`에 새로운 Commit이 생겼다고 해서 내 컴퓨터의 `origin/main`이 즉시 자동으로 변경되는 것은 아니다.

```text
GitHub 실제 main
       ↓
   git fetch
       ↓
origin/main 갱신
```

그 후 로컬 `main`까지 최신 상태로 맞추려면 상황에 따라 `git pull` 등을 사용한다.

현재 위치와 Branch 포인터를 함께 확인할 때는 다음 명령이 유용하다.

```powershell
git log --oneline --decorate --graph --all
```

예:

```text
abc1234 (HEAD -> main, origin/main, origin/HEAD)
```

여기서:

```text
HEAD
= 현재 내가 위치한 곳

main
= 로컬 main Branch

origin/main
= 원격 main을 추적하는 정보

origin/HEAD
= 원격 저장소의 기본 Branch가 무엇인지 나타내는 참조
```
---

## Branch

Branch는 기존 개발 흐름에서 분리된 별도의 작업 공간이다.

```text
main
A ── B ── C
         \
          D ── E
          feature
```

새 기능 개발, 버그 수정, 문서 작성 등을 `main`과 분리해서 진행할 수 있다.

새 Branch를 만들면서 바로 이동:

```powershell
git switch -c <branch-name>
```

예:

```powershell
git switch -c feature/boss-timer
```

현재 Branch 목록 확인:

```powershell
git branch
```

다른 Branch로 이동:

```powershell
git switch main
```

Branch를 사용하면 작업 중인 변경사항을 `main`에 바로 넣지 않고 독립적으로 Commit한 뒤 검토할 수 있다.

---

## Branch 비교

두 Branch 사이의 파일 변경사항을 비교할 수 있다.

```powershell
git diff main..feature/example
```

특정 Branch에만 존재하는 Commit을 확인할 수도 있다.

```powershell
git log --oneline main..feature/example
```

예를 들어:

```text
main
A ── B

feature/example
A ── B ── C
```

이라면 다음 명령은:

```powershell
git log --oneline main..feature/example
```

`C` Commit을 보여준다.

---

## Merge

Merge는 한 Branch에서 만든 변경사항을 다른 Branch에 합치는 작업이다.

일반적으로 기능 Branch의 작업이 끝나면 `main`으로 이동한 뒤 Merge한다.

```powershell
git switch main
git merge feature/example
```

---

## Fast-forward Merge

`main`에서 추가 작업이 없고 기능 Branch만 앞으로 진행된 경우에는 Fast-forward Merge가 가능하다.

Merge 전:

```text
A ── B  main
     \
      C ── D  feature
```

실제로는 같은 직선상의 Commit이므로 Merge 후 `main` 포인터만 앞으로 이동한다.

```text
A ── B ── C ── D
               ↑
             main
```

별도의 Merge Commit은 만들어지지 않는다.

이 방식을 **Fast-forward Merge**라고 한다.

---

## Merge Commit

두 Branch가 각각 별도의 Commit을 가진 상태라면 단순히 Branch 포인터만 이동할 수 없다.

예:

```text
       C ── D  feature
      /
A ── B
      \
       E       main
```

이 상태에서:

```powershell
git switch main
git merge feature
```

를 실행하면 두 개발 흐름을 하나로 합치는 새로운 Merge Commit이 만들어질 수 있다.

```text
       C ── D
      /      \
A ── B        M
      \      /
       E ────
```

여기서 `M`이 Merge Commit이다.

Merge Commit은:

```text
두 Branch의 개발 흐름이
이 시점에서 합쳐졌다는 기록
```

이라고 볼 수 있다.

Commit 그래프를 확인하면 Merge 구조를 쉽게 볼 수 있다.

```powershell
git log --oneline --decorate --graph --all
```

---

## Branch 삭제

작업이 Merge된 뒤 더 이상 필요하지 않은 로컬 Branch는 삭제할 수 있다.

```powershell
git branch -d <branch-name>
```

`-d`는 아직 Merge되지 않은 Branch를 실수로 삭제하는 것을 막아준다.

강제로 삭제하려면:

```powershell
git branch -D <branch-name>
```

를 사용할 수 있지만, Merge되지 않은 Commit까지 잃을 수 있으므로 주의해서 사용해야 한다.

원격 GitHub Branch 삭제:

```powershell
git push origin --delete <branch-name>
```

Branch를 삭제해도 이미 Merge되어 Commit 이력에 들어간 변경사항까지 사라지는 것은 아니다.

---

## Merge Conflict

Merge Conflict는 Git이 두 Branch의 변경사항을 자동으로 합칠 수 없을 때 발생한다.

대표적인 경우는 **같은 파일의 같은 부분을 서로 다르게 수정했을 때**다.

예를 들어 `main`에서는:

```text
Main version
```

으로 수정하고, 다른 Branch에서는:

```text
Branch version
```

으로 수정했다고 하자.

두 Branch를 Merge하면 Git은 어느 내용을 남겨야 하는지 스스로 결정할 수 없다.

```text
main
A ── B ── M

branch
     \
      C
```

이때 Merge를 실행하면:

```powershell
git merge <branch-name>
```

다음과 비슷한 메시지가 나타날 수 있다.

```text
CONFLICT (add/add): Merge conflict in conflict-practice.txt
Automatic merge failed; fix conflicts and then commit the result.
```

---

## 충돌 상태 확인

충돌이 발생하면 먼저 상태를 확인한다.

```powershell
git status
```

예:

```text
Unmerged paths:

  both added: conflict-practice.txt
```

`both added`, `both modified` 등의 표시를 통해 어떤 파일에서 충돌이 발생했는지 확인할 수 있다.

---

## Conflict Marker

충돌이 발생한 파일을 열면 Git이 다음과 같은 표시를 넣어둔다.

```text
 <<<<<<< HEAD
Main version
 =======
Branch version
 >>>>>>> feature/example
```

각 부분의 의미는 다음과 같다.

```text
 <<<<<<< HEAD
현재 내가 위치한 Branch의 내용

 =======
두 변경사항을 구분하는 선

 >>>>>>> feature/example
Merge하려는 상대 Branch의 내용
```

이 표시는 오류 메시지가 아니라 **어떤 두 내용이 충돌했는지 보여주는 표시**다.

---

## 충돌 해결

개발자가 직접 최종적으로 사용할 내용을 결정해야 한다.

예를 들어 최종 내용을:

```text
Resolved version
```

으로 결정했다면 충돌 표시를 모두 제거하고 파일을 다음처럼 수정한다.

```text
Resolved version
```

중요한 점은 다음과 같은 Marker가 파일에 남아 있으면 안 된다는 것이다.

```text
 <<<<<<<
 =======
 >>>>>>>
```

---

## 해결한 파일 Staging

충돌을 수정했다고 Git이 자동으로 해결 완료로 판단하는 것은 아니다.

수정한 파일을 다시 Staging해야 한다.

```powershell
git add conflict-practice.txt
```

그다음 상태를 확인한다.

```powershell
git status
```

정상적으로 해결되었다면 더 이상 `Unmerged paths`가 표시되지 않는다.

---

## Merge 완료

충돌을 모두 해결하고 Staging한 뒤 Commit하면 Merge가 완료된다.

```powershell
git commit
```

또는 Commit 메시지를 직접 지정할 수도 있다.

```powershell
git commit -m "merge: resolve conflict"
```

전체 흐름은 다음과 같다.

```text
git merge
    ↓
Conflict 발생
    ↓
git status
    ↓
충돌 파일 확인
    ↓
 <<<<<<< / ======= / >>>>>>> 확인
    ↓
최종 내용으로 직접 수정
    ↓
git add
    ↓
git commit
    ↓
Merge 완료
```

---

## Merge Conflict의 핵심

Merge Conflict 자체는 Git이 고장 난 상태가 아니다.

```text
Git:
"두 변경사항 중 무엇이 맞는지
내가 판단할 수 없으니 개발자가 결정해 주세요."
```

라는 의미에 가깝다.

따라서 충돌이 발생했을 때 무작정 파일을 삭제하기보다 먼저:

```powershell
git status
```

로 충돌 파일을 확인하고, 파일 안의 Conflict Marker를 읽은 뒤 해결하는 것이 중요하다.

---

## GitHub Issue

Issue는 해야 할 작업, 버그, 개선사항 등을 기록하는 공간이다.

예를 들어:

```text
Issue #1
Boss Timer 기본 화면 만들기
```

처럼 작업 단위를 먼저 기록할 수 있다.

Issue를 사용하면:

```text
무엇을 해야 하는지
왜 필요한지
어디까지 완료했는지
```

를 나중에 다시 확인하기 쉽다.

---

## Issue를 기준으로 Branch 만들기

작업을 시작할 때 `main`에서 바로 수정하기보다 별도 Branch를 만든다.

예:

```powershell
git switch -c feature/1-boss-timer
```

Branch 이름에 Issue 번호를 넣으면 어떤 작업과 연결된 Branch인지 알아보기 쉽다.

```text
Issue #1
   ↓
feature/1-boss-timer
```

---

## 작업 후 Commit

파일을 수정한 뒤 상태를 확인한다.

```powershell
git status
```

변경사항을 확인한다.

```powershell
git diff
```

Staging:

```powershell
git add <file-name>
```

Commit:

```powershell
git commit -m "feat: add boss timer"
```

---

## Branch를 GitHub에 Push

처음 Push하는 Branch는 다음과 같이 사용할 수 있다.

```powershell
git push -u origin feature/1-boss-timer
```

이제 GitHub에도 같은 Branch가 생성된다.

```text
Local Branch
feature/1-boss-timer
       ↓
      push
       ↓
GitHub Branch
feature/1-boss-timer
```

---

## Pull Request

Pull Request, 줄여서 PR은:

```text
이 Branch에서 만든 변경사항을
main에 합쳐도 되는지 검토해 주세요.
```

라는 요청이다.

보통 다음처럼 설정한다.

```text
base: main
compare: feature/1-boss-timer
```

즉:

```text
feature/1-boss-timer
        ↓
      Pull Request
        ↓
       main
```

---

## PR에서 확인할 내용

PR을 만든 뒤 바로 Merge하기보다 변경사항을 먼저 확인한다.

특히 `Files changed`에서:

```text
원하지 않는 파일이 포함되지 않았는지
코드가 예상대로 변경됐는지
비밀번호나 Token 같은 Secret이 없는지
실수로 임시 파일을 올리지 않았는지
```

를 확인하는 습관이 중요하다.

---

## Issue와 PR 연결

PR 본문에 다음처럼 작성할 수 있다.

```text
Closes #1
```

이렇게 하면 PR이 Merge될 때 연결된 Issue도 자동으로 닫을 수 있다.

```text
Issue #1
   ↓
Branch
   ↓
Commit
   ↓
Push
   ↓
Pull Request
   ↓
Merge
   ↓
Issue Close
```

---

## PR Merge

검토가 끝나면 PR을 `main`에 Merge한다.

Merge가 완료되면 GitHub의 `main`에는 작업 Branch의 변경사항이 포함된다.

하지만 내 컴퓨터의 `main`은 자동으로 최신 상태가 되지 않는다.

---

## 로컬 main 동기화

GitHub에서 PR을 Merge한 뒤 로컬에서:

```powershell
git switch main
```

그다음:

```powershell
git fetch origin
```

을 사용하면 원격 상태를 먼저 확인할 수 있다.

```powershell
git status
```

원격 `main`의 변경사항을 로컬 `main`에도 반영하려면:

```powershell
git pull
```

을 실행한다.

전체 흐름:

```text
GitHub PR Merge
       ↓
GitHub main 변경
       ↓
git fetch
       ↓
origin/main 갱신
       ↓
git pull
       ↓
local main 갱신
```

---

## 작업 Branch 정리

Merge가 끝난 Branch는 필요에 따라 삭제한다.

로컬 Branch:

```powershell
git branch -d feature/1-boss-timer
```

원격 Branch:

```powershell
git push origin --delete feature/1-boss-timer
```

Branch를 삭제해도 이미 `main`에 Merge된 Commit 이력은 유지된다.

---

## 실무 흐름 정리

GitHub를 이용한 기본적인 작업 흐름은 다음과 같이 정리할 수 있다.

```text
Issue 생성
   ↓
main 최신화
   ↓
작업 Branch 생성
   ↓
파일 수정
   ↓
git status / git diff
   ↓
git add
   ↓
git commit
   ↓
git push
   ↓
Pull Request 생성
   ↓
Files changed 검토
   ↓
Merge
   ↓
local main 동기화
   ↓
작업 Branch 삭제
```

이 흐름을 반복하면 `main`을 비교적 안정적으로 유지하면서 작업 이력을 관리할 수 있다.

---

## Git Restore

`git restore`는 주로 **Working Directory의 파일 변경사항을 되돌릴 때** 사용한다.

예를 들어 어떤 파일을 수정했지만 아직 Commit하지 않았다고 하자.

```text id="i6uw2h"
Original version
   ↓ 수정
Broken version
```

이 변경을 버리고 마지막 Commit 상태로 되돌리려면:

```powershell id="mws5kl"
git restore <file-name>
```

을 사용할 수 있다.

예:

```powershell id="xnvu5u"
git restore recovery-practice.txt
```

그러면 파일은 마지막 Commit 상태로 돌아간다.

```text id="95bii3"
Broken version
      ↓
 git restore
      ↓
Original version
```

주의할 점은 Working Directory에서 아직 Commit하지 않은 변경사항이 사라질 수 있다는 것이다.

---

## Staging 취소

이미 `git add`로 Staging한 파일을 다시 Working Directory 상태로 내릴 수도 있다.

```powershell id="40sc3y"
git restore --staged <file-name>
```

예:

```powershell id="i5kwou"
git restore --staged recovery-practice.txt
```

이 명령은 파일의 수정 내용을 삭제하지 않는다.

```text id="lfw5rl"
Working Directory 수정
        ↓
     git add
        ↓
  Staging Area
        ↓
git restore --staged
        ↓
Working Directory 수정 상태 유지
```

즉:

```text id="xs6uo7"
Staging만 취소
파일 내용은 그대로 유지
```

라고 이해하면 된다.

---

## Git Reset

`git reset`은 Branch의 Commit 위치를 이전으로 되돌릴 때 사용할 수 있다.

Reset에는 대표적으로:

```text id="ggitgt"
--soft
--mixed
--hard
```

세 가지 방식이 있다.

이 세 옵션의 가장 큰 차이는:

```text id="d35mfh"
Commit
Staging Area
Working Directory
```

를 어디까지 되돌리는가이다.

---

## git reset --soft

예:

```powershell id="ho8tlp"
git reset --soft HEAD~1
```

`HEAD~1`은 현재 Commit의 바로 이전 Commit을 의미한다.

```text id="uxyq1e"
A ── B ── C
        ↑
   HEAD~1
```

현재 `C`에서 실행하면 Branch 위치는 `B`로 돌아간다.

하지만 `C`에서 변경했던 내용은 **Staging된 상태로 유지된다.**

```text id="chq1s3"
Commit       되돌림
Staging      유지
파일 변경    유지
```

즉:

```text id="meifde"
Commit만 다시 만들고 싶을 때
```

유용할 수 있다.

---

## git reset --mixed

```powershell id="jz0fpo"
git reset --mixed HEAD~1
```

`--mixed`는 기본 Reset 방식이므로 다음과 같이 써도 같은 의미다.

```powershell id="1rvgds"
git reset HEAD~1
```

이 경우:

```text id="9yp9pl"
Commit       되돌림
Staging      취소
파일 변경    유지
```

즉 Commit은 사라지고, 변경한 파일은 다시 Working Directory의 수정 상태로 남는다.

```text id="42dj24"
Commit
   ↓ reset --mixed
수정된 파일
   ↓
다시 add와 commit 가능
```

---

## git reset --hard

```powershell id="xiyiu5"
git reset --hard HEAD~1
```

이 방식은 가장 주의해야 한다.

```text id="1vmdj4"
Commit       되돌림
Staging      되돌림
파일 변경    되돌림
```

즉 해당 Commit 이후의 변경사항을 Working Directory에서도 제거한다.

```text id="72zls4"
A ── B ── C
          ↑ HEAD

git reset --hard HEAD~1

A ── B
      ↑ HEAD
```

`C`에서 만든 변경사항도 파일에서 사라질 수 있다.

따라서 `--hard`를 사용하기 전에는:

```powershell id="ue6f6k"
git status
```

와:

```powershell id="wh27eu"
git log --oneline
```

을 먼저 확인하는 것이 안전하다.

---

## Reset 비교

세 방식은 다음처럼 정리할 수 있다.

```text id="1efll7"
reset --soft
Commit 되돌림
Staging 유지
Working Directory 유지

reset --mixed
Commit 되돌림
Staging 취소
Working Directory 유지

reset --hard
Commit 되돌림
Staging 취소
Working Directory도 되돌림
```

---

## Git Revert

`git revert`는 기존 Commit을 삭제하거나 Branch를 과거로 이동시키는 대신,

**기존 변경사항을 반대로 적용하는 새로운 Commit을 만든다.**

예:

```text id="qf4z1i"
A ── B ── C
```

`C`가 잘못된 Commit이라고 하자.

```powershell id="46674a"
git revert HEAD
```

를 실행하면:

```text id="gxowj9"
A ── B ── C ── R
```

처럼 새로운 Revert Commit `R`이 생성된다.

`C` Commit 자체는 기록에서 사라지지 않는다.

```text id="d0hx7f"
C
= 잘못된 변경을 만든 기록

R
= C의 변경을 되돌린 기록
```

---

## Reset과 Revert의 차이

Reset:

```text id="fpx8g9"
기존 Branch의 위치를 과거로 이동
```

Revert:

```text id="b1u9id"
기존 기록은 그대로 유지
+
되돌리는 새로운 Commit 생성
```

이미 GitHub에 Push해서 다른 사람과 공유된 Commit을 되돌릴 때는 일반적으로 Revert가 더 안전한 선택이 될 수 있다.

기존 공개 이력을 다시 쓰지 않기 때문이다.

---

## 복구 명령어 선택

상황에 따라 다음처럼 생각할 수 있다.

```text id="wh6i82"
파일을 수정했지만
변경을 버리고 싶다
→ git restore

git add를 취소하고 싶다
→ git restore --staged

최근 Commit만 취소하고
변경사항은 Staging 상태로 유지
→ git reset --soft

최근 Commit과 Staging은 취소하지만
수정한 파일은 유지
→ git reset --mixed

최근 Commit과 파일 변경까지 제거
→ git reset --hard

이미 공유된 Commit을
이력을 유지하면서 되돌리고 싶다
→ git revert
```

Reset, 특히 `--hard`를 사용할 때는 현재 상태를 먼저 확인하는 습관이 중요하다.

---

## Git Reflog

`git reflog`는 Git에서 **HEAD와 Branch가 어디를 가리켰는지 이동 기록을 확인하는 명령어**다.

일반적인 `git log`에서는 더 이상 보이지 않는 Commit도 Reflog에서는 일정 기간 확인할 수 있다.

예를 들어 다음 Commit이 있다고 하자.

```text
A ── B ── C
          ↑
        branch
```

그런데 실수로 Branch를 삭제했다고 하자.

```powershell
git branch -D feature/example
```

이후:

```powershell
git log --oneline --all
```

을 실행했을 때 `C`가 더 이상 보이지 않을 수 있다.

Commit 자체가 즉시 완전히 사라졌다고 단정할 필요는 없다.

---

## Reflog 확인

다음 명령으로 최근 Git 이동 기록을 확인한다.

```powershell
git reflog --oneline
```

최근 기록만 보고 싶다면:

```powershell
git reflog --oneline -10
```

처럼 사용할 수 있다.

예:

```text
f4a399a HEAD@{1}: commit: practice: add reflog recovery file
06eb185 HEAD@{2}: checkout: moving from practice/example to main
```

여기서 중요한 것은 Commit ID다.

```text
f4a399a
```

이 Commit을 다시 찾았다면 해당 위치에 새로운 Branch를 만들 수 있다.

---

## 잃어버린 Commit에서 Branch 복구

예를 들어 Reflog에서 찾은 Commit ID가:

```text
f4a399a
```

라면:

```powershell
git branch feature/restored f4a399a
```

를 실행한다.

그러면 해당 Commit을 가리키는 Branch가 다시 생성된다.

```text
        C
        ↑
feature/restored

A ── B
```

이후 Branch로 이동:

```powershell
git switch feature/restored
```

파일과 Commit이 정상적으로 복구되었는지 확인한다.

```powershell
git status
```

```powershell
git log --oneline --decorate -5
```

---

## Reflog가 중요한 이유

Git에서는 Branch 이름과 Commit 자체를 구분해서 이해하는 것이 중요하다.

Branch는 특정 Commit을 가리키는 **포인터**에 가깝다.

```text
feature/example
      ↓
      C
```

Branch를 삭제하면:

```text
feature/example
```

이라는 이름이 사라질 수 있지만, 해당 Commit이 즉시 완전히 제거되는 것은 아니다.

그래서 Reflog를 통해 Commit ID를 다시 찾을 수 있는 경우가 있다.

```text
Branch 삭제
    ↓
git log에서 Commit이 안 보임
    ↓
git reflog 확인
    ↓
Commit ID 발견
    ↓
새 Branch 생성
    ↓
복구
```

---

## Reflog와 Git Log 차이

`git log`:

```text
현재 Branch나 참조에서
도달 가능한 Commit 이력 확인
```

`git reflog`:

```text
HEAD와 Branch 참조가
어디로 이동했는지 로컬 기록 확인
```

따라서 실수로:

```text
Branch를 삭제했거나
reset으로 과거로 이동했거나
Commit 위치를 잃어버렸을 때
```

Reflog가 복구에 도움이 될 수 있다.

---

## Reflog 사용 시 주의

Reflog는 영구 백업 시스템이 아니다.

오래된 기록은 Git의 정리 과정에서 사라질 수 있다.

또한 Reflog는 기본적으로 **내 로컬 Git 저장소의 기록**이다.

따라서:

```text
"실수해도 reflog가 있으니 아무렇게나 삭제해도 된다"
```

라고 생각하기보다,

위험한 명령을 실행하기 전에:

```powershell
git status
git log --oneline --decorate --graph --all
```

로 현재 상태를 먼저 확인하는 습관이 중요하다.

---

## Git Stash

`git stash`는 아직 Commit하지 않은 변경사항을 **잠시 임시 보관**할 때 사용한다.

예를 들어 파일을 수정하고 있는데 갑자기 다른 Branch로 이동해야 한다고 하자.

```text
파일 수정 중
   ↓
아직 Commit하기 애매함
   ↓
다른 Branch로 이동해야 함
```

이럴 때 작업 내용을 버리지 않고 잠시 치워둘 수 있다.

```powershell
git stash
```

실행하면 Working Directory의 변경사항이 임시 저장되고, 작업 폴더는 마지막 Commit 상태에 가깝게 정리된다.

```text
수정 중인 파일
      ↓
   git stash
      ↓
임시 보관
      ↓
Working Directory 정리
```

---

## Stash 목록 확인

현재 보관된 Stash 목록은 다음 명령으로 확인할 수 있다.

```powershell
git stash list
```

예:

```text
stash@{0}: WIP on main: abc1234 previous commit
```

여러 번 Stash를 만들면:

```text
stash@{0}
stash@{1}
stash@{2}
```

처럼 쌓일 수 있다.

가장 최근 Stash가 보통 `stash@{0}`이다.

---

## Stash에 이름 붙이기

어떤 작업인지 알아보기 쉽게 메시지를 붙여 저장할 수도 있다.

```powershell
git stash push -m "practice: test stash"
```

이후:

```powershell
git stash list
```

를 보면 메시지와 함께 확인할 수 있다.

---

## Git Stash Pop

임시 보관했던 변경사항을 다시 복원하면서 Stash 목록에서도 제거하려면:

```powershell
git stash pop
```

을 사용한다.

```text
stash에 보관된 변경사항
        ↓
    git stash pop
        ↓
Working Directory에 복원
        +
Stash 목록에서 제거
```

즉 `pop`은:

```text
복원 + Stash 삭제
```

라고 이해할 수 있다.

---

## Git Stash Apply

변경사항을 복원하되 Stash 자체는 남겨두고 싶다면:

```powershell
git stash apply
```

를 사용할 수 있다.

```text
stash에 보관된 변경사항
        ↓
   git stash apply
        ↓
Working Directory에 복원
        +
Stash 기록은 유지
```

즉:

```text
apply
= 복원만 함

pop
= 복원하고 Stash도 제거
```

---

## 특정 Stash 적용

여러 Stash가 있다면 특정 항목을 지정할 수도 있다.

```powershell
git stash apply "stash@{0}"
```

PowerShell에서는 `stash@{0}`의 중괄호가 셸에서 다르게 해석될 수 있으므로 따옴표로 감싸는 것이 안전하다.

```powershell
git stash apply "stash@{0}"
```

같은 이유로 삭제할 때도:

```powershell
git stash drop "stash@{0}"
```

처럼 사용할 수 있다.

---

## Stash 삭제

더 이상 필요하지 않은 Stash 하나를 삭제:

```powershell
git stash drop "stash@{0}"
```

모든 Stash를 한꺼번에 삭제하는 명령도 있다.

```powershell
git stash clear
```

하지만 `clear`는 보관된 Stash 전체를 삭제하므로 사용 전에:

```powershell
git stash list
```

로 확인하는 것이 안전하다.

---

## Stash 사용 흐름

기본적인 흐름은 다음과 같다.

```text
파일 수정 중
   ↓
git status
   ↓
git stash
   ↓
다른 작업 수행
   ↓
원래 Branch로 복귀
   ↓
git stash list
   ↓
git stash pop
   ↓
작업 계속
```

`git stash`는 임시 보관 기능이므로 중요한 작업을 장기간 저장하는 용도로 사용하기보다는, 작업 흐름을 잠시 전환할 때 사용하는 것이 적절하다.

---

## Stash 사용 전 확인

Stash를 사용하기 전에도 먼저:

```powershell
git status
```

로 어떤 변경사항이 있는지 확인하는 습관이 좋다.

특히 새로 만든 Untracked 파일은 상황과 옵션에 따라 기본 Stash에 포함되지 않을 수 있으므로, 어떤 파일이 보관되는지 확인해야 한다.

---

## Git Commit Amend

`git commit --amend`는 **가장 최근 Commit을 다시 수정해서 새 Commit으로 만드는 명령어**다.

주로 이런 경우에 사용한다.

```text
Commit 메시지를 잘못 작성했을 때

파일 하나를 Commit에 빠뜨렸을 때
```

중요한 점은 기존 Commit을 그대로 수정하는 것이 아니라 **새로운 Commit으로 다시 작성한다는 것**이다.

---

## Commit 메시지 수정

예를 들어 최근 Commit 메시지에 오타가 있다고 하자.

```text
pratice: add amend file
```

다음처럼 수정할 수 있다.

```powershell
git commit --amend -m "practice: add amend file"
```

그러면 마지막 Commit 메시지가 새 내용으로 바뀐다.

---

## Commit ID도 변경된다

Amend를 실행하면 Commit 내용이나 메시지가 바뀌기 때문에 Commit ID도 새로 생성된다.

예:

```text
수정 전
abc1234

수정 후
def5678
```

즉:

```text
abc1234
   ↓ amend
def5678
```

처럼 기존 Commit을 기반으로 새로운 Commit이 만들어진다고 이해하면 된다.

---

## 빠뜨린 파일 추가

Commit을 만든 뒤 파일 하나를 빼먹었다는 것을 발견했다고 하자.

먼저 빠뜨린 파일을 Staging한다.

```powershell
git add amend-extra.txt
```

그다음:

```powershell
git commit --amend --no-edit
```

을 실행한다.

`--no-edit`는 기존 Commit 메시지는 그대로 유지하면서 Staging된 변경사항만 마지막 Commit에 추가한다.

```text
기존 Commit
   +
빠뜨린 파일
   ↓
git commit --amend --no-edit
   ↓
새로운 Commit
```

---

## Amend 전후 확인

Amend 전에는 현재 상태를 먼저 확인하는 것이 좋다.

```powershell
git status
```

최근 Commit 확인:

```powershell
git log --oneline -3
```

Amend 후에도 다시:

```powershell
git log --oneline -3
```

을 확인하면 Commit 메시지와 Commit ID가 변경된 것을 볼 수 있다.

---

## Push 전 Amend

아직 GitHub에 Push하지 않은 Commit이라면 Amend를 비교적 안전하게 사용할 수 있다.

```text
Local Commit
   ↓
amend
   ↓
새 Commit
   ↓
push
```

아직 다른 사람이 해당 Commit을 사용하지 않았기 때문이다.

---

## Push 후 Amend 주의

이미 GitHub에 Push한 Commit을 Amend하면 로컬과 원격의 Commit ID가 달라진다.

```text
GitHub
abc1234

Local
def5678
```

이 상태에서는 일반적인 `git push`가 거절될 수 있다.

이력을 강제로 다시 쓰는 Push가 필요할 수 있기 때문이다.

따라서 이미 공유된 Commit에 Amend를 사용할 때는 주의해야 한다.

특히 여러 사람이 함께 사용하는 Branch라면 다른 사람의 작업 이력에 영향을 줄 수 있다.

---

## Amend 사용 기준

```text
아직 Push하지 않은
최근 Commit 수정
→ amend 사용하기 좋음

이미 Push한
최근 Commit 수정
→ 신중하게 판단

공용 Branch의
공개된 Commit 수정
→ 가능하면 기존 이력을 다시 쓰지 않는 방법 고려
```

---

## Amend와 새 Commit

항상 Amend를 사용해야 하는 것은 아니다.

이미 Push된 작업에 단순한 수정이 필요한 경우에는 새로운 Commit을 추가하는 방법도 있다.

```text
A ── B ── C
          ↑ 기존 Commit
             \
              D 수정 Commit
```

이 방식은 기존 이력을 다시 작성하지 않는다.

따라서 Amend는:

```text
최근 Commit을 정리하고 싶고
아직 공유되지 않았을 때
```

특히 유용하다.

---

## .gitignore

`.gitignore`는 Git이 추적하지 않아야 할 파일이나 폴더를 지정하는 파일이다.

대표적으로 이런 파일들을 제외할 수 있다.

```text
.env
node_modules/
dist/
*.log
*.pem
*.key
```

예:

```text
.env
.env.*
!.env.example

node_modules/
dist/
*.log

*.pem
*.key
```

---

## 왜 .gitignore가 필요한가

프로젝트에는 GitHub에 올릴 필요가 없거나, 올리면 안 되는 파일이 존재할 수 있다.

예:

```text
환경설정 파일
API Key가 들어 있는 파일
비밀번호
Token
SSH Private Key
빌드 결과물
패키지 설치 폴더
로그 파일
```

특히 Secret이 포함된 파일은 공개 Repository에 올라가면 보안 사고로 이어질 수 있다.

---

## .env

`.env` 파일은 환경변수를 저장할 때 많이 사용한다.

예:

```dotenv
DATABASE_URL=example
API_KEY=example
```

실제 프로젝트에서는 이런 값에 진짜 접속 정보가 들어갈 수 있으므로 `.env`는 보통 Git에 올리지 않는다.

`.gitignore`:

```text
.env
.env.*
```

---

## .env.example

다른 사람이 어떤 환경변수가 필요한지 알 수 있도록 예제 파일은 Repository에 포함할 수 있다.

예:

```dotenv
DATABASE_URL=
API_KEY=
```

파일 이름:

```text
.env.example
```

`.gitignore`에서 `.env.*`를 제외하고 있다면 예제 파일만 다시 허용할 수 있다.

```text
.env
.env.*
!.env.example
```

즉:

```text
.env
→ 실제 Secret
→ Git에 올리지 않음

.env.example
→ 필요한 변수 이름만 설명
→ Git에 올릴 수 있음
```

실제 Secret 값을 `.env.example`에 넣으면 안 된다.

---

## 파일이 Ignore되는지 확인

특정 파일이 어떤 `.gitignore` 규칙 때문에 제외되는지 확인할 수 있다.

```powershell
git check-ignore -v .env
```

예:

```text
.gitignore:1:.env    .env
```

이 결과는 `.env`가 `.gitignore` 규칙에 의해 제외되고 있다는 뜻이다.

---

## Git이 이미 추적하는 파일은 주의

`.gitignore`에 파일을 추가했다고 해서 이미 Git이 추적하고 있는 파일이 자동으로 사라지는 것은 아니다.

예를 들어 `.env`가 이미 Commit된 상태라면:

```text
.env
```

를 추가해도 기존 추적 상태는 계속 유지될 수 있다.

현재 Git이 특정 파일을 추적하고 있는지는 다음처럼 확인할 수 있다.

```powershell
git ls-files .env
```

출력이 없다면 Git이 해당 파일을 추적하지 않는 상태다.

---

## Secret을 실수로 Commit한 경우

가장 중요한 원칙은:

```text
Git에서 파일을 지우는 것보다
Secret 자체를 먼저 폐기하거나 교체해야 한다.
```

예를 들어 실제 API Key를 실수로 Commit하거나 GitHub에 Push했다면:

```text
1. 해당 API Key / Token / 비밀번호 즉시 폐기
2. 새 Secret 발급
3. Git 추적에서 제거
4. 필요하면 Git History에서도 제거
5. 다시 노출되지 않는지 확인
```

Secret이 Git History에 남아 있다면 단순히 최신 Commit에서 파일을 삭제하는 것만으로는 충분하지 않을 수 있다.

과거 Commit에서 다시 확인할 수 있기 때문이다.

---

## 채팅이나 문서에도 Secret을 공개하지 않기

보안 정보는 GitHub뿐 아니라 다음 장소에도 그대로 적지 않는 것이 중요하다.

```text
개발 블로그
Issue
Pull Request
스크린샷
채팅
README
로그 파일
```

예제를 보여줄 때는 실제 값을 사용하지 않고:

```text
YOUR_API_KEY
example
FAKE_VALUE
```

같은 가짜 값을 사용한다.

---

## Private Key

특히 다음과 같은 파일은 매우 주의해야 한다.

```text
*.pem
*.key
*.p12
*.pfx
```

SSH Private Key나 인증서 Private Key는 외부에 노출되면 서버 접근 권한까지 탈취될 수 있다.

따라서 `.gitignore`에 포함시키는 것이 좋다.

```text
*.pem
*.key
*.p12
*.pfx
```

---

## .gitignore 확인 습관

Commit 전에:

```powershell
git status
```

를 확인하는 것이 중요하다.

예상하지 못한 파일이 나타난다면 바로 `git add .`를 실행하기보다 파일의 정체부터 확인한다.

특히:

```text
.env
키 파일
로그
임시 파일
빌드 결과물
설정 파일
```

이 보인다면 먼저 확인해야 한다.

---

## Secret 관리 핵심

```text
Secret은 Git에 넣지 않는다.

.env는 보통 .gitignore에 추가한다.

.env.example에는 변수 이름만 넣는다.

Commit 전에 git status를 확인한다.

Secret이 노출되었다면
삭제보다 먼저 Secret 자체를 폐기하거나 교체한다.
```

`.gitignore`는 보안을 도와주는 장치이지만, 이미 노출된 Secret을 다시 안전하게 만들어주는 기능은 아니다.

---

## Git Cherry-pick

`git cherry-pick`은 다른 Branch에 있는 **특정 Commit 하나를 현재 Branch에 적용하는 명령어**다.

보통 Merge는 Branch 전체의 변경 흐름을 합치지만, Cherry-pick은 필요한 Commit만 선택해서 가져올 수 있다.

예:

```text
main
A ── B

feature
A ── B ── C ── D
```

여기서 `C` Commit만 `main`에 가져오고 싶다면 Cherry-pick을 사용할 수 있다.

---

## Commit ID 확인

먼저 가져오고 싶은 Commit의 ID를 확인한다.

```powershell
git log --oneline
```

예:

```text
21a4bd6 practice: add first cherry-pick file
7c15e92 practice: add second cherry-pick file
```

여기서 첫 번째 Commit만 가져오고 싶다면 Commit ID:

```text
21a4bd6
```

를 사용한다.

---

## Cherry-pick 실행

Commit을 적용할 대상 Branch로 먼저 이동한다.

```powershell
git switch main
```

그다음:

```powershell
git cherry-pick 21a4bd6
```

을 실행한다.

그러면 해당 Commit의 변경사항이 현재 Branch에 적용되고 새로운 Commit이 만들어진다.

```text
feature
A ── B ── C ── D
          ↑

main
A ── B ── C'
```

여기서 `C`와 `C'`는 같은 변경사항을 가지고 있지만 Commit ID는 서로 다르다.

---

## Commit ID가 다른 이유

Cherry-pick은 기존 Commit 자체를 이동시키는 것이 아니다.

해당 Commit의 변경사항을 현재 Branch 위에서 **다시 적용하여 새로운 Commit을 만드는 방식**이다.

```text
기존 Commit
C = 21a4bd6

Cherry-pick 후
C' = 새로운 Commit ID
```

Commit은 부모 Commit, 작성 정보, 시점 등의 영향을 받기 때문에 새로운 Commit ID가 생성된다.

---

## Cherry-pick과 Merge 차이

Merge:

```text
Branch 전체의 개발 흐름을 합침
```

Cherry-pick:

```text
특정 Commit만 선택해서 가져옴
```

예:

```text
feature Branch
C ── D ── E

Merge
→ C, D, E 흐름 전체 반영

Cherry-pick D
→ D에 해당하는 변경만 반영
```

---

## Cherry-pick 확인

적용 후 Commit 이력을 확인한다.

```powershell
git log --oneline --decorate --graph --all
```

파일이 정상적으로 적용되었는지도 확인한다.

```powershell
git status
```

필요하면:

```powershell
git diff HEAD~1..HEAD
```

로 방금 생성된 Commit의 변경사항을 확인할 수도 있다.

---

## Cherry-pick 충돌

Cherry-pick도 현재 Branch와 가져올 Commit의 변경사항이 충돌하면 Conflict가 발생할 수 있다.

이 경우:

```text
cherry-pick
   ↓
Conflict 발생
   ↓
충돌 파일 수정
   ↓
git add
   ↓
git cherry-pick --continue
```

순서로 진행할 수 있다.

Cherry-pick을 취소하고 이전 상태로 돌아가려면:

```powershell
git cherry-pick --abort
```

를 사용할 수 있다.

---

## Cherry-pick 사용 시 주의

Cherry-pick은 편리하지만 같은 변경사항이 여러 Branch에 서로 다른 Commit ID로 존재하게 될 수 있다.

```text
feature
C

main
C'
```

따라서 Branch 전체를 합치는 것이 목적이라면 Merge가 더 자연스러울 수 있다.

Cherry-pick은:

```text
특정 수정 Commit만 필요하거나
다른 Branch의 일부 작업만 가져와야 할 때
```

선택적으로 사용하는 것이 좋다.

---

## Git Tag

Tag는 특정 Commit에 **이름표를 붙여 중요한 시점을 표시하는 기능**이다.

보통 버전을 표시할 때 많이 사용한다.

예:

```text id="tag1"
v0.1.0
v1.0.0
v1.1.0
```

Commit 이력이 다음과 같다고 하자.

```text id="tag2"
A ── B ── C ── D
          ↑
        v0.1.0
```

`v0.1.0` Tag는 `C` Commit을 가리킨다.

---

## Annotated Tag

메시지와 작성 정보까지 포함하는 Annotated Tag를 만들 수 있다.

```powershell id="tag3"
git tag -a v0.1.0 -m "Release v0.1.0"
```

생성한 Tag 확인:

```powershell id="tag4"
git tag
```

특정 Tag의 상세 정보 확인:

```powershell id="tag5"
git show v0.1.0
```

Annotated Tag는 단순한 이름뿐 아니라 Tag 메시지 등의 정보도 함께 저장한다.

---

## Tag는 자동으로 Push되지 않는다

Commit을 Push했다고 해서 로컬에서 만든 Tag가 자동으로 GitHub에 올라가는 것은 아니다.

```text id="tag6"
Local
Commit + Tag

git push
   ↓

GitHub
Commit만 반영될 수 있음
```

특정 Tag를 GitHub에 Push하려면:

```powershell id="tag7"
git push origin v0.1.0
```

을 사용한다.

모든 로컬 Tag를 한꺼번에 Push하는 방법도 있다.

```powershell id="tag8"
git push origin --tags
```

하지만 필요하지 않은 Tag까지 모두 올라갈 수 있으므로 특정 Tag만 Push하는 방식이 더 명확할 수 있다.

---

## 원격 Tag 확인

GitHub 원격 저장소에 특정 Tag가 있는지 확인할 수 있다.

```powershell id="tag9"
git ls-remote --tags origin v0.1.0
```

Annotated Tag의 경우 결과에 다음처럼 두 줄이 나타날 수도 있다.

```text id="tag10"
<sha> refs/tags/v0.1.0
<sha> refs/tags/v0.1.0^{}
```

`^{}`가 붙은 항목은 Annotated Tag가 최종적으로 가리키는 Commit과 관련된 참조다.

---

## Tag 삭제

로컬 Tag 삭제:

```powershell id="tag11"
git tag -d v0.1.0
```

GitHub 원격 Tag 삭제:

```powershell id="tag12"
git push origin --delete v0.1.0
```

Tag는 보통 배포 버전이나 중요한 기록을 가리키므로 실제 프로젝트의 정식 Tag를 삭제할 때는 신중해야 한다.

---

## Tag와 Branch의 차이

Branch와 Tag는 둘 다 Commit을 가리킬 수 있지만 목적이 다르다.

Branch:

```text id="tag13"
개발이 진행되면서
새 Commit을 따라 계속 이동
```

Tag:

```text id="tag14"
특정 Commit을
고정된 시점으로 표시
```

예:

```text id="tag15"
A ── B ── C ── D
              ↑
            main

        ↑
      v0.1.0
```

`main`은 이후 Commit이 생기면 계속 이동하지만 `v0.1.0`은 지정한 Commit을 계속 가리킨다.

---

## 버전 번호

프로젝트 버전은 보통 다음과 같은 형태를 많이 사용한다.

```text id="tag16"
v1.2.3
```

개념적으로:

```text id="tag17"
1 = 큰 변화
2 = 기능 추가
3 = 수정
```

처럼 구분할 수 있다.

이 방식은 Semantic Versioning에서 흔히 사용하는:

```text id="tag18"
MAJOR.MINOR.PATCH
```

형식이다.

예:

```text id="tag19"
v1.0.0
v1.1.0
v1.1.1
```

프로젝트 초기 개발 단계에서는 반드시 복잡하게 운영하기보다 실제 배포 시점부터 일관된 규칙을 정하는 것이 중요하다.

---

## GitHub Release

GitHub Release는 특정 Git Tag를 기준으로 **사용자에게 배포 버전을 설명하고 공개하는 기능**이다.

개념적으로:

```text id="tag20"
Commit
  ↓
Tag
  ↓
GitHub Release
```

순서로 이해할 수 있다.

예를 들어 Boss Timer의 첫 사용 가능한 버전을 배포한다면:

```text id="tag21"
v0.1.0
```

Tag를 만든 뒤 GitHub Release에서:

```text id="tag22"
버전 이름
변경 내용
설치 또는 사용 방법
주의사항
```

등을 기록할 수 있다.

---

## Tag와 Release의 차이

Git Tag:

```text id="tag23"
Git이 특정 Commit을 표시하는 기능
```

GitHub Release:

```text id="tag24"
Tag를 기준으로
GitHub에서 배포 정보를 제공하는 기능
```

따라서 Release는 Git 자체의 기능이 아니라 GitHub에서 제공하는 기능이다.

---

## 언제 Tag를 만드는가

모든 Commit마다 Tag를 만들 필요는 없다.

예를 들어:

```text id="tag25"
문서 오타 수정
작은 코드 수정
개발 중간 Commit
```

마다 Tag를 만들면 오히려 관리하기 어려워진다.

보통:

```text id="tag26"
처음 사용 가능한 버전
큰 기능이 추가된 버전
배포할 가치가 있는 안정된 시점
```

등 중요한 지점에 Tag를 만드는 것이 좋다.

---

## 기본 Release 흐름

```text id="tag27"
기능 개발 완료
   ↓
테스트
   ↓
main에 Merge
   ↓
배포 상태 확인
   ↓
Tag 생성
   ↓
Tag Push
   ↓
GitHub Release 작성
```

Tag는 단순한 장식이 아니라:

```text id="tag28"
"이 Commit이 특정 버전이다"
```

라는 기준점을 만드는 역할을 한다.
