---
layout: post
title: "[14-04-2026] 손 안대고 PPT 만들기 - (4) Gemma4 E4B 벤치마크 및 PPTArena 코드 복원"
date: 2026-04-14 18:00:00 +0900
categories: [PPT Auto-generation]
tags: [gemma4, e4b, pptarena, benchmark, float16, xml, quantization, v100, pipeline]
author: Hwijun Kim
description: "원본 PPTArena 벤치마크 코드를 복원하고, Gemma4 E4B 모델로 플래닝+편집 파이프라인을 구성하여 벤치마크를 실행한 결과를 정리해요."
---

## 오늘의 목표

원본 PPTArena 벤치마크 코드 복원 및 Gemma4 E4B 모델 벤치마크 실행

## 요약

원본 PPTArena 벤치마크와 수정본 코드를 비교 분석하여 벤치마크 공정성에 영향을 주는 7가지 불필요한 로직 변경을 원본으로 복원했어요. Gemma4 E4B 모델로 플래닝과 편집을 모두 수행하는 파이프라인을 구성하고 벤치마크를 실행했어요. 여러 버그를 발견하고 수정하여 최종 69.2%의 성능을 달성했어요.

## 오늘 한 일

- 원본 PPTArena 벤치마크(GitHub)와 수정본 코드 비교 분석 수행, 벤치마크 공정성에 영향을 주는 7가지 불필요 로직 변경을 원본으로 복원 (코드 후처리 강화, compile() 구문검사, python-pptx 힌트 등 제거)
- 트렁케이션 한도는 V100 메모리 적응용으로 유지 (원본 대비 1/10~1/25 축소)
- Gemma4 E4B 플래닝 로직 구현: Gemma4 하나로 플래닝(XML 파일 선별) + 편집 모두 수행하도록 파이프라인 구성
- 로컬 모델 분기에서 `relevant_xml_paths`(플래닝 결과)를 활용하도록 `llm_handler.py` 수정 (기존에는 전체 XML을 보내는 버그 존재)
- Gemma4 E4B 벤치마크 다수 제출 (Job 149803, 149805, 149875, 149984)
- 벤치마크 결과 종합 분석: Gemini 3.1 Pro 100%, Gemma4 E4B 69.2%(52건), Qwen2.5-VL-7B 40%, Qwen2.5 28%, Qwen2.5-VL-7B(7b) 3%
- GPU 메모리/토큰 사용량 심층 분석: Gemma4 E4B 실제 모델 크기 ~15GB (멀티모달 포함), V100 32GB에서 운용 가능 확인
- 벤치마크 결과/리포트 파일 25개 백업(`backup_20260414/`) 후 정리
- 단일 케이스 수동 테스트 스크립트(`test_gemma4_single.py`) 작성

## 에러/이슈

- `bitsandbytes` 미설치로 4-bit 양자화 로드 시 Job 즉시 실패 (`ImportError`)
- Gemma4 E4B 실패 16건 전부 Invalid XML — 조기 검증 코드(orchestrator.py:301-307)가 자동 복구(`attempt_repair_xml`) 및 PYTHON_PPTX_EDIT 폴백을 차단하는 버그 발견
- 로컬 모델 분기에서 플래닝 결과(`relevant_xml_paths`)를 무시하고 전체 XML(`xml_file_paths`)을 전달하는 버그 발견

## 해결 방법

- 4-bit 양자화 제거 → float16 직접 로드로 전환 (E4B ~15GB, V100 32GB 충분)
- 조기 검증 블록 제거 → 복구 로직 및 PYTHON_PPTX_EDIT 폴백 정상 작동 가능하도록 수정
- 로컬 모델(Gemma/Qwen) 분기에 `relevant_xml_paths` 및 `edit_plan` 전달 추가

## 벤치마크 결과 비교

| 모델 | 성공률 |
|------|--------|
| Gemini 3.1 Pro | 100% |
| Gemma4 E4B | 69.2% (52건) |
| Qwen2.5-VL-7B | 40% |
| Qwen2.5 | 28% |
| Qwen2.5-VL-7B (7b) | 3% |
