---
layout: ../../layouts/BlogPost.astro
title: "GitHub와 Cloudflare Pages로 개발 블로그 만들기"
description: "Astro, GitHub, Cloudflare Pages와 커스텀 도메인을 이용해 무료 개발 블로그를 구축한 과정을 기록합니다."
date: "2026-08-17"
---

# GitHub와 Cloudflare Pages로 개발 블로그 만들기

프로젝트를 진행하면서 코드만 남기는 것이 아니라, 초보자의 시선에서 어떤 작업을 했고 어떤 문제를 만났는지 함께 기록하기 위해 개인 개발 블로그를 만들었다.

## 사용한 구성

```text
Markdown 글
   ↓
GitHub 저장소
   ↓
Cloudflare Pages 자동 빌드
   ↓
Astro 정적 사이트
   ↓
https://blog.mongku.org
```

## GitHub 저장소

블로그 전용 저장소로 `Katts2/dev-blog`를 만들었다.

프로젝트와 블로그 저장소를 분리한 이유는 실제 애플리케이션 코드와 공개 개발 기록을 각각 관리하기 쉽도록 하기 위해서다.

## Astro

블로그 프레임워크는 Astro를 사용했다.

현재는 복잡한 CMS를 사용하지 않고 `src/pages/blog/` 아래에 Markdown 파일을 추가하는 단순한 방식으로 운영한다.

예:

```text
src/pages/blog/001-project-start.md
src/pages/blog/002-github-cloudflare-blog.md
```

## Cloudflare Pages

Cloudflare Pages에서 GitHub의 `dev-blog` 저장소를 연결했다.

빌드 설정은 다음과 같다.

```text
Production branch: main
Build command: npm run build
Build output directory: dist
```

GitHub의 `main` 브랜치에 변경사항이 올라가면 Cloudflare Pages가 자동으로 사이트를 다시 빌드하고 배포한다.

## 기본 Pages 주소

배포 후 다음 기본 주소가 생성되었다.

```text
https://dev-blog-5jp.pages.dev
```

## 커스텀 도메인

Cloudflare Pages의 Custom domains 기능을 이용해 다음 주소를 연결했다.

```text
https://blog.mongku.org
```

도메인 상태가 활성화되고 SSL 사용 상태까지 확인했다.

## 보안 원칙

블로그에는 다음 정보를 공개하지 않는다.

- 비밀번호
- API Key / Token
- SSH Private Key
- 실제 `.env` 내용
- 데이터베이스 접속 비밀번호
- Cloudflare Tunnel Token

공개 기록에는 작업 방법과 구조만 남기고 인증정보는 항상 제외한다.

## 다음 단계

다음으로 Reddit에도 프로젝트 진행 상황을 짧게 공유할 수 있는 기록 방식을 준비한다.

그 작업까지 끝나면 개발 기록 환경 구축을 완료하고 본 프로젝트의 첫 단계인 보스타이머 인프라 구축을 시작한다.
