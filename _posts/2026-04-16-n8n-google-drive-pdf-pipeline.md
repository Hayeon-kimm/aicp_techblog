---
layout: post
title: "[16-04-2026] 사이버 노예 AI Agent 만들기 - (6) n8n 구글 드라이브 연동 및 PDF 자동 분석 파이프라인 구축"
date: 2026-04-16
categories: ai-agent
author: Hyunyoung Kim
---

## 오늘의 목표
n8n과 구글 드라이브를 연동해서 PDF 파일이 업로드되면 자동으로 감지하고 AI가 분석하는 파이프라인 구축하기

## 요약
n8n에서 구글 드라이브 연동 파이프라인을 구축했어요. 특정 폴더에 PDF가 업로드되면 자동으로 감지하고 다운로드한 뒤, Gemini 모델로 내용을 분석하는 워크플로우를 설계했어요. 권한/인증 문제, 바이너리 데이터 변환 문제 등 여러 이슈를 해결하면서 파이프라인을 완성했어요.

## 환경
- n8n (Automation Workflow)
- Google Drive API
- Google AI Studio (Gemini)
- Discord API

## 오늘 한 일
- n8n-구글 드라이브 연동 파이프라인 구축: 특정 폴더에 PDF 파일이 업로드되면 이를 감지하고 자동으로 다운로드하는 워크플로우 설계
- AI(Gemini) 모델 연동: Google AI Studio를 통해 발급받은 API 키로 n8n 내에 지능형 에이전트(AI Agent) 배치
- PDF 데이터 파싱 및 텍스트 추출: 다운로드된 바이너리 파일(PDF)에서 AI가 읽을 수 있는 텍스트 데이터를 성공적으로 추출

## 에러/이슈
- **Node does not have any credentials set**: AI 모델 노드에 인증 정보가 등록되지 않아 실행이 차단됨
- **Folder selection error**: 구글 드라이브 API 권한 문제로 인해 n8n UI상에서 폴더 목록이 보이지 않는 이슈 발생
- **File ID mapping**: 앞선 노드(Trigger)의 출력값이 다음 노드(Action)로 자동으로 넘어가지 않아 수동 매핑이 필요했던 상황

### 실패 원인 분석
- **권한 및 인증 체계의 복잡성**: 단순 계정 연결 외에도 각 서비스(Google Drive, Gemini)가 요구하는 API 활성화 및 특정 권한 범위(Scope) 설정이 누락되어 접근 제한 발생
- **데이터 형식의 이해 부족**: 텍스트 메시지와 달리 파일(PDF)은 'Binary' 형태로 취급되어야 하며, 이를 변환하는 중간 과정이 필수적임을 간과함

## 해결 방법
- **Credential 직접 등록**: Google AI Studio에서 전용 API 키를 발급받아 n8n 시스템에 보안 인증서로 등록하여 해결
- **ID 수동 매핑(Expression)**: 목록이 뜨지 않는 폴더나 파일은 주소창의 고유 ID를 복사하여 n8n의 'Expression' 기능을 통해 직접 경로를 지정
- **바이너리 변환 노드 활용**: Extract From PDF 노드를 추가하여 파일 덩어리를 텍스트 데이터로 변환, AI가 학습할 수 있는 환경을 조성

## 참고
- [Google AI Studio](https://aistudio.google.com/)
- [n8n 공식 문서](https://docs.n8n.io/)

## 다음 목표
PDF 분석 결과를 Discord 채널로 자동 전송하는 기능 추가
