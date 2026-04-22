---
layout: post
title: "[20-04-2026] 손 안대고 PPT 만들기 - (6) PPTArena 벤치마크 PPTX 전용 프롬프트 설계"
date: 2026-04-20
categories: ppt-auto-generation
author: Hwijun Kim
---

## 오늘의 목표
PPTArena 벤치마크에서 pptx 파일만 제공하는 조건에 최적화된 프롬프트 설계하기

## 요약
PPTArena 벤치마크에서 pptx 파일만 입력으로 제공하는 조건의 프롬프트를 설계했어요. 일반 프롬프트 구성부터 시작해서 여러 케이스별 영문 프롬프트를 작성하고, Claude 코드 벤치마크의 XML 편집 프롬프트와 비교해서 python-pptx 직접 수정 프롬프트 수정안도 제안했어요. 웹 서비스 제출용으로 프롬프트를 강화하고 txt 형식으로도 재작성했어요.

## 환경
- PPTArena 벤치마크
- python-pptx
- OOXML (Office Open XML)

## 오늘 한 일
- pptx 전용 입력 시 일반 프롬프트 구성 예시 정리
- Case 1(번역), 42(포스터 4열), 76(미학 개편), 87(중복 삭제), 100(애니메이션) 대상 영문 프롬프트 작성
  - 단일 pptx 입력 → 완성 pptx 출력, shape 열거/최소 diff/OOXML 유효성 포함
- 클로드 코드 벤치마크의 XML 편집 프롬프트 양식과 비교해 python-pptx 직접 수정 프롬프트 수정안 제안
  - 3파트 구조, CRITICAL 규칙, 보일러플레이트/Preservation rules 등
- 웹 서비스 단일 pptx 제출용으로 Case별 프롬프트 강화
- md 프롬프트를 txt 형식(역할 → 컨텍스트 → 지시사항 1줄 → CRITICAL RULES)으로 재작성
- Case 28 "Fix Slide Order and Agenda" 시나리오 작성

## 에러/이슈
- Case 28을 처음에는 다른 주제("Apply Consistent Corporate Branding")로 잘못 작성함

## 해결 방법
- 올바른 "Fix Slide Order and Agenda" 시나리오로 재작성해 `.discord-output/case_28.md`에 저장

## 참고
- PPTArena: 100개의 샘플 덱이 있는 PPT 편집 능력 평가 벤치마크 ([논문](https://arxiv.org/pdf/2512.03042))

## 다음 목표
설계한 프롬프트로 실제 벤치마크 실행 및 성능 비교
