# KZG | 반복 상호작용 입력 수정

[프로젝트 개요](README.md) · [인벤토리·상호작용](inventory-and-interaction.md)

## 문제

상호작용 입력이 `Triggered`에 연결되어 있어, 키를 길게 누르는 동안 같은 입력의 처리 경로가 반복 진입할 수 있었습니다. 한 번 누른 행동과 누르고 있는 상태를 구분할 필요가 있었습니다.

## 변경

본인의 2023-10-30 `PlayerDebug` 변경 이력을 기준으로 확인한 수정입니다.

1. 입력 시작은 `Started`에 연결했습니다.
2. 입력 종료는 `Completed`와 별도 종료 Server RPC에 연결했습니다.
3. 처리 중에는 `bIsInteractionInput` Guard로 재진입을 차단했습니다.
4. 종료 이벤트에서 상태를 `false`로 되돌렸습니다.
5. 상태 변수에 `Replicated`와 `DOREPLIFETIME` 등록을 추가했습니다.

```text
Started → 기존 Server/Multicast 경로 → Guard 확인 → 상호작용 처리
                                                ↓
                                       처리 중 상태 = true
Completed → 종료 Server/Multicast 경로 → 처리 중 상태 = false
```

### 핵심 로직 발췌

아래는 실제 변경의 Guard와 종료 상태만 발췌해 정리한 것입니다. 완전한 함수·독립 실행 예제가 아닙니다.

```cpp
// 상호작용 진입부
if (bIsInteractionInput) return;
// 각 처리 분기에 들어갈 때
bIsInteractionInput = true;

// Multicast_InteractionUnputEnd_Implementation()
bIsInteractionInput = false;
```

`Unput`은 원본 함수명의 철자입니다. 문서에서 임의로 다른 API인 것처럼 고치지 않았습니다.

## 이 수정에서 보여 주고 싶은 점

Blueprint 시스템을 팀 C++ 기반과 연결하면서 발생한 입력 문제를, 화면에서만 우회하지 않고 입력 이벤트와 상태의 경계에서 보완했습니다. 단순히 RPC를 추가한 사실보다 **시작·처리 중·종료를 구분한 것**이 핵심입니다.

기존 캐릭터·전투기반은 팀원이 작성한 부분입니다. 제 기여는 상호작용 RPC·Multicast와 본인 Blueprint 시스템의 연동입니다.

## 근거와 검증 범위

| 항목 | 상태 |
|---|---|
| Started/Completed 바인딩 변경 | 해당 커밋의 C++ diff로 확인 |
| 종료 RPC·Multicast, Guard 상태 추가 | 해당 커밋의 헤더·구현 diff로 확인 |
| 복제 등록 | `UPROPERTY(Replicated)`, `DOREPLIFETIME` 확인 |
| 실제 인벤토리·컨테이너 UI | 기존 실행 화면·공개 시연으로 확인 |
| 모든 네트워크 조건에서 입력 문제 해소 | 별도의 전체 회귀 결과를 제시하지 않음 |

복제 플래그가 있다는 사실만으로 서버 권한 검증·악성 입력 방지·모든 동시성 문제가 해결됐다고 주장하지 않습니다.

### 추가 회귀 테스트 항목

다음은 향후 검증을 명확히 하기 위한 체크리스트이며 완료 기록이 아닙니다.

- 한 번 누르기 / 길게 누르기 / 빠르게 반복 누르기
- 대상이 없는 상태에서 누른 뒤 다른 대상으로 전환
- 처리 도중 메뉴 열기·포커스 상실·입력 취소 시 상태 복구
- Listen Server 호스트와 원격 클라이언트의 동일 동작
- 지연·연결 해제·재접속에서 Guard가 남는지 확인
- 인벤토리 동시 접근 시 슬롯 수량과 UI 상태 일치

## 원본 프로젝트 위치

- `Source/KZG/Private/KZGCharacter.cpp`
- `Source/KZG/Public/KZGCharacter.h`

이 문서 저장소는 설명을 위한 별도 기록입니다. 원본 프로젝트 전체·팀 저장소 변경 이력·로그 파일을 복제해서 게시하지 않습니다.
