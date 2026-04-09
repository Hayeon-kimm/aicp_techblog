---
layout: post
title: "[09-04-2026] 사이버 노예 AI Agent 만들기 - (4) Discord 봇 파일 처리 및 봇 간 통신 디버깅"
date: 2026-04-09 20:30:00 +0900
categories: [AI Agent]
tags: [n8n, discord-bot, claude-api, file-handling, bot-communication]
author: Hwijun Kim
description: "n8n 기반 Discord 요약 봇 워크플로우를 디버깅하고, 봇 파일 처리 기능 및 봇 간 통신 문제를 해결한 과정을 정리해요."
---

## 요약

n8n 기반 Discord 요약 봇 워크플로우의 디버깅을 진행했어요. Discord 봇에 파일 수신/전송 기능을 추가하고, 봇 간 메시지 인식 문제를 해결하기 위한 allowlist 로직을 구현했어요.

## 환경

- n8n (자동화 워크플로우)
- Discord Bot (`discord-claude/server.js`)
- Claude API / Claude CLI
- Node.js

## 오늘 한 일

- n8n 기반 Discord 요약 봇 워크플로우를 구축하고 디버깅했어요.
  - 구조: Webhook → Discord 메시지 수집 → JS 코드(오늘 메시지 필터링/프롬프트 생성) → Claude API 호출 → Discord 채널 전송
  - `discord_summary_workflow_fixed.json` 워크플로우 파일을 생성 및 수정했어요 (jsonBody 따옴표 충돌, 템플릿 리터럴 백틱 누락 수정).
  - `discord_hello_workflow.json` 테스트 워크플로우를 생성했어요 (수동 트리거 → HTTP Request로 Discord 메시지 전송).
- Discord 봇에 파일 처리 기능을 추가했어요 (`discord-claude/server.js`).
  - 텍스트 파일: 다운로드 후 프롬프트에 직접 포함
  - 이미지 파일: `claude -p --file` 플래그로 전달
- 봇 간 메시지 인식 문제 해결을 시도했어요.
  - 특정 봇 ID만 허용하는 `ALLOWED_BOT_IDS` 배열 로직을 설계했어요.
  - HTTP Request 노드에서 `allowed_mentions: { parse: ["users"] }` 설정을 추가했어요.

## 에러/이슈

- `400 Bad Request - Cannot send an empty message` → Claude API 응답이 비어서 Discord에 빈 메시지를 전송하려 했어요.
- `400 Bad Request - Value "YOUR_CHANNEL_ID" is not snowflake` → 플레이스홀더를 실제 채널 ID로 교체하지 않았어요.
- `exit code 1: Session token required for file downloads` → 파일 다운로드 시 세션 토큰이 미설정이었어요.
- `exit code 1: Input must be provided either through stdin or as a prompt argument when using --print` → Claude CLI 입력이 누락됐어요 (3회 반복).

## 해결 방법

- 빈 메시지 오류 → Claude API 키 교체 및 오늘 날짜 메시지 존재 여부를 확인해야 해요.
- 채널 ID 오류 → `YOUR_CHANNEL_ID`, `YOUR_BOT_TOKEN`, `YOUR_ANTHROPIC_API_KEY` 3개 플레이스홀더를 실제 값으로 교체했어요.
- 봇 메시지 무시 문제 → `allowed_mentions` 설정 추가 + 대상 봇 코드에서 특정 봇 ID 허용 로직을 추가했어요.

## 참고

- Claude CLI에서 `--print` 모드 사용 시 stdin 또는 `-p` 인자로 입력을 반드시 제공해야 해요.
- 파일 다운로드 기능을 사용하려면 세션 토큰 설정이 필요해요.
