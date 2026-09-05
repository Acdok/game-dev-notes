# ThiefCode — AI 에이전트 기반 정수 로그라이트

> AI 에이전트를 주된 구현 수단으로 사용하고, 작업 계약·도구 경계·자동 검증·증빙 인계를 묶은 하네스로 결과를 통제한 Unreal Engine 프로토타입입니다.

![ThiefCode 전투 QA 화면](../../assets/thiefcode/runtime_combat.png)

*PIE에서 전투 루프를 확인한 QA 화면입니다. 개발용 UI가 포함된 검증 자료이며 최종 폴리시 화면을 의미하지 않습니다.*

## 프로젝트 개요

| 항목 | 내용 |
|---|---|
| 확인된 작업 기간 | 2026.08.10–08.27 · 로컬 작업 기록 기준 |
| 형태 | 2인 팀 · AI 에이전트 중심 개발 |
| 장르 | 쿼터뷰 액션 로그라이트 |
| 핵심 규칙 | 몬스터가 떨어뜨린 `[언제]`와 `[무엇을]` 조각을 장착·합성해 전투 로직 구성 |
| 기술 | Unreal Engine 5.8, C++, GAS, StateTree, Enhanced Input, UMG, Niagara |
| 개발 도구 | CLI, UE-MCP, Python Editor Scripting, Automation Test, Data Validation, Pixel Streaming 2, Diversion |
| 실행 산출물 | Win64 Development 패키지 확인 |

이 프로젝트에서 강조하는 것은 “AI가 코드를 만들었다”는 사실만이 아닙니다. 모호한 요구를 실행 가능한 Task와 Checklist로 바꾸고, 에이전트가 수정할 모듈과 사용할 도구를 제한한 뒤, 에디터·테스트·패키지 결과로 통과 여부를 판정하는 개발 방식이 핵심입니다.

## 기여와 소유권

| 구분 | 범위 |
|---|---|
| 본인 | 하네스 규칙 설계, 기능 계약과 작업 경계 정의, 에이전트 작업 통합, 검증 게이트 구성·실행, Evidence/Handoff 기준 운영 |
| 2인 팀 결과 | 게임 규칙과 콘텐츠 방향, 전투·진행·UI가 연결된 플레이 가능한 프로토타입 |
| AI 에이전트 | 지정된 Task·도구·소유 경로 안에서 코드와 에셋을 생성·수정하고 반복 검증 수행 |
| 외부 기술 | Unreal Engine 및 엔진 플러그인·프레임워크 |

AI가 모든 코드와 에셋을 무인으로 완성했다고 주장하지 않습니다. 본인의 핵심 역량은 에이전트의 출력이 다시 실행되고 검증되며 다음 작업자에게 인계될 수 있도록 개발 환경을 구조화한 것입니다.

## 구현된 플레이 루프

```text
이동·마우스 조준
  → 자동 표적 선택·공격 / 대시
  → GAS·StateTree 기반 몬스터 행동과 공격 예고
  → 조각 드롭·획득
  → [언제] + [무엇을] 장착 또는 지정 합성
  → SaveGame 진행도·로드아웃 반영
  → HUD·미니맵·체크포인트로 다음 전투 진행
```

- 자동 조준·자동 공격과 대시를 연결한 쿼터뷰 전투
- GAS 능력과 StateTree 태스크를 사용하는 몬스터 및 텔레그래프
- 조각 획득, 수집 상태, 지정 합성, 패시브·액티브 로드아웃
- 체크포인트, 스폰·드롭, 진행도 저장
- HUD, 수집·합성 메뉴, 미니맵과 런타임 가림 처리
- 해커톤 제출 경로를 위한 Pixel Streaming 웹 실행 구성

세부 책임과 파일 관계는 [게임플레이 아키텍처](GAMEPLAY_ARCHITECTURE.md), AI 작업을 안정화한 방법과 실제 게이트 결과는 [하네스와 검증](HARNESS_AND_VALIDATION.md)에 정리했습니다.

## 검증 결과를 읽는 법

| 기록 | 확인된 결과 | 의미 |
|---|---|---|
| T18 | Editor/Game Build, Automation, Data Validation, Product Map Smoke, Package 및 패키지 QA 기록 `PASS` | 전체 구성과 패키지 산출물에 대한 저장된 통과 기록 |
| T24 | Automation 5/5, Product Map Smoke 1/1, Data Validation 오류·경고 0, 스폰 지점 검사 36/36 `PASS` | 월드·미니맵·스폰 관련 작업의 저장된 검증 기록 |
| T23 | strict 1080p60 최종 게이트 `BLOCKED`, ReleaseVerdict `FAIL` | 최종 성능·릴리스 통과 주장에 사용할 수 없음 |

T24의 최저 55.9225 FPS는 에디터 PIE 관측값입니다. 패키지 1080p60 보장이나 성능 인증 수치로 사용하지 않습니다.

## 구현 근거

원 프로젝트 기준 상대 경로입니다.

- 런타임 모듈: `ThiefCode/Source/ThiefCodeContracts/`, `ThiefCode/Source/ThiefCodeSystemsA/`, `ThiefCode/Source/ThiefCodeSystemsB/`, `ThiefCode/Source/ThiefCode/`
- 작업 표준: `Assets/UITasks/TASK_EXECUTION_STANDARD.md`
- 전체 구성 Task: `Assets/Tasks/18-full-composition-standalone.md`
- 월드 기능 Task: `Assets/Tasks/24-lush-demo-world-features.md`
- strict 릴리스 게이트: `Assets/Tasks/23-final-pixel-1080p60-release-gate.md`
- 저장된 게이트 결과: `Assets/Evidence/T18/latest/gate-summary.json`, `Assets/Evidence/T24/latest/gate-summary.json`, `Assets/Evidence/T23/latest/gate-summary.json`
- 빌드·테스트 진입점: `Scripts/BuildAndTest.ps1`

## 영상

현재 공개 확인이 끝난 ThiefCode 시연 영상 링크가 없어 문서에 임의로 추가하지 않았습니다. 영상이 공개되면 실행 빌드·촬영 버전·확인 가능한 기능 범위를 함께 표기할 예정입니다.

## 현재 한계

- 저장된 검증 기록과 현재 소스·패키지의 해시 일치는 별도 재검증이 필요합니다.
- strict 1080p60 3회 실시간 Pixel Streaming 최종 수용 테스트는 완료되지 않았습니다.
- QA 화면에는 디버그 표시가 포함될 수 있으며 상용 게임 수준의 시각 완성도를 뜻하지 않습니다.
- 해커톤 제출용 Pixel Streaming 경로는 구성했지만, 장시간 운영·네트워크 품질·동시 사용자 성능은 측정하지 않았습니다.
