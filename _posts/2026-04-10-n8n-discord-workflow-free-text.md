---
layout: post
title: "[10-04-2026] 사이버 노예 AI Agent 만들기 - (4) n8n 자동 포스팅 워크플로우 완성 및 SSH 터널 환경 이슈 해결"
date: 2026-04-10 18:00:00 +0900
categories: [AI Agent]
tags: [n8n, discord-bot, claude-api, ssh-tunnel, free-text, scp]
author: Hwijun Kim
description: "n8n Discord 워크플로우에서 Send Message and Wait for Response 노드를 활용한 자동 포스팅 봇을 개발하고, SSH 터널 환경에서의 이슈를 해결한 과정을 정리해요."
---

## 요약

n8n Discord 워크플로우에서 "Send Message and Wait for Response" 노드를 활용한 자동 포스팅 봇을 개발했어요. Custom Form과 Free Text 방식을 비교한 후, SSH 터널 환경에서의 webhook URL 접근 문제를 회피하기 위해 Free Text 방식을 채택했어요. Discord 채널 메시지를 수집하여 Claude API로 요약 후 자동 포스팅하는 워크플로우를 완성했어요.

## 환경

- n8n (자동화 워크플로우)
- Discord Bot
- Claude API
- SSH 터널 환경 (로컬 → 서버)
- SCP (파일 전송)

## 오늘 한 일

- n8n Discord 워크플로우에서 "Send Message and Wait for Response" 노드를 설정했어요 (Free Text / Custom Form 방식 비교 및 구현).
- SSH 터널 환경에서 Custom Form의 webhook URL 문제를 분석했어요 (0.0.0.0 접근 불가 이슈 → ngrok/cloudflared 터널링 해결방안 정리, Free Text 방식으로 우회).
- n8n Code 노드에서 두 개의 Discord "Get Many Messages" 노드 출력을 합쳐서 처리하는 코드를 작성했어요 (`$('노드이름').all()` + 스프레드 연산자로 병합).
- Discord 채널 메시지를 수집하여 Claude API로 요약 후 자동 포스팅하는 워크플로우를 완성했어요.
- test.txt 파일 생성 후 SCP로 서버(`aicp415@dlogin01.usc.unist.ac.kr:2123`)에 전송 테스트를 했어요.

## 에러/이슈

- Custom Form 클릭 시 `http://0.0.0.0:5678/webhook-waiting/...` URL 접근 불가 — SSH 터널은 내 PC→서버 방향만 열려 있어 Discord→서버 방향 접근이 안 됐어요.
- Code 노드에서 `SyntaxError: Unexpected token '{'` 발생 — 템플릿 리터럴에 백틱(`` ` ``)이 빠져 있었어요.
- `spawn claude ENOENT` 에러가 발생했어요.

## 해결 방법

- Custom Form 대신 **Free Text 방식**으로 전환했어요 (ngrok/cloudflared 터널링 없이 동작 가능).
- `.map(item => ${...})` → `` .map(item => `${...}`) `` 로 백틱을 추가하여 SyntaxError를 해결했어요.
- 두 Get Messages 노드 병합: `$('Get Many Messages').all()`과 `$('Get Many Messages1').all()`을 스프레드 연산자로 합쳤어요.

## 참고

- SSH 터널 환경에서는 외부에서 서버로의 접근이 제한되므로, webhook 기반 Custom Form 대신 Free Text 방식이 적합해요.
- n8n Code 노드에서 여러 노드의 출력을 합칠 때는 `$('노드이름').all()`과 스프레드 연산자를 활용하면 편리해요.
