---
layout: post
title: "[21-04-2026] 손 안대고 PPT 만들기 - (7) PPTArena 판정 파이프라인 분석 및 평가 스크립트 구축"
date: 2026-04-21
categories: ppt-auto-generation
author: Hwijun Kim
---

## 오늘의 목표
PPTArena 벤치마크의 판정(evaluation) 파이프라인을 분석하고, CLI 기반 평가 스크립트를 구축하기

## 요약
PPTArena 벤치마크의 판정 파이프라인을 심층 분석하고 CLI 기반 평가 스크립트를 구축했어요. `run_evaluation_claude_code_cli.py`를 작성해서 judge.py의 IF/VQ 프롬프트를 복제하고, identity/cheat 두 가지 baseline으로 judge 건강성을 진단했어요. 그 과정에서 기존 `run_evaluation.py`에 ground_truth 대신 original_rel_path를 넣는 구조적 버그를 발견했어요 — 편집을 안 한 모델이 자동 5점을 받는 역구조였어요.

## 환경
- PPTArena 벤치마크
- Gemini-3.1-pro-preview
- Claude CLI (`claude-musl`)
- CentOS 7 (glibc 2.17), HPC 클러스터
- GPT-5.4 (비용 분석 기준)

## 오늘 한 일
- `run_evaluation_claude_code_cli.py` 작성 — `judge.py`의 IF/VQ 프롬프트를 그대로 복제, `evaluation_pairs_refined.json`과 이름 join으로 실제 ground_truth/style_target 사용
- 실증 테스트 스크립트 `make_identity_baseline.py` / `make_identity_baseline_cli.py` 제작 — identity(원본=pred) / cheat(GT=pred) 두 baseline으로 judge 건강성 진단
- Gemini-3.1-pro-preview 벤치마크 99/99 판정 완료
  - IF avg 2.55 / median 2.0 / dist {1:19, 2:36, 3:19, 4:11, 5:10}
- 토큰/비용 분석 — 케이스당 평균 ~116K 토큰, GPT-5.4 기준 100케이스 보수적 $36~46 (케이스당 약 500~640원)
- `export_slides_to_images()` 내부 동작 및 30MB pptx 토큰 추정(텍스트 ~2K / vision ~30K) 분석

## 에러/이슈
1. **`run_evaluation.py`의 구조적 버그**: `ground_truth` 자리에 `original_rel_path`를 넣는 버그 → pred=원본이면 `diff_pptx_json`이 빈 diff를 반환, judge.py:150 perfect-match 단축으로 **편집 무시 모델이 자동 5점**을 받는 역구조. 편집 지시를 잘 따를수록 감점되는 문제
2. **CentOS 7 glibc 호환성**: conda env의 native `claude` CLI가 `GLIBC_2.18 not found`로 7/10 케이스 실패, 전원 `instruction_following_score=0`
3. **VQ Missing images**: identity 모드라 `benchmark_outputs_claude_code`에 렌더 이미지 없음, 실질적 VQ 신호 부재
4. **필드명 불일치**: `make_identity_baseline_cli.py`의 `summarize()` 필드명(`instruction_score`/`visual_score`)이 실제 결과 스키마(`instruction_following_score`/`visual_quality_score`)와 불일치

## 해결 방법
1. `src/evaluation_pairs_refined.json`의 실제 `ground_truth` 경로와 이름 join해서 judge에 전달하도록 CLI 평가 스크립트 수정 완료
2. `~/bin/claude-musl` 래퍼 존재 확인, `run_evaluation_claude_code_cli.py:270`에 `os.environ.get("CLAUDE_BIN")` fallback 추가 + `CLAUDE_BIN=~/bin/claude-musl`로 재실행 권장. benchmark JSON은 `--restore`로 원상복구 완료
3. `make_identity_baseline_cli.py`에서 `modified_pptx` 키 사용 + `final_status="Skip-IdentityBaseline"`로 limit 밖 케이스 judge 루프 건너뛰게 정리, 결과 JSON `.bak_identity` 백업/복원 지원

## 참고
- PPTArena: 100개의 샘플 덱이 있는 PPT 편집 능력 평가 벤치마크 ([논문](https://arxiv.org/pdf/2512.03042))
- `run_evaluation.py`의 ground_truth 버그는 벤치마크 공정성에 큰 영향을 미치는 중요한 발견

## 다음 목표
수정된 평가 스크립트로 전체 모델 재평가 및 공정한 비교 분석 수행
