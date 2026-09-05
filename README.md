# 진주형 | 게임 개발 프로젝트

## Intro

Unreal Engine에서 C++와 Blueprint로 게임플레이·UI·데이터 흐름을 구현해 온 진주형입니다. 최근에는 AI 에이전트의 작업 경계와 완료 조건을 정의하고, 실행·테스트 결과로 산출물을 검증하는 개발 방식까지 경험을 넓혔습니다.

포트폴리오에서 짧게 소개했던 프로젝트를 이곳에 자세히 정리했습니다. **각 ‘프로젝트 상세 설명’ 페이지에서 기능별 화면, 구현 방식, 데이터 흐름, 문제 해결과 본인 담당 범위를 한 번에 읽을 수 있습니다.**

## Unreal Engine · 소스 분석 프로젝트

### 1. K-현실고증 시뮬레이터

> AI 생성 대화를 캐릭터·감정·음성·카메라가 있는 Unreal 장면으로 재생하고 편집하는 숏폼 제작 도구

- 프로젝트 형태: 유튜브 ‘K-현실고증’ 측과 함께한 기업 협업 프로젝트
- 개발 기간·인원: 2024.06–12 · 2인 팀
- 핵심 역할: Unreal 클라이언트 — 대화 구조체 변환, 15개 UMG 흐름, 장면 연출·편집·다시보기
- 기술: Unreal Engine 5.4, C++, Blueprint, UMG, HTTP/JSON 연동
- 결과: Windows 실행 패키지 · 2024 메타버스 개발자 경진대회 우수상(팀)

