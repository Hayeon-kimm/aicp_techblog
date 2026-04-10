---
layout: post
title: "[09-04-2026] 사이버 노예 AI Agent 만들기 - (3) n8n Discord 요약 봇 구축, 파일 처리 및 봇 간 통신 해결"
date: 2026-04-09 18:30:00 +0900
categories: [AI Agent]
tags: [n8n, discord-bot, claude-api, bot-to-bot, allowed-mentions, file-handling]
author: Hwijun Kim
description: "n8n 기반 Discord 요약 봇 워크플로우를 구축하고, 봇 파일 처리 기능 추가 및 봇 간 통신 문제를 해결한 과정을 정리해요."
---

## 오늘의 목표

n8n으로 개인 봇이 블로그 포스팅 봇과 소통하게 하기 + 개인 봇을 통해 원격으로 파일 작업할 수 있게 하기

## 요약

n8n 기반 Discord 요약 봇 워크플로우를 구축했어요. Webhook → 메시지 수집 → JS 필터링 → Claude API 호출 → Discord 전송 파이프라인을 완성했고, Discord 봇에 파일 수신/전송 기능을 추가했어요. 또한 봇→봇 메시지 허용 및 멘션 파싱 문제를 해결했어요.

## 환경

- n8n (자동화 워크플로우)
- Discord Bot (`discord-claude/server.js`)
- Claude API / Claude CLI
- Node.js

## 오늘 한 일

- n8n 기반 Discord 요약 봇 워크플로우를 구축하고 디버깅했어요.
  - 구조: Webhook → Discord 메시지 수집 → JS 코드(오늘 메시지 필터링/프롬프트 생성) → Claude API 호출 → Discord 채널 전송
  - `discord_summary_workflow_fixed.json`을 생성 및 수정했어요 (jsonBody 따옴표 충돌 수정, 템플릿 리터럴 백틱 적용).
  - `discord_hello_workflow.json` 테스트 워크플로우를 생성했어요 (Manual Trigger → HTTP Request로 Discord 메시지 전송).
- n8n Send Message 노드를 HTTP Request 노드로 교체하여 `allowed_mentions: { parse: ["users"] }` 명시적 설정을 추가했어요.
- Discord 봇에 파일 처리 기능을 추가했어요 (`discord-claude/server.js`).
  - 텍스트 파일: 다운로드 후 프롬프트에 직접 포함
  - 이미지 파일: `claude -p --file` 플래그로 전달
- 봇→봇 메시지 허용 로직을 구현했어요 (`ALLOWED_BOT_IDS` 배열로 특정 봇 ID만 허용).

## 에러/이슈

- `400 - Value "YOUR_CHANNEL_ID" is not snowflake` → 플레이스홀더를 실제 채널 ID로 미교체한 것이 원인이었어요.
- `400 - Cannot send an empty message` → Claude API 호출 실패 또는 오늘 날짜 메시지 0건으로 빈 응답이 전송됐어요.
- 타임아웃 오류 (2분 초과)가 다수 발생했어요.
- 테크블로그 봇이 다른 봇 메시지를 무시하는 문제가 있었어요 (`message.author.bot` 필터링 + `allowed_mentions` 미설정).
- `exit code 1: Session token required for file downloads` → 파일 다운로드 시 세션 토큰이 미설정이었어요.
- `exit code 1: Input must be provided either through stdin or as a prompt argument when using --print` → Claude CLI 입력이 누락됐어요 (3회 반복).

## 해결 방법

- 채널 ID/봇 토큰/API 키 플레이스홀더를 실제 값으로 교체했어요.
- `allowed_mentions` 설정으로 멘션 파싱을 활성화했어요.
- `ALLOWED_BOT_IDS` 로직으로 특정 봇 메시지만 선택적으로 허용했어요.
- 빈 메시지 문제는 각 노드 출력값을 순차적으로 확인하여 원인을 추적할 수 있어요.

## 참고

- n8n에서 Discord 노드 대신 HTTP Request 노드를 사용하면 `allowed_mentions` 같은 세부 옵션을 직접 제어할 수 있어요.
- 봇 간 통신이 필요한 경우, `message.author.bot` 필터를 완전히 해제하지 말고 `ALLOWED_BOT_IDS`로 화이트리스트 방식을 사용하는 것이 안전해요.
- Claude CLI에서 `--print` 모드 사용 시 stdin 또는 `-p` 인자로 입력을 반드시 제공해야 해요.
- 파일 다운로드 기능을 사용하려면 세션 토큰 설정이 필요해요.

## 다음 목표

봇에게 더 많은 일 시키기
