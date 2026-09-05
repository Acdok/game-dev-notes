# SmartGlassAR 기여와 검증

## 개인 기여

1. 사용한 Meta Ray-Ban·SDK 환경에서 초기 Unity→Android 휴대폰→스마트글라스 미러링 경로가 목표 방식에 적용되지 않음을 확인했습니다.
2. 팀과 기획을 수정하고 Unity를 최종 런타임에서 제외해 Android/Kotlin·Meta 연동 중심으로 파이프라인을 다시 구성했습니다.
3. AI가 만든 기획안과 구현안을 요구 기능·현재 소스·실행 흐름과 교차검증하고, 빠진 기능과 파이프라인 단계를 조정했습니다.
4. 작업을 Task로 나누고 Git 동시 수정·충돌을 조율했습니다.
5. 정리한 파이프라인을 따라 문제를 확인하고 디버그·수정에 참여했습니다.


## 문제 해결 1 — 실행할 수 없는 전제 제거

### 문제

초기 기획은 Unity 앱 화면을 Android 휴대폰에서 스마트글라스로 미러링하는 흐름에 의존했습니다. 개발에 사용한 기기와 SDK에서는 목표한 방식이 적용되지 않았기 때문에, 이 전제를 유지하면 핵심 사용자 흐름을 검증할 수 없었습니다.

### 판단과 행동

- 기기·SDK 환경에 한정된 제약인지 먼저 구분했습니다.
- 미러링을 전제로 한 화면과 기능 목록을 다시 검토했습니다.
- Unity를 최종 앱에서 제외하고 Android/Kotlin을 호스트로 선택했습니다.
- Meta 입력·Display와 온디바이스 비전을 한 앱의 파이프라인으로 연결하도록 Task 순서를 재구성했습니다.

### 결과

팀 최종 소스에는 Android/Kotlin 단일 코드베이스에서 Meta 입력, 팀 비전 모듈, 미션 상태와 UI로 이어지는 경로가 남았습니다. 다만 최종 소스의 실기기 전체 체인 재현까지 완료됐다는 의미는 아닙니다.

## 문제 해결 2 — AI 결과를 팀 작업으로 통합

AI 보조 개발에서는 생성 속도보다 “현재 프로젝트에서 실행되는가”를 기준으로 삼았습니다.

```text
요구 기능 확인
  → AI 기획·구현안 생성
  → 기존 소스·SDK 제약과 교차검증
  → Task와 소유 경로 분리
  → 팀 변경 통합·Git 충돌 조율
  → 파이프라인 단계별 디버그
  → 테스트·보고서와 실제 결과 대조
```

- 기획 문장을 구현 단위와 완료 조건으로 다시 나눴습니다.
- AI가 제안한 기술이 현재 SDK·앱 구조에서 유효한지 확인했습니다.
- 동시에 수정할 가능성이 큰 파일은 작업 순서와 담당 범위를 조율했습니다.
- 정리한 파이프라인을 따라 문제를 확인하고 디버그·수정에 참여했습니다.
- 자동 테스트가 통과해도 실제 기기·현장 검증을 대체하지 않는다고 구분했습니다.

## 검증 기록과 시점

### 저장된 2026.07.26 팀 게이트

`Assets/Reports/18-AutomatedValidationReport.md`에는 다음 실행 결과가 기록돼 있습니다.

| 항목 | 저장된 결과 |
|---|---|
| `:app:testDebugUnitTest` | 141 tests · failure 0 · error 0 · skipped 0 |
| `:app:assembleDebug` | Debug APK 생성 |
| `:app:assembleRelease` | unsigned Release APK 생성 |
| Release DEX 검사 | debug manual-event gateway 제외 통과 |

이 결과는 JVM 로직, 빌드 가능성, debug 전용 진입점의 release 격리를 확인하는 근거입니다. 당시 연결된 API 31 이상 기기·에뮬레이터가 없어 connected instrumentation과 물리 현장 게이트는 실행되지 않았습니다.

### 최종 소스와의 시간 차이

```text
2026.07.26 저장된 자동 검증
  ↓ 이후 팀 변경
2026.08.02 Meta DAT·최종 UI가 포함된 소스 상태
  ↓
최종 HEAD 전체 재실행 결과는 미확인
```

따라서 7월 26일의 141개 통과 기록을 8월 2일 최종 통합의 완전한 검증으로 사용하지 않습니다. 현재 소스에 더 많은 테스트가 선언돼 있어도, 새 실행 결과가 없으면 통과 수치에 더하지 않습니다.

## 영상으로 확인한 팀 결과

[스마트글라스 스탬프투어 시연](https://youtu.be/yOnPj3VstqE)

| 구간 | 확인 내용 | 증명하지 않는 것 |
|---|---|---|
| 00:05–00:30 | 현장 이동·탐색 흐름 | BLE 거리 정확도, 장시간 연결 안정성 |
| 00:37–01:15 | 토끼 대상과 검출 시각화 | 모델 precision/recall, 최종 소스와 촬영 빌드 일치 |
| 01:29 이후 | 완료·스탬프 UI | 배포 서명, 발열·배터리 성능 |

영상의 기능은 팀 결과입니다. 개인 기여 범위는 이 화면을 가능하게 한 방향 전환, AI 결과 교차검증, Task·Git 조율, 파이프라인 디버그 참여로 설명합니다.

## 근거 파일 지도

| 확인하려는 내용 | 상대 경로 |
|---|---|
| 플랫폼·의존성 | `app/build.gradle.kts` |
| 최종 앱 조합 | `app/src/main/java/com/example/smartglassar/MainActivity.kt` |
| 팀 카메라 계층 | `app/src/main/java/com/example/smartglassar/camera/` |
| 팀 비전 계층 | `app/src/main/java/com/example/smartglassar/vision/` |
| 팀 미션·저장 계층 | `app/src/main/java/com/example/smartglassar/mission/` |
| 팀 UI 계층 | `app/src/main/java/com/example/smartglassar/ui/` |
| Task 설계 | `Assets/Tasks/` |
| 완료 조건 | `Assets/Checklists/` |
| 저장된 팀 검증 | `Assets/Reports/18-AutomatedValidationReport.md` |

