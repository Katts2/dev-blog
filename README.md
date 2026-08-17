# Dev Blog

Cloud / Web 개발을 배우면서 프로젝트 진행 과정을 기록하는 개인 기술 블로그입니다.

현재 주 프로젝트는 **보스타이머 → 아이템 경매 → 정산 시스템**이며, 가능한 한 Always Free / Free Tier 인프라를 활용해 직접 구축하는 과정을 기록합니다.

## 기술 스택

- Astro
- Markdown
- GitHub
- Cloudflare Pages

## 로컬 실행

Astro 7 기준 Node.js 22.12 이상이 필요합니다. 이 저장소는 `.node-version`으로 Node.js 22.16.0을 지정합니다.

```bash
npm install
npm run dev
```

브라우저에서 터미널에 표시되는 로컬 주소로 접속합니다.

## 빌드

```bash
npm run build
```

빌드 결과물은 `dist/` 폴더에 생성됩니다.

## Cloudflare Pages 배포 설정

```text
Production branch: main
Build command: npm run build
Build directory: dist
```

GitHub 저장소를 Cloudflare Pages에 연결하면 `main` 브랜치에 새 커밋이 올라올 때 자동으로 다시 빌드하고 배포합니다.

## 글 작성 위치

현재 초보 단계에서는 구조를 최대한 단순하게 유지하기 위해 블로그 글을 다음 위치에 Markdown 파일로 작성합니다.

```text
src/pages/blog/
```

예:

```text
src/pages/blog/001-project-start.md
src/pages/blog/002-github-setup.md
src/pages/blog/003-oci-setup.md
```

글 상단에는 다음과 같이 정보를 작성합니다.

```yaml
---
layout: ../../layouts/BlogPost.astro
title: "글 제목"
description: "글 설명"
date: "2026-08-17"
---
```

## 현재 상태

- [x] GitHub 저장소 생성
- [x] Astro 기본 구조 생성
- [x] 첫 화면 생성
- [x] 블로그 글 레이아웃 생성
- [x] 첫 개발 글 작성
- [ ] Cloudflare Pages 연결
- [ ] `*.pages.dev` 배포 확인
- [ ] 개인 도메인 `blog.<domain>` 연결
- [ ] Reddit 기록 방식 준비
