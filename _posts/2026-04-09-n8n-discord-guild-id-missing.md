---
layout: post
title: "[09-04-2026] 사이버 노예 AI Agent 만들기 - (2) n8n Discord 노드 Guild ID 누락 오류 해결"
date: 2026-04-09 17:00:00 +0900
categories: [AI Agent]
tags: [n8n, discord, guild-id, troubleshooting]
author: Hyunyoung Kim
description: "n8n에서 Discord 노드 사용 시 필수 파라미터인 Server ID(Guild ID) 누락으로 워크플로우가 실행되지 않는 문제를 해결한 과정을 정리해요."
---

## 요약

n8n에서 Discord 노드를 사용하여 메시지를 수집하려는 중, 필수 파라미터 누락으로 워크플로우 자체가 실행되지 않는 문제가 발생했어요. Channel ID 외에 Server ID(Guild ID)를 추가로 입력하여 해결했어요.

## 환경

- n8n (Automation Workflow)
- Discord API
- Discord Bot (Message Content Intent 활성화)

## 오늘 한 일

n8n에서 Discord 노드를 사용해 채널 메시지를 수집하는 워크플로우를 구성했어요. 디스코드 개발자 포털에서 봇 설정을 완료하고, 서버 초대 및 채널 멤버 추가까지 마쳤어요.

## 에러/이슈

워크플로우 실행 시 다음과 같은 에러가 발생했어요:

```
Problem executing workflow: The workflow has issues and cannot be executed for that reason. Please fix them first.
```

n8n Discord 노드에서 필수값이 누락되어 워크플로우 자체가 실행 불가능한 상태였어요.

## 해결 방법

1. 디스코드 개발자 포털에서 **Message Content Intent** 활성화를 확인했어요.
2. 봇을 서버에 초대하고 해당 채널에 멤버로 추가했어요.
3. n8n 노드 설정에서 **Channel ID** 외에 필수값인 **Server ID(Guild ID)**를 추가로 입력했어요.

핵심은 n8n Discord 노드가 Channel ID만으로는 동작하지 않고, **Server ID(Guild ID)도 반드시 필요하다**는 점이에요. Guild ID는 디스코드에서 서버 아이콘을 우클릭 → "서버 ID 복사"로 얻을 수 있어요.

## 참고

- n8n Discord 노드 사용 시 Channel ID와 Server ID(Guild ID)는 둘 다 필수 파라미터예요.
- 개발자 모드를 활성화해야 ID 복사 메뉴가 표시돼요 (사용자 설정 → 고급 → 개발자 모드).
