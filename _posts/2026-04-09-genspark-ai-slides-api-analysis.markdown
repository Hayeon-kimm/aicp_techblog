---
layout: post
title: "[09-04-2026] 손 안대고 PPT 만들기 - (1) Genspark AI Slides API 구조 분석 및 HAR 파일 기반 리버스 엔지니어링"
date: 2026-04-09 14:10:00 +0900
categories: [PPT Auto-generation]
tags: [genspark, api-analysis, har, ppt, reverse-engineering]
author: Hwijun Kim
description: "Genspark AI Slides 서비스의 HTTP Request 양식과 API 구조를 조사하고, HAR 파일을 활용한 실제 API 분석 방향을 정리합니다."
---

## 요약

Genspark AI Slides 서비스의 API 구조를 파악하기 위해 오픈소스 클론(open-genspark)을 분석하고, 실제 서비스의 네트워크 요청을 HAR 파일로 캡처하여 분석하는 방향을 검토했어요.

## 환경

- Genspark AI Slides (genspark.ai/ai_slides)
- 오픈소스 클론: open-genspark
- 브라우저 개발자 도구 (F12 → Network)

## 오늘 한 일

- **Genspark AI Slides** (genspark.ai/ai_slides) 서비스의 HTTP Request 양식 조사
- 오픈소스 클론(**open-genspark**) 기반으로 슬라이드 생성 API 및 PPT 변환 API의 엔드포인트와 요청/응답 구조 파악

```
POST /api/generate-slides   → 슬라이드 생성
POST /api/convert-to-ppt    → PPT 변환
```

- 실제 서비스 네트워크 분석을 위해 브라우저 HAR 파일 캡처(F12 → Network) 방식 검토

## 에러/이슈

실제 genspark.ai 서비스는 **인증이 필요**하며, 오픈소스 클론과 내부 API 형식이 다를 수 있음

## 해결 방법

HAR 파일을 캡처하여 실제 API 요청을 직접 분석하는 방향으로 진행 예정

> **HAR(HTTP Archive) 파일이란?**
> 브라우저의 개발자 도구(F12 → Network 탭)에서 캡처할 수 있는 네트워크 요청 기록 파일이에요. API 호출의 URL, 헤더, 요청/응답 본문을 모두 확인할 수 있어 리버스 엔지니어링에 유용해요.

## 참고

- Genspark AI Slides: genspark.ai/ai_slides
- HAR 파일 캡처: 브라우저 F12 → Network → Export HAR
