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


## 2. 날짜·시간을 조명과 HUD에 연결

![KZG 날짜와 시간 HUD](../../assets/kzg/time_hud.jpg)

DAY·시간 HUD가 포함된 팀 플레이 화면입니다. 건설 배치 Preview를 보여 주는 이미지는 아닙니다.

`BP_Day`는 날짜·위치·Time Zone 값을 Sun Position에 사용하고, Timeline으로 인게임 시간을 진행시킵니다. 시간 상태는 두 곳으로 전달됩니다.

| 소비자 | 사용 목적 |
|---|---|
| Directional Light·Sky·대기 표현 | 시간에 따른 태양·장면 조명 변화 |
| `WB_PlayerHUD` | Day와 Time 표시 |

## 3. 시설물과 시간 관련 기능

| 자산 | 연결한 기능 | 구분 |
|---|---|---|
| `BP_Bed`, `BP_Bed1` | 침대 상호작용으로 시간 진행 | 본인 Blueprint 범위 |
| `BP_WaterSpawner` | Timer로 물 아이템을 인벤토리에 추가 | 일주기와는 별도 Timer 경로 |
| `BP_AIRSPAWN` | Solar Time 조건을 보급 이벤트와 연결 | 조건 연동은 본인, 비행기 이동·낙하 C++은 팀원 |

시간을 보여 주는 데서 끝나지 않고 시설물·보급 조건과 연결한 경험입니다. 

## 4. Steam 세션 진입 UI

세션 작업의 목표는 플레이어가 직접 호스트를 만들거나 검색된 방에 참가할 수 있게 하는 것이었습니다.

| 단계 | 확인한 구성 |
|---|---|
| 엔진 설정 | Steam Net Driver, Online Subsystem Steam |
| 진입 관리자 | 프로젝트 GameInstance로 `MultiGameInstance` 지정 |
| 호스트 생성 | Advanced Sessions의 `CreateAdvancedSession`, Listen Server |
| 검색·표시·참가 | `BP_Session`, `WB_Menu`, `WB_ServerSlot` |

본인이 작성한 것은 외부 플러그인을 이용한 프로젝트 설정과 세션 UI·흐름의 연결입니다. 

## 5. 구조상 얻은 경험

- UMG에서 입력받은 선택을 게임플레이 컴포넌트와 연결했습니다.
- 시간 상태를 조명과 HUD가 함께 소비하도록 구성했습니다.
- 엔진·외부 플러그인이 제공하는 세션 기능을 사용자 화면으로 연결했습니다.
- 같은 주제의 프로토타입과 최종 경로를 구분해서 유지·설명하는 필요성을 확인했습니다.


