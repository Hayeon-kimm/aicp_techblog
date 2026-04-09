---
layout: post
title: "[09-04-2026] 사이버 노예 AI Agent 만들기 - (3) n8n 워크플로우 디버깅 및 봇 간 통신 문제 해결"
date: 2026-04-09 16:10:00 +0900
categories: [AI Agent]
tags: [n8n, discord-bot, workflow, debugging, allowed-mentions, bot-communication]
author: Hwijun Kim
description: "n8n 워크플로우 구축 과정에서 발생한 다양한 에러와 봇 간 메시지 인식 문제를 해결한 과정을 정리해요."
---

## 요약

n8n 워크플로우를 활용해 Discord 채널 메시지 수집 → AI 요약 → tech-blog-bot 채널 전송 파이프라인을 구축하면서 발생한 다양한 에러들을 해결한 과정을 정리했어요.

## 환경

- n8n (워크플로우 자동화)
- Discord Bot API
- Claude API (AI 요약)
- Node.js (`discord-claude/server.js`)

## 오늘 한 일

- n8n 워크플로우 구축 및 디버깅 (Discord 채널 메시지 수집 → AI 요약 → tech-blog-bot 채널 전송)
- `discord_hello_workflow.json` 테스트용 워크플로우 생성
- n8n Send Message 노드를 HTTP Request 노드로 교체하여 Discord API 직접 호출 방식으로 변경
- `discord_summary_workflow_fixed.json` 수정본 생성 (따옴표 충돌, 백틱 누락 수정)
- Discord 봇 파일 수신/전송 기능 조사 및 `server.js`에 파일 처리 기능 추가 (텍스트 파일 프롬프트 삽입, 이미지 `--file` 플래그 전달)
- 봇 간 메시지 인식 문제 해결 시도 (특정 봇 ID만 허용하는 allowlist 로직 설계)

## 에러/이슈

### 1. `Value "YOUR_CHANNEL_ID" is not snowflake`
채널 ID 플레이스홀더를 실제 값으로 교체하지 않아서 발생한 에러예요.

### 2. `Cannot send an empty message (400, 50006)`
Claude API 응답이 비어서 Discord에 빈 메시지를 전송하려고 시도했을 때 발생했어요.

### 3. 템플릿 리터럴 백틱 미사용
n8n Code 노드에서 템플릿 리터럴 백틱(`` ` ``) 대신 일반 따옴표(`"`)를 사용해서 `${}` 보간이 작동하지 않았어요.

### 4. `jsonBody` 내부 따옴표 충돌
JSON 안의 `"`와 바깥 JSON의 `"`가 충돌하는 문제가 발생했어요.

### 5. 봇 간 메시지 무시 문제
테크블로그 봇이 다른 봇의 메시지를 무시하는 문제가 있었어요. `message.author.bot` 필터링과 `allowed_mentions` 미설정이 원인이었어요.

## 해결 방법

- `YOUR_CHANNEL_ID`, `YOUR_BOT_TOKEN`, `YOUR_ANTHROPIC_API_KEY`를 실제 값으로 교체
- 내부 따옴표를 작은따옴표로 변경하고 백틱을 적용하여 워크플로우 수정본 생성
- HTTP Request 노드에서 `allowed_mentions: { parse: ["users"] }` 명시적 설정
- 특정 봇 ID만 허용하는 `ALLOWED_BOT_IDS` 배열 로직 제안

## 참고

- n8n Code 노드에서는 반드시 백틱(`` ` ``)을 사용해야 템플릿 리터럴이 작동해요
- Discord API에서 봇이 보낸 멘션이 실제로 알림을 트리거하려면 `allowed_mentions` 설정이 필수예요