**[프로젝트 상세 설명 →](projects/k-reality-simulator/DETAILS.md)** · [시연 영상](https://youtu.be/DKNxsU4mA8I)

### 2. ThiefCode — 정수 로그라이트

> AI 에이전트 기반으로 개발하고, 작업 계약·자동 검증·증빙 인계를 묶은 하네스로 통합한 액션 로그라이트

- 작업 기간·인원: 2026.08.10–27 · 2인 팀
- 핵심 역할: 하네스 설계, 모듈·작업 경계 정의, AI 산출물 통합·검증
- 기술: Unreal Engine 5.8, C++, GAS, StateTree, UE-MCP, Python Editor Automation, Diversion
- 세부 기능: 자동 전투·대시, 몬스터 스킬, 조각 장착·합성, 진행도 저장, HUD·미니맵
- 결과: Win64 Development 패키지·저장된 검증 기록 · Pixel Streaming 웹 플레이 경로로 해커톤 제출

**[프로젝트 상세 설명 →](projects/thiefcode/DETAILS.md)**

### 3. Minecraft 모작

> 절차적 복셀 월드와 런타임 블록 수정, 아이템·인벤토리를 구현한 C++ 샌드박스

- 개발 형태: 1인 개인 프로젝트
- 핵심 역할: 청크·블록 배열, Naive/Greedy Meshing, 메시 재생성, 아이템·UMG
- 기술: Unreal Engine 5, C++, Blueprint, FastNoiseLite, Procedural Mesh, DataTable, UMG
- 세부 기능: 2D 높이·3D 밀도 지형, 가시 면 병합, 블록 파괴·배치, 아이템 획득·스택·월드 드롭

**[프로젝트 상세 설명 →](projects/minecraft/DETAILS.md)** · [시연 영상](https://youtu.be/L7N0lZDtYAo)

### 4. KZG — A Trail of Survival

> 인벤토리·건설·시설물·시간·세션 UI를 연결한 3인칭 좀비 생존 프로젝트

- 개발 기간·인원: 2023.10–12 · 7인 팀
- 핵심 역할: Blueprint 생존 시스템·UI, 공통 인벤토리·컨테이너, C++ 상호작용 입력 보완
- 기술: Unreal Engine 5, Blueprint, UMG, ActorComponent, DataTable, Online Subsystem Steam, Advanced Sessions
- 세부 기능: 슬롯 이동·사용·드롭, Trace·Interface·아웃라인, 건설 Preview, 낮·밤 HUD, 호스트 생성·검색·참가
- 결과: 메타버스 아카데미 성과공유회 장려상(팀)

**[프로젝트 상세 설명 →](projects/kzg/DETAILS.md)** · [시연 영상](https://youtu.be/0tP8uwXulIc)

### 5. 오버워치 모작 — 겐지

> 겐지·트레이서가 대결하는 멀티플레이 데스매치 프로토타입

- 개발 기간·인원: 2023.08 · 7인 팀
- 핵심 역할: 겐지 스킬, 1인칭·3인칭 애니메이션, 팀 RPC·복제 기반 연동
- 기술: Unreal Engine 5.2, Blueprint, AnimBP, IK Retargeter, Blend Space, Aim Offset, Montage
- 세부 기능: 좌·우클릭 표창, 질풍참, 튕겨내기, 근접 공격, 용검

**[프로젝트 상세 설명 →](projects/overwatch-genji/DETAILS.md)** · [시연 영상](https://youtu.be/fYp8vJ2BdDE)

### 6. LostArk 모작

> 하우징과 아이소메트릭 보스 전투를 시연한 학습용 프로젝트

- 핵심 역할: 플레이어 담당 — 위치 지정 공격·스킬, 쿨타임·캐스팅 취소, 피격·사망·전투 UI
- 기술: Unreal Engine 5.2, Blueprint, UMG, ActorComponent, Montage, 애니메이션 리타기팅, Collision, Git
- 세부 기능: 블레이즈·아이스 애로우·종말의 날·천벌·익스플로전·리액트·돌풍, HP·피해 수치·효과 연결
- 상세 설명: 원본 프로젝트와 작성 이력 기반. 플레이어 담당과 팀 C++ 보스·하우징을 구분

**[프로젝트 상세 설명 →](projects/lostark-clone/DETAILS.md)** · [시연 영상](https://youtu.be/kG_SeeEKs6A)

## 추가 프로젝트 · 영상 및 교육 프로젝트

### 7. Risk of Risk

> 적 전투·아이템 획득·텔레포터 목표·보스전으로 진행하는 프로토타입

- 핵심 역할: 적·보스 AI, 게임 시스템, UI 담당
- 상세 설명: 시작 흐름, 필드 전투, 상자·아이템, 보스 공격과 HUD

**[프로젝트 상세 설명 →](projects/other-projects/risk-of-risk.md)** · [시연 영상](https://youtu.be/wRpARYSkSx0)

### 8. 청소부들 VR

> VR 포인터로 오브젝트를 선택·배치하고 작업을 진행하는 프로토타입

- 핵심 역할: 오브젝트 선택·배치
- 상세 설명: 차량·가구 선택 UI와 배치, 팀 세척·체크리스트 기능의 경계

**[프로젝트 상세 설명 →](projects/other-projects/cleaners-vr.md)** · [시연 영상](https://youtu.be/SgpD5AwSxGM)

### 9. 슬기로운 돼지 키우기

> 축사 시점·개체 선택·검사 진행·결과 패널을 연결한 2박 3일 프로토타입

- 핵심 역할: 카메라·UI
- 상세 설명: 시점 전환, 아웃라인·이름 표시, 개체별 로딩과 결과 UI
- 범위: UI 프로토타입이며 실제 질병 진단 성능을 주장하지 않음

**[프로젝트 상세 설명 →](projects/other-projects/wise-pig-raising.md)** · [시연 영상](https://www.youtube.com/watch?v=-oQ2TOpFXZE)

### 10. SmartGlassAR — AR Stamp Tour

> Unity 미러링 초기안의 제약을 확인하고 Android/Kotlin으로 방향을 전환한 스마트글라스 스탬프 투어

- 개발 기간·인원: 2026.07–08 · 4인 팀
- 핵심 역할: 기술 전환·파이프라인 재구성 참여, AI 결과 교차검증, Task·Git 조율, 디버그·수정 참여
- 플랫폼·팀 기술: Android/Kotlin, Meta 연동, BLE, TensorFlow Lite, Jetpack Compose
- 세부 기능: 근접 구역·미션, 카메라·객체 인식, 검출 안정화·재검증, 완료·스탬프 UI
- 개발 방식: CLI·MCP 기반 AI 보조 개발. 비전 모듈·최종 UI는 팀 결과로 구분

**[프로젝트 상세 설명 →](projects/smartglass/DETAILS.md)** · [시연 영상](https://youtu.be/yOnPj3VstqE)

## 기록 안내

본인 구현·팀 공통 기반·AI 생성 산출물·외부 도구를 구분합니다. 저장된 테스트 기록은 당시 조건의 결과이며, 미측정 성능·미완료 기능은 완료 성과로 적지 않습니다. Risk of Risk·청소부들 VR·슬기로운 돼지 키우기는 영상과 기존 기여 기록 중심으로 설명하며 내부 알고리즘을 추정하지 않습니다.

이 저장소는 기술 설명을 위한 문서 모음입니다. 원본 프로젝트 전체·상용 에셋·내부 로그를 배포하지 않습니다.

[GitHub 프로필](https://github.com/Acdok) · [이미지 출처와 권리 안내](NOTICE.md)
