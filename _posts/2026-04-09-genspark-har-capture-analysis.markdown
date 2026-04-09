---
layout: post
title: "[09-04-2026] 손 안대고 PPT 만들기 - (2) Genspark AI Slides HAR 캡처를 통한 실제 API 분석"
date: 2026-04-09 16:10:00 +0900
categories: [PPT Auto-generation]
tags: [genspark, har-capture, api-analysis, ppt-generation, reverse-engineering]
author: Hwijun Kim
description: "Genspark AI Slides 실제 API를 HAR 캡처로 분석하고, 오픈소스 클론과의 차이점을 조사한 과정을 정리해요."
---

## 요약

Genspark AI Slides(`genspark.ai/ai_slides`) PPT 생성 API의 실제 구조를 파악하기 위해 오픈소스 클론 분석과 HAR 파일 캡처 방법을 검토했어요.

## 환경

- Genspark AI Slides (`genspark.ai/ai_slides`)
- 오픈소스 클론: ComposioHQ/open-genspark
- 브라우저 개발자 도구 (F12 → Network)

## 오늘 한 일

- Genspark AI Slides(`genspark.ai/ai_slides`) PPT 생성 API 구조 조사
- 오픈소스 클론(open-genspark) 기반 HTTP request 양식 분석:
  - `POST /api/generate-slides` (프롬프트 → 슬라이드 JSON 생성)
  - `POST /api/convert-to-ppt` (JSON → .pptx 파일 변환)
- 브라우저 HAR 파일 캡처를 통한 실제 API 분석 방법 논의

## 에러/이슈

- 실제 `genspark.ai` 서비스는 공식 API가 비공개이며 인증이 필요해요
- 오픈소스 클론과 실제 엔드포인트가 다를 수 있어요

## 해결 방법

- F12 → Network 탭에서 HAR 파일을 캡처하여 실제 API 요청 형식을 직접 분석하기로 했어요

## 참고

- HAR 파일에는 인증 토큰 등 민감 정보가 포함될 수 있으므로 공유 시 주의가 필요해요
- 오픈소스 클론은 학습/참고용이며, 실제 서비스와 API 구조가 다를 수 있어요
