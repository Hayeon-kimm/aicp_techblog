---
layout: post
title: "[09-04-2026] 손 안대고 PPT 만들기 - (2) Genspark AI로 발표자 노트 Filler Words 제거 테스트"
date: 2026-04-09 20:00:00 +0900
categories: [PPT Auto-generation]
tags: [genspark, ppt, filler-words, har-analysis, sse-streaming]
author: Hwijun Kim
description: "Genspark AI 슬라이드 에이전트로 PPT 발표자 노트의 filler words를 자동 제거하는 테스트를 수행하고, HTTP request 제출 방식을 조사한 과정을 정리해요."
---

## 요약

Genspark AI의 슬라이드 에이전트를 활용해 PPT 발표자 노트에서 filler words(um, uh, like 등)를 자동 제거하는 테스트를 수행했어요. 39개 슬라이드를 분석해서 30개 수정, 9개 삭제, 217자의 불필요 단어를 제거했어요. PPTX 파일과 프롬프트를 HTTP request로 제출하는 방법도 조사했어요.

## 환경

- Genspark AI (`genspark.ai/ai_slides`) 슬라이드 에이전트
- HAR 파일 분석 도구 (749건 요청, 24.76MB)
- 오픈소스 클론: `open-genspark`

## 오늘 한 일

- Genspark AI 슬라이드 에이전트를 활용한 PPT 편집 테스트를 수행했어요 (HAR 파일 분석).
- `RemoveFillerWords_TestA.pptx` 파일을 업로드하여 발표자 노트에서 filler words 제거 작업을 확인했어요.
  - 39개 슬라이드 분석, 30개 슬라이드 노트 수정, 9개 삭제, 217자 불필요 단어 제거
- Genspark의 HTTP request 양식을 조사했어요.
  - 핵심 API: `POST /api/agent/ask_proxy` (slides_agent 타입, SSE 스트리밍 응답)
  - 오픈소스 클론(`open-genspark`) 기반으로 `/api/generate-slides`, `/api/convert-to-ppt` 엔드포인트를 확인했어요.

## 에러/이슈

- 타임아웃 오류 2회 발생 (2분 초과) — HAR 파일 분석 시 발생했어요.
- HAR 파일 분석 중 `413 Payload Too Large` (Google Analytics 수집 데이터 크기 초과) 2건, 네트워크 실패 3건이 발견됐어요.

## 해결 방법

- 타임아웃은 SSE 스트리밍 응답 특성상 약 5분이 소요되므로, 타임아웃 값을 늘려야 해요.
- `413 Payload Too Large`는 GA 수집 데이터 관련 오류로, PPT 편집 기능에는 영향 없어요.

## 참고

- Genspark의 실제 API는 비공개 상태이므로, HAR 파일 캡처를 통한 리버스 엔지니어링 방식으로 구조를 파악하고 있어요.
- PPT 업로드 → SSE 스트리밍 → AI 편집 결과 수신까지 약 5분이 소요돼요.
