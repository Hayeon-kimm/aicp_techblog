---
layout: post
title: "[09-04-2026] 손 안대고 PPT 만들기 - (1) Genspark AI 슬라이드 에이전트 HAR 분석 및 Filler Words 제거 테스트"
date: 2026-04-09 18:00:00 +0900
categories: [PPT Auto-generation]
tags: [genspark, ppt, har-analysis, slides-agent, open-genspark, filler-words, sse-streaming]
author: Hwijun Kim
description: "Genspark AI 슬라이드 에이전트의 HAR 파일을 분석하고, PPT 발표자 노트의 filler words 자동 제거 테스트 및 오픈소스 클론 API 조사를 정리해요."
---

## 오늘의 목표

GenSpark 웹의 PPT 편집 기능 자동화하기

## 요약

Genspark AI의 슬라이드 에이전트 동작 방식을 HAR 파일 분석을 통해 파악했어요. PPT 업로드 후 SSE 스트리밍으로 AI 편집 결과를 수신하는 구조이며, 발표자 노트에서 filler words(um, uh, like 등)를 자동 제거하는 테스트도 수행했어요. 39개 슬라이드를 분석해서 30개 수정, 9개 삭제, 217자의 불필요 단어를 제거했어요. 오픈소스 클론(`open-genspark`)의 API 엔드포인트도 확인했어요.

## 환경

- Genspark AI (`genspark.ai/ai_slides`) 슬라이드 에이전트
- HAR 파일 분석 도구 (749건 요청, 24.76MB)
- 오픈소스 클론: `open-genspark`

## 오늘 한 일

- `www.genspark.ai.har` 파일(749건 요청, 24.76MB) 분석을 완료했어요.
- Genspark의 `POST /api/agent/ask_proxy` (slides_agent) 호출 구조를 파악했어요: PPT 업로드 → SSE 스트리밍으로 AI 편집 결과 수신 (약 5분 소요).
- `RemoveFillerWords_TestA.pptx` 파일을 업로드하여 발표자 노트에서 filler words 제거 작업을 확인했어요.
  - 39개 슬라이드 분석, 30개 슬라이드 노트 수정, 9개 삭제, 217자 불필요 단어 제거
- Genspark에 PPTX 파일과 프롬프트를 HTTP request로 제출하는 방법을 조사했어요.
  - 핵심 API: `POST /api/agent/ask_proxy` (slides_agent 타입, SSE 스트리밍 응답)
  - 오픈소스 클론(`open-genspark`) 기반 `/api/generate-slides`, `/api/convert-to-ppt` 엔드포인트를 확인했어요.

## 에러/이슈

- Genspark 공식 API는 비공개라 오픈소스 클론의 API 구조와 실제 서비스가 다를 수 있어요.
- 타임아웃 오류 2회 발생 (2분 초과) — HAR 파일 분석 시 발생했어요.
- HAR 파일 내 `413 Payload Too Large` (GA 수집 데이터 초과) 2건, 네트워크 실패 3건이 발견됐어요.

## 해결 방법

- 공식 API 대신 오픈소스 클론 `open-genspark`의 엔드포인트를 활용하는 방향으로 전환했어요.
- 타임아웃은 SSE 스트리밍 응답 특성상 약 5분이 소요되므로, 타임아웃 값을 늘려야 해요.
- `413 Payload Too Large`는 GA 수집 데이터 관련 오류로, PPT 편집 기능에는 영향 없어요.
- HAR 분석을 통해 실제 서비스의 요청/응답 구조를 파악해, 자체 파이프라인 설계에 참고할 수 있어요.

## 참고

- Genspark 공식 API가 비공개이므로, 오픈소스 클론 기반으로 자체 파이프라인을 구축하는 방향이 현실적이에요.
- HAR 파일 분석은 비공개 API의 동작 방식을 리버스 엔지니어링하는 데 유용한 방법이에요.
- PPT 업로드 → SSE 스트리밍 → AI 편집 결과 수신까지 약 5분이 소요돼요.

## 다음 목표

원격으로 GPU 서버에 저장해둔 벤치마크 샘플 로컬에 불러오는 기능 만들기
