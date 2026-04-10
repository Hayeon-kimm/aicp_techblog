---
layout: post
title: "[10-04-2026] 손 안대고 PPT 만들기 - (2) PPTArena 벤치마크 프로젝트 구조 확인 및 파일 동기화"
date: 2026-04-10 18:30:00 +0900
categories: [PPT Auto-generation]
tags: [pptarena, benchmark, pptx, wsl, server-sync]
author: Hwijun Kim
description: "PPTArena 프로젝트의 디렉토리 구조를 확인하고, 벤치마크용 PPTX 파일 목록 점검 및 서버 간 파일 동기화 환경을 확인한 과정을 정리해요."
---

## 오늘의 목표

GPU 서버에 저장해둔 벤치마크 자유롭게 복제해서 로컬로 이동과 HTTP POST 가능하게 하기

## 다음 목표

n8n HTTP request로 GenSpark 웹페이지에 PPT 편집 요청 넣는 기능 만들기

## 요약

PPTArena 프로젝트의 디렉토리 구조를 탐색하고, Selected_Original 폴더 내 벤치마크용 PPTX 파일 목록을 점검했어요. 로컬(WSL)과 원격 서버 간 파일 동기화 환경도 확인했어요.

## 환경

- PPTArena 프로젝트
- WSL (로컬 환경)
- 원격 서버: `aicp415@dlogin01.usc.unist.ac.kr`

## 오늘 한 일

- PPTArena 프로젝트의 Selected_Original 디렉토리 내 PPTX 파일 목록을 확인했어요 (16개 테스트 케이스: AccessibilityMasterCleanup, AddBibliography, AddTransitions 등).
- PPTArena 프로젝트 전체 구조를 탐색했어요 (Selected_GenSpark, Selected_GroundTruth, benchmark_outputs 등 디렉토리 구성 확인).
- 로컬(WSL)과 원격 서버(`aicp415@dlogin01.usc.unist.ac.kr`) 간 PPTArena 관련 파일 동기화 환경을 확인했어요.

## 에러/이슈

- 없음

## 해결 방법

- 해당 없음

## 참고

- PPTArena는 100개의 샘플 덱이 있는 PPT 편집 능력 평가 벤치마크예요. ([논문 링크](https://arxiv.org/pdf/2512.03042))
- 현재 Selected_Original에는 16개 테스트 케이스가 선별되어 있으며, GenSpark 생성 결과와 GroundTruth를 비교하는 구조예요.
