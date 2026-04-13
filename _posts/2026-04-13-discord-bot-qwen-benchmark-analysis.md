---
layout: post
title: "[13-04-2026] 사이버 노예 AI Agent 만들기 - (5) Qwen 3B 벤치마크 결과 분석 및 모델 품질 한계 확인"
date: 2026-04-13 18:30:00 +0900
categories: [AI Agent]
tags: [discord-bot, n8n, qwen, benchmark, hallucination, pptx]
author: Hwijun Kim
description: "Discord 봇 및 n8n 워크플로우 가동을 확인하고, Qwen 3B 벤치마크 결과를 분석하여 모델 품질 한계를 확인한 과정을 정리해요."
---

## 오늘의 목표

Discord 봇 및 n8n 워크플로우 정상 가동 확인, 이전 Qwen 3B 벤치마크 결과 분석

## 요약

Discord 봇과 n8n 워크플로우의 정상 가동을 확인했어요. 이전에 실행한 Qwen 3B 벤치마크 결과를 분석한 결과, 성공 40건 중 9건이 원본과 동일한 문제가 발견됐어요. prs.save() 누락과 모델 환각(hallucination)으로 인한 코드 반복 생성이 원인이었고, 7B 모델로 전환하기로 결정했어요.

## 환경

- Discord Bot (`discord-claude/server.js`)
- n8n (자동화 워크플로우)
- Qwen2.5-VL-3B 벤치마크 결과 데이터

## 오늘 한 일

- Discord 봇(`discord-claude/server.js`) 및 n8n 워크플로우 정상 가동을 확인했어요.
- 이전 Qwen 3B 벤치마크 결과를 분석했어요: 성공 40건 중 9건이 원본과 동일한 문제를 확인했어요.

## 에러/이슈

- **prs.save() 호출 누락**: Qwen 3B 모델이 생성한 코드에서 PPTX 저장 함수를 호출하지 않아, 결과 파일이 원본과 동일하게 저장됐어요.
- **모델 환각(hallucination)**: 동일한 코드 줄을 10회 이상 반복 생성하는 현상이 발생했어요.

## 해결 방법

- 모델 자체의 품질 한계로 판단하고, 7B 모델로 전환하여 벤치마크를 재실행하는 방향으로 결정했어요.

## 참고

- 경량 VLM(3B)은 코드 생성 시 핵심 함수 호출 누락이나 환각 문제가 빈번하게 발생할 수 있어요.
- 벤치마크 결과 분석 시 "성공"으로 분류된 건도 원본과의 동일성 검사가 필요해요.

## 다음 목표

실패 개선, 개인 봇이 나의 개인 비서처럼 상호작용할 수 있게 하는 기반 갖추기
