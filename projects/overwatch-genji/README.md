# 오버워치 모작 — 겐지 전투·애니메이션

> 비상업적 학습 목적으로 제작한 팀 프로젝트입니다. 원작 `Overwatch`와 캐릭터·상표의 권리는 Blizzard Entertainment에 있습니다.

[시연 영상 보기](https://youtu.be/fYp8vJ2BdDE)

![겐지 플레이 장면](../../assets/overwatch-genji/genji_gameplay_clean.jpg)

*시연 영상의 겐지 플레이 장면입니다. 겐지 기능에는 본인 기여가 포함되며, 맵·HUD·공통 네트워크 흐름은 팀 결과입니다.*

## 프로젝트 개요

| 항목 | 내용 |
|---|---|
| 개발 기간 | 2023.08 |
| 구성 | 7인 팀 프로젝트 |
| 개발 당시 엔진 | Unreal Engine 5.2 |
| 본인 역할 | 겐지 전투 기능, 1인칭·3인칭 애니메이션 구성, 팀 네트워크 기반과의 연동 |
| 팀 결과 | Listen Server 기반 겐지·트레이서 데스매치 프로토타입 |

Blueprint로 겐지의 표창·질풍참·튕겨내기·근접 공격·용검을 구성하고, 별도의 1인칭·3인칭 Animation Blueprint와 IK Retargeter를 전투 상태에 연결했습니다. 공통 C++ 캐릭터와 Server RPC·Replication 기반은 팀원이 주도했으며, 본인은 그 진입점에 겐지 Blueprint 이벤트와 일부 상태를 연동했습니다.

## 핵심 기여

- 좌·우클릭 표창, 질풍참, 튕겨내기, 근접 공격과 용검의 시작·공격·종료 흐름 구성
- `PlayerAttack`, `DashCollision`, `ReflectCollision`, `pyochang` 등 스킬별 충돌 규칙을 겐지 기능에 연결
- 1인칭 `ABP_Genji`와 3인칭 `ABP_3GenJi`를 분리하고 IK Retargeter, Blend Space, Aim Offset, Montage를 조합
- 팀의 입력·Server RPC·Replication 기반에 겐지 스킬 이벤트와 일부 전투 상태 연동

![겐지 Animation Blueprint](../../assets/overwatch-genji/genji_animbp.jpg)

*겐지 애니메이션 구성 화면입니다. 원본 애니메이션 제작이 아니라 리타기팅, 상태머신·블렌딩 구성과 전투 기능 연결이 본인 기여입니다.*

## 구현 근거

공개 저장소 기준 상대 경로만 표기합니다.

- 겐지 중심 Blueprint: `Content/JJH/blueprint/BP_Genji2.uasset`
- 표창: `Content/JJH/blueprint/BP_Pyochang.uasset` 및 좌·우 투사체 변형
- 튕겨내기: `Content/JJH/blueprint/Reflect.uasset`, `Content/JJH/blueprint/TracerReflect.uasset`
- 1인칭·3인칭 AnimBP: `Content/JJH/Animation/ABP_Genji.uasset`, `Content/JJH/Animation/ABP_3GenJi.uasset`
- IK: `Content/JJH/Retarget/IK_Genji.uasset`, `Content/JJH/Retarget/IK_3Genji.uasset`, `Content/JJH/Retarget/NewIKRetargeter.uasset`
- Aim Offset: `Content/JJH/Animation/GenjiAimoffset.uasset`
- 충돌 프로필: `Config/DefaultEngine.ini`
- 팀 공통 입력·RPC 기반: `Source/OValTan/OValTanCharacter.h`, `Source/OValTan/OValTanCharacter.cpp`

스킬별 흐름과 소유권 구분은 [겐지 기능 상세](GENJI_FEATURES.md)에 정리했습니다.

## 시연 구간

| 구간 | 확인할 수 있는 내용 | 구분 |
|---|---|---|
| 00:37–03:44 | 겐지 1인칭 조작과 전투 스킬 | 본인 핵심 기여가 포함된 장면 |
| 03:45–04:35 | 트레이서 조작과 스킬 | 팀원 기여 |
| 04:36–07:08 | 겐지·트레이서 대전, HUD·점수·사망·리스폰 | 본인 기능이 포함된 팀 결과 |
| 07:09–07:23 | 경기 종료 연출 | 팀 결과 |

## 기여 범위와 한계

- 본인 구현으로 설명하는 범위는 겐지 Blueprint 전투, 애니메이션 리타기팅·상태 구성, 스킬별 충돌 연결과 팀 네트워크 기반 연동입니다.
- 트레이서 기능, 공통 C++ 캐릭터, PlayerController·GameState, 로비·세션과 공통 UI는 팀원이 주도했습니다.
- 모든 전투 상태가 완전한 서버 권위 구조로 검증됐다고 주장하지 않습니다.
- 지연·동기화·성능의 정량 측정 기록은 없으며, 상용 수준 네트워크 완성도를 의미하지 않습니다.
- Blueprint 자산은 바이너리이므로 자산 경로, 작성 이력, 설정과 시연 영상을 함께 근거로 사용했습니다.
