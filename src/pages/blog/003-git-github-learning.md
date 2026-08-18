---

layout: ../../layouts/BlogPost.astro
title: "Git · GitHub 학습 정리 — Commit부터 Branch, Merge, 복구까지"
description: "Git과 GitHub의 기본 개념부터 Commit, Branch, Merge, Pull Request, 충돌 해결, 복구, Stash, Secret 관리와 Tag까지 정리합니다."
date: "2026-08-18"
------------------

# Git · GitHub 학습 정리

Git과 GitHub를 이용한 기본적인 개발 흐름과 주요 명령어를 정리한다.

이 글에서 다루는 내용:

* Git과 GitHub의 차이
* Repository
* Working Directory와 Staging Area
* Commit
* Clone / Push / Pull / Fetch
* Branch
* Merge
* Merge Conflict
* GitHub Issue / Pull Request
* Restore / Reset / Revert
* Reflog
* Stash
* Commit Amend
* `.gitignore`와 Secret 관리
* Cherry-pick
* Tag와 GitHub Release

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
