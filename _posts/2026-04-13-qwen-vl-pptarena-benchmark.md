---
layout: post
title: "[13-04-2026] 손 안대고 PPT 만들기 - (3) Qwen2.5-VL-7B로 PPTArena 벤치마크 실행 및 GPU 호환성 해결"
date: 2026-04-13 18:00:00 +0900
categories: [PPT Auto-generation]
tags: [qwen, vl-model, pptarena, hpc, gpu, quantization, bitsandbytes, cuda]
author: Hwijun Kim
description: "Qwen2.5-VL-7B 모델로 PPTArena 벤치마크를 HPC 클러스터에서 실행하면서 발생한 GPU 호환성 문제와 VRAM 최적화 과정을 정리해요."
---

## 오늘의 목표

Qwen2.5-VL-7B 모델로 PPTArena 벤치마크를 HPC 클러스터에서 실행하기

## 요약

Qwen2.5-VL-7B 모델로 PPTArena 벤치마크를 HPC 클러스터에서 실행했어요. GPU 구성을 여러 차례 변경하고, 4bit NF4 양자화로 VRAM을 ~14GiB에서 ~3.5GiB로 절감했어요. NVIDIA 드라이버 460 호환성 문제는 PyTorch 다운그레이드와 CUDA 라이브러리 경로 설정으로 해결했어요.

## 환경

- Qwen2.5-VL-7B 모델
- HPC 클러스터 (V100, P100, K80 GPU)
- NVIDIA 드라이버 460
- PyTorch 2.1.2+cu118
- bitsandbytes (4bit NF4 양자화)

## 오늘 한 일

- Qwen2.5-VL-7B 모델로 PPTArena 벤치마크를 실행했어요 (HPC 클러스터).
- GPU 구성을 여러 차례 변경했어요: V100 1장 → P100 2장 → K80 4장 → P100 2장으로 최종 결정.
- 4bit NF4 양자화(bitsandbytes)를 적용하여 모델 VRAM을 ~14GiB에서 ~3.5GiB로 절감했어요.
- NVIDIA 드라이버 460 호환성 문제를 해결했어요 (PyTorch 2.6 → 2.1.2 다운그레이드, LD_LIBRARY_PATH에 CUDA 라이브러리 경로 추가).
- 모델 로딩 설정을 수정했어요: max_memory 명시, attn_implementation="eager" (P100 호환), GPU 배치 검증 로직 추가.
- 오픈소스 경량 VLM 대안을 조사했어요 (Gemma-3-4B-it, Qwen2.5-VL-3B 등).

## 에러/이슈

- **CUDA OOM**: 100건 전부 실패 — FP16 상태에서 모델 가중치가 GPU VRAM 대부분을 점유했어요.
- **nvmlDeviceGetNvLinkRemoteDeviceType 심볼 에러**: 드라이버 460에 없는 NVML API를 호출해서 발생했어요.
- **libcusparse.so.11 미발견 에러**: LD_LIBRARY_PATH가 누락되어 있었어요.
- **P100에서 bitsandbytes 4bit가 FP16 fallback**: compute capability 6.0이라 INT8/NF4가 제대로 동작하지 않았어요.
- **모델 로딩 후 VRAM 미사용**: `device_map="auto"`가 CPU로 silent fallback 되고 있었어요.

## 해결 방법

- PyTorch 2.1.2+cu118로 다운그레이드하여 드라이버 호환성을 확보했어요.
- LD_LIBRARY_PATH에 nvidia cusparse/cublas 등 라이브러리 경로를 추가했어요.
- 4bit 양자화를 적용하여 OOM 문제를 해결했어요.
- max_memory 명시 + eager attention으로 GPU 배치를 정상화했어요.

## 참고

- P100(compute capability 6.0)에서는 bitsandbytes의 INT8/NF4 양자화가 FP16으로 fallback될 수 있어요. V100 이상 권장.
- `device_map="auto"` 사용 시 GPU VRAM이 부족하면 자동으로 CPU로 fallback되므로, max_memory를 명시적으로 설정하는 것이 안전해요.
- 드라이버 버전이 낮은 HPC 환경에서는 PyTorch 버전을 맞춰서 호환성을 확보해야 해요.

## 다음 목표

n8n HTTP request로 GenSpark 웹페이지에 PPT 편집 요청 넣는 기능 만들기
