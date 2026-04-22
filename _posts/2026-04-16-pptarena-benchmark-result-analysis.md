---
layout: post
title: "[16-04-2026] 손 안대고 PPT 만들기 - (5) PPTArena 벤치마크 결과 분석 및 실패 원인 진단"
date: 2026-04-16
categories: ppt-auto-generation
author: Hwijun Kim
---

## 오늘의 목표
PPTArena 벤치마크 실행 결과를 분석하고, 실패 원인을 파악해서 성공률을 높이기

## 요약
PPTArena 벤치마크 100케이스 중 28개만 성공하고 72개가 실패했어요. 실패 원인을 분석해보니 XML_EDIT 경로 53건은 Claude API 토큰 한도(rate limit) 도달로 출력 토큰이 0이 되어 실패한 것이었고, PYTHON_PPTX_EDIT 경로 19건은 JSON 파싱 실패(16건)와 스크립트 오류(3건)가 원인이었어요. `--retry-failed` 옵션으로 실패 케이스만 재실행하면 성공률을 최대 47%+까지 올릴 수 있을 것으로 예상돼요.

## 환경
- PPTArena 벤치마크 (100 케이스)
- Claude API
- `run_all_claude_code.py` 스크립트

## 오늘 한 일
- PPTArena 벤치마크 결과 분석 (총 100케이스 중 28개 성공/72개 실패)
- 실패 원인 분석:
  - XML_EDIT 경로 53건 실패 — 출력 토큰 0
  - PYTHON_PPTX_EDIT 경로 19건 실패 — JSON 파싱 16건, 스크립트 오류 3건
- 출력 토큰 0인 53건은 Claude API 토큰 한도(rate limit) 도달이 원인으로 확인
- `run_all_claude_code.py`의 `--retry-failed` 옵션 확인 및 실패 케이스 재시도 요청

## 에러/이슈
- **XML_EDIT 경로 53건**: 토큰 한도 도달로 출력 토큰 0 → 서브프로세스 exit code 1 실패
- **PYTHON_PPTX_EDIT 경로**: JSON 포맷 파싱 실패(16건), 스크립트 런타임 에러(3건)

## 해결 방법
- `--retry-failed` 플래그로 실패 케이스만 재실행
- 토큰 한도 문제 해결 시 성공률 28% → 최대 47%+ 가능성 예상

## 참고
- PPTArena: 100개의 샘플 덱이 있는 PPT 편집 능력 평가 벤치마크 ([논문](https://arxiv.org/pdf/2512.03042))

## 다음 목표
실패 케이스 재실행으로 성공률 개선 및 토큰 한도 관리 전략 수립
