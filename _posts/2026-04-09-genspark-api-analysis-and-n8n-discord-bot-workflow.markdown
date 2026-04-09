---
layout: post
title: "Genspark AI Slides API 분석 및 n8n 디스코드 봇 워크플로우 구축"
date: 2026-04-09 14:10:00 +0900
categories: [PPT Auto-generation, AI Agent]
tags: [genspark, api-analysis, har, n8n, discord-bot, webhook, automation]
author: Hwijun Kim
description: "Genspark AI Slides 서비스의 API 구조를 분석하고, n8n 기반 디스코드 봇 자동화 워크플로우를 구축한 과정을 정리합니다."
---

## 요약

오늘은 두 가지 트랙에서 작업을 진행했습니다.
1. **PPT Auto-generation**: Genspark AI Slides 서비스의 HTTP Request 양식과 API 구조를 조사
2. **AI Agent**: n8n 워크플로우 구축 및 디스코드 봇 파일 처리 기능 추가

---

## PPT Auto-generation

### 오늘 한 일

- **Genspark AI Slides** (genspark.ai/ai_slides) 서비스의 HTTP Request 양식 조사
- 오픈소스 클론(**open-genspark**) 기반으로 슬라이드 생성 API 및 PPT 변환 API의 엔드포인트와 요청/응답 구조 파악

```
POST /api/generate-slides   → 슬라이드 생성
POST /api/convert-to-ppt    → PPT 변환
```

- 실제 서비스 네트워크 분석을 위해 브라우저 HAR 파일 캡처(F12 → Network) 방식 검토

### 에러/이슈

실제 genspark.ai 서비스는 **인증이 필요**하며, 오픈소스 클론과 내부 API 형식이 다를 수 있음

### 해결 방법

HAR 파일을 캡처하여 실제 API 요청을 직접 분석하는 방향으로 진행 예정

> **HAR(HTTP Archive) 파일이란?**
> 브라우저의 개발자 도구(F12 → Network 탭)에서 캡처할 수 있는 네트워크 요청 기록 파일입니다. API 호출의 URL, 헤더, 요청/응답 본문을 모두 확인할 수 있어 리버스 엔지니어링에 유용합니다.

---

## AI Agent

### 오늘 한 일

1. **n8n 워크플로우 생성** (`discord_hello_workflow.json`)
   - Manual Trigger → HTTP Request로 Discord 채널에 메시지 전송하는 구조

2. **n8n 테크블로그 봇 워크플로우 분석**
   - Webhook → Get messages → JS 필터링 → AI 요약 → Discord 전송 파이프라인 구조 파악

3. **Discord 봇 파일 처리 기능 추가** (`discord-claude/server.js`)
   - 텍스트 파일 → 프롬프트 삽입
   - 이미지 → `--file` 플래그 전달
   - 임시 파일 자동 정리

### 에러/이슈

- 테크블로그 봇이 **휘준님 봇(for휘준)이 보낸 메시지를 인식하지 못하는 문제** 발생
- `allowed_mentions` 미설정으로 봇이 보낸 `<@ID>` 멘션이 파싱되지 않을 수 있음
- n8n Discord Trigger 노드가 기본적으로 봇 메시지(`message.author.bot === true`)를 무시할 가능성

### 해결 방법

n8n에서 **HTTP Request 노드**로 직접 Discord API를 호출하면서 `allowed_mentions`를 명시적으로 설정:

```json
{
  "content": "<@봇ID>\n메시지 내용",
  "allowed_mentions": {
    "parse": ["users"]
  }
}
```

추가로 n8n Discord Trigger 설정에서 봇 메시지 수신 옵션 확인이 필요합니다.

## 참고

- Genspark AI Slides: genspark.ai/ai_slides
- HAR 파일 캡처: 브라우저 F12 → Network → Export HAR
- n8n 공식 문서의 Discord 노드 설정 참조
