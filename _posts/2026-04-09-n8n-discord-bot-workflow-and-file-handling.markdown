---
layout: post
title: "n8n 디스코드 봇 워크플로우 구축 및 파일 처리 기능 추가"
date: 2026-04-09 14:11:00 +0900
categories: [AI Agent]
tags: [n8n, discord-bot, webhook, automation, allowed-mentions]
author: Hwijun Kim
description: "n8n 워크플로우를 활용한 디스코드 봇 자동화 구축과 파일 처리 기능 추가, 봇 간 멘션 인식 이슈 해결 과정을 정리합니다."
---

## 요약

n8n 워크플로우를 생성하여 디스코드 채널에 자동 메시지를 전송하는 구조를 구축하고, 디스코드 봇의 파일 수신/전송 기능을 추가했습니다. 또한 봇 간 멘션이 인식되지 않는 문제의 원인을 분석하고 해결 방안을 도출했습니다.

## 환경

- n8n (자동화 워크플로우 플랫폼)
- Discord Bot (for휘준, TechBlog_Bot)
- discord-claude/server.js

## 오늘 한 일

1. **n8n 워크플로우 생성** (`discord_hello_workflow.json`)
   - Manual Trigger → HTTP Request로 Discord 채널에 메시지 전송하는 구조

2. **n8n 테크블로그 봇 워크플로우 분석**
   - Webhook → Get messages → JS 필터링 → AI 요약 → Discord 전송 파이프라인 구조 파악

3. **Discord 봇 파일 처리 기능 추가** (`discord-claude/server.js`)
   - 텍스트 파일 → 프롬프트 삽입
   - 이미지 → `--file` 플래그 전달
   - 임시 파일 자동 정리

## 에러/이슈

- 테크블로그 봇이 **휘준님 봇(for휘준)이 보낸 메시지를 인식하지 못하는 문제** 발생
- `allowed_mentions` 미설정으로 봇이 보낸 `<@ID>` 멘션이 파싱되지 않을 수 있음
- n8n Discord Trigger 노드가 기본적으로 봇 메시지(`message.author.bot === true`)를 무시할 가능성

## 해결 방법

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

- n8n 공식 문서의 Discord 노드 설정 참조
- Discord API `allowed_mentions` 문서 참조
