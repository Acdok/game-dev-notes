# SmartGlassAR — 스마트글라스 스탬프 투어

> 적용할 수 없던 Unity 미러링 전제를 제거하고, Android/Kotlin 앱과 Meta 연동 중심으로 개발 파이프라인을 다시 구성한 4인 팀 프로젝트입니다.

[시연 영상 보기](https://youtu.be/yOnPj3VstqE)

![토끼 미션 팀 UI 시각 자산](../../assets/smartglass/team_mission_rabbit.png)



## 프로젝트 개요

| 항목 | 내용 |
|---|---|
| 과정 | MDIA 스마트 글래스 활용 AI 기반 가상융합 콘텐츠 제작과정 |
| 교육 기간 | 2026.06.09–08.04 |
| 개발 확인 기간 | 2026.07.20–08.02 · 로컬 Git 기록 기준 |
| 구성 | 4인 팀 프로젝트 |
| 최종 플랫폼 | Android/Kotlin 단일 앱 · Android API 31 이상 |
| 팀 기술 | Meta Wearables DAT, BLE, MediaCodec, TensorFlow Lite, Jetpack Compose |
| 개발 방식 | CLI·MCP를 활용한 AI 보조 개발, Task 분할, Git 협업, 자동 테스트·검증 문서 |

관광지의 근접 구역에 들어가면 미션을 시작하고, 스마트글라스 카메라로 대상을 찾은 뒤, 팀 비전 파이프라인이 검출·재검증한 결과를 미션과 스탬프 UI에 연결하는 프로토타입입니다. 최종 화면은 스마트글라스 Display와 Android Compose UI입니다.

## 방향을 바꾼 이유

초기에는 Unity 앱을 Android 휴대폰에서 실행하고 그 화면을 스마트글라스에 미러링할 계획이었습니다. 개발 중 사용한 Meta Ray-Ban 기기에서는 SDK 환경에서 목표한 미러링 방식을 적용할 수 없음을 확인해, Unity를 최종 런타임에서 제외하고 Android/Kotlin 앱·Meta 입력·Display를 중심으로 기획과 파이프라인을 다시 구성했습니다.



## 본인 기여와 팀 결과

| 구분 | 범위 |
|---|---|
| 본인 | 초기안의 기술 제약 확인, Android/Kotlin 방향 전환과 파이프라인 재구성 참여, AI 생성 기획·구현안 교차검증, 필요한 기능·흐름 조정, Task 분할과 Git 충돌 조율, 파이프라인을 따라 디버그·수정 참여 |
| 팀 결과 | Meta DAT·HEVC 카메라 처리, BLE, YOLO·Tracker·ROI·CNN, 미션 상태, Compose·Meta Display UI, 로컬 스탬프 저장이 연결된 최종 소스와 시연 |
| AI 보조 | 기획안·구현안·문서 초안을 생성하고, 팀이 요구사항·소스·테스트 결과와 대조해 수정 |


## 팀 최종 파이프라인

```text
BLE 근접 구역
  → 미션 상태
  → Meta Display 입력
  → Meta DAT 카메라·HEVC 디코딩
  → TFLite YOLO 검출
  → 시간축 Tracker 안정화
  → 같은 원본 프레임 ROI
  → TFLite CNN 재검증
  → 미션·Display / Compose UI
  → 로컬 스탬프 저장
```

계층과 데이터 흐름은 [팀 아키텍처](ARCHITECTURE.md), 개인 기여 범위·AI 협업 방식·검증의 시점 차이는 [기여와 검증](CONTRIBUTION_AND_VALIDATION.md)에 정리했습니다.

## 시연에서 확인할 수 있는 것

| 구간 | 화면에서 확인되는 팀 결과 |
|---|---|
| 00:05–00:30 | 현장 이동과 탐색 흐름 |
| 00:37–01:15 | 토끼 대상과 검출 시각화 |
| 01:29 이후 | 미션 완료와 스탬프 UI |

영상은 사용자 흐름을 보여주지만, 촬영 빌드와 최종 소스의 동일성이나 BLE 거리·모델 정확도·발열·배터리 성능을 증명하지는 않습니다.

## 저장된 검증 기록

`Assets/Reports/18-AutomatedValidationReport.md`의 2026.07.26 실행 기록에는 다음 결과가 남아 있습니다.

- JVM 단위 테스트 141개, 실패·오류·skip 0개
- Debug APK 생성
- unsigned Release APK 생성
- Release DEX에서 debug manual-event gateway 제외 검사 통과

