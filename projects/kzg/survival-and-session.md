# KZG | 건설·시설물·낮과 밤·세션 UI

[프로젝트 개요](README.md) · [인벤토리](inventory-and-interaction.md) · [입력 수정](input-fix-and-validation.md)

## 1. 건설 선택에서 시설물 생성까지

최종 Player에 연결된 경로는 다음과 같습니다.

```text
WB_Build에서 침대·물탱크 선택
    → AC_BuildingSystem
    → 자원 처리 / 카메라 Trace 기반 Preview
    → 시설물 생성과 상호작용 연결
```

UI는 무엇을 지을지 선택하고, 컴포넌트는 배치 위치·미리보기·생성 처리를 담당하도록 연결했습니다. 인벤토리의 자원 처리와 시설물 액터를 이어 주는 게임플레이 기능입니다.

### 최종 경로와 별도 프로토타입 구분

프로젝트에는 `WB_BuildSystemWidget → ACBP_BuildingSystem → BP_Building`이라는 별도 건축 프로토타입도 있습니다. 이 경로에서는 Mesh 선택·회전, 커스텀 충돌 기반 겹침 상태와 재질 피드백을 다뤘습니다.

이 프로토타입의 모든 기능을 최종 `AC_BuildingSystem` 경로에 통합한 것으로 소개하지 않습니다. 동일한 건설 주제라도 **최종 연결 경로와 별도 실험 경로**를 나눠 기록합니다.

## 2. 날짜·시간을 조명과 HUD에 연결

![KZG 날짜와 시간 HUD](../../assets/kzg/time_hud.jpg)

DAY·시간 HUD가 포함된 팀 플레이 화면입니다. 건설 배치 Preview를 보여 주는 이미지는 아닙니다.

`BP_Day`는 날짜·위치·Time Zone 값을 Sun Position에 사용하고, Timeline으로 인게임 시간을 진행시킵니다. 시간 상태는 두 곳으로 전달됩니다.

| 소비자 | 사용 목적 |
|---|---|
| Directional Light·Sky·대기 표현 | 시간에 따른 태양·장면 조명 변화 |
| `WB_PlayerHUD` | Day와 Time 표시 |

Actor Replication과 일부 시간 상태의 복제 경로도 구성했습니다. 다만 동기 오차를 측정하거나 모든 클라이언트에서 장시간 동일하게 유지되는지 정량 검증한 결과는 이 문서에 포함하지 않습니다.

## 3. 시설물과 시간 관련 기능

| 자산 | 연결한 기능 | 구분 |
|---|---|---|
| `BP_Bed`, `BP_Bed1` | 침대 상호작용으로 시간 진행 | 본인 Blueprint 범위 |
| `BP_WaterSpawner` | Timer로 물 아이템을 인벤토리에 추가 | 일주기와는 별도 Timer 경로 |
| `BP_AIRSPAWN` | Solar Time 조건을 보급 이벤트와 연결 | 조건 연동은 본인, 비행기 이동·낙하 C++은 팀원 |

시간을 보여 주는 데서 끝나지 않고 시설물·보급 조건과 연결한 경험입니다. 모든 기능을 하나의 통합 시간 스케줄러로 구현한 것은 아닙니다.

## 4. Steam 세션 진입 UI

세션 작업의 목표는 플레이어가 직접 호스트를 만들거나 검색된 방에 참가할 수 있게 하는 것이었습니다.

| 단계 | 확인한 구성 |
|---|---|
| 엔진 설정 | Steam Net Driver, Online Subsystem Steam |
| 진입 관리자 | 프로젝트 GameInstance로 `MultiGameInstance` 지정 |
| 호스트 생성 | Advanced Sessions의 `CreateAdvancedSession`, Listen Server |
| 검색·표시·참가 | `BP_Session`, `WB_Menu`, `WB_ServerSlot` |

본인이 작성한 것은 외부 플러그인을 이용한 프로젝트 설정과 세션 UI·흐름의 연결입니다. Advanced Sessions 자체나 Steam 서비스, 팀 공통 네트워크를 개발한 것은 아닙니다.

## 5. 구조상 얻은 경험

- UMG에서 입력받은 선택을 게임플레이 컴포넌트와 연결했습니다.
- 시간 상태를 조명과 HUD가 함께 소비하도록 구성했습니다.
- 엔진·외부 플러그인이 제공하는 세션 기능을 사용자 화면으로 연결했습니다.
- 같은 주제의 프로토타입과 최종 경로를 구분해서 유지·설명하는 필요성을 확인했습니다.

## 확인 범위와 다음 검증

건설 Preview·시설물 배치와 세션 메뉴는 자산 구조로 확인했습니다. 공개 영상에는 시설물 상호작용과 시간 HUD가 중심이며 세션 화면·건설 Preview를 뚜렷하게 보여 주지는 않습니다.

향후 보강할 검증은 아래와 같습니다. **현재 완료한 테스트가 아니라 추가할 검증 항목**입니다.

- 건설 자원이 부족한 경우와 배치 취소 시 상태 복구
- 시간 진행·침대 상호작용의 다중 클라이언트 동기화
- 세션 검색 결과 없음·참가 실패·호스트 종료 시 UI 복구
- 물 생성 Timer의 반복 실행과 컨테이너 용량 초과 처리

## 원본 프로젝트의 관련 위치

- `Content/DEV/JJH/WB_Build.uasset`, `AC_BuildingSystem.uasset`
- `Content/DEV/JJH/WB_BuildSystemWidget.uasset`, `ACBP_BuildingSystem.uasset`, `BP_Building.uasset`
- `Content/DEV/JJH/BP_Day.uasset`, `WB_PlayerHUD.uasset`
- `Content/DEV/JJH/BP_Bed.uasset`, `BP_Bed1.uasset`, `BP_WaterSpawner.uasset`, `BP_AIRSPAWN.uasset`
- `Content/DEV/JJH/MultiGameInstance.uasset`, `BP_Session.uasset`, `WB_Menu.uasset`, `WB_ServerSlot.uasset`
- `Config/DefaultEngine.ini` — 설정 항목의 존재만 설명하며 내부 설정 파일 원문은 게시하지 않습니다.
