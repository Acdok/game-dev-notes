# 복셀 월드와 Greedy Meshing

[프로젝트 개요](README.md) · [아이템·인벤토리·UMG](inventory-ui.md)

## 청크 단위로 데이터를 관리하는 구조

각 블록을 독립 Actor로 만드는 대신 `TArray<EBlock>`에 블록 종류를 저장하고 청크 단위로 메시를 생성합니다. 월드 배치, 블록 데이터, 메시 생성 결과를 구분하여 Naive·Greedy 구현이 공통 적용 경로를 사용하도록 했습니다.

| 구성 요소 | 역할 | 원본 프로젝트의 상대 경로 |
|---|---|---|
| `AChunkWorld` | 청크 배치, 생성 방식과 크기·재질 전달 | `Source/MineCraft/World/Worlds/ChunkWorld.cpp` |
| `AChunkBase` | 초기화·데이터 생성·메시 생성·적용 순서 | `Source/MineCraft/World/Chunk/ChunkBase.cpp` |
| `ANaiveChunk` | 가시 면을 개별 Quad로 만드는 기준 방식 | `Source/MineCraft/World/Chunk/NaiveChunk.cpp` |
| `AGreedyChunk` | 지형 데이터와 연속 면 병합 | `Source/MineCraft/World/Chunk/GreedyChunk.cpp` |
| `FChunkMeshData` | 정점·삼각형·법선·UV·색 데이터 | `Source/MineCraft/World/Util/ChunkMeshData.h` |
| `UVoxelFunctionLibrary` | 월드·청크·로컬 복셀 좌표 변환 | `Source/MineCraft/Player/VoxelFunctionLibrary.cpp` |

### 초기 생성 순서

`AChunkWorld::BeginPlay`는 `GenerationType`에 따라 2D 또는 3D 생성 함수를 호출합니다. 반복문 안에서 `SpawnActorDeferred`로 청크를 만들고 `Size`, `Frequency`, `Material`, `GenerationType`을 설정한 뒤 `FinishSpawningActor`를 호출합니다.

여기서 Deferred Spawn은 생성 전 속성을 주입하는 방식입니다. 작업 스레드에서 청크를 비동기 생성하거나 프레임별 생성량을 나누는 스트리밍 기능을 뜻하지 않습니다. 현재는 시작 시 설정 범위를 동기적으로 생성합니다.

`AChunkBase`의 공통 순서는 다음과 같습니다.

```text
Noise 설정
  → Setup: 블록 저장 공간 준비
  → GenerateHeightMap: 2D/3D 데이터 생성
  → GenerateMesh: Naive 또는 Greedy 메시 구성
  → ApplyMesh: Procedural Mesh Section에 반영
```

## Noise를 블록으로 변환

FastNoiseLite를 Perlin Noise·fBm으로 설정합니다. 청크 위치를 블록 단위로 변환한 오프셋과 청크 내부 좌표를 더해 Noise를 샘플링합니다.

| 생성 방식 | 현재 데이터 해석 |
|---|---|
| 2D 높이 | X/Y Noise를 청크 높이 범위로 변환하고 Stone·Dirt·Grass·Air의 층을 배치 |
| 3D 밀도 | X/Y/Z Noise가 0 이상이면 Air, 음수이면 Stone으로 설정 |

블록 저장 인덱스는 `Z * Size * Size + X * Size + Y`입니다. 공간 좌표를 1차원 배열로 변환하며, 각 축의 유효 범위는 청크 크기 안으로 제한합니다.

현재 Greedy 청크에는 나무·석탄을 확률적으로 추가하는 경로도 있습니다. 해당 경로는 `FMath` 난수를 사용하므로 Noise 설정만 같다고 전체 월드의 나무·자원 배치까지 항상 동일하다고 보장하지 않습니다. 난수 시드와 생성 조건을 통제한 재현성 검증은 후속 범위입니다.

## Naive와 Greedy의 차이

두 방식 모두 이웃 블록을 검사해 숨은 면을 제외합니다. Greedy만 숨은 면을 제거하는 것은 아닙니다.

| 방식 | 메시 생성 단위 |
|---|---|
| Naive | 노출된 블록 면마다 Quad 생성 |
| Greedy | 노출된 면 중 블록 종류와 법선이 같은 직사각형 영역을 하나의 Quad로 병합 |

따라서 Greedy의 추가 작업은 남은 가시 면의 연속 영역을 찾는 것입니다. 블록 종류가 다르거나 법선 방향이 다르면 같은 평면에 있어도 병합하지 않습니다.

## Greedy Meshing 처리 과정

![Procedural Mesh 와이어프레임 디버그 화면](../../assets/minecraft/procedural_mesh_debug.png)

*실제 프로젝트의 메시 디버그 화면입니다. 알고리즘 구조를 설명하기 위한 자료이며 Naive 대비 성능 향상 수치를 보여주는 전후 비교는 아닙니다.*

### 1. 축별 가시 면 마스크

X/Y/Z 중 하나를 주축으로 선택하고 나머지 두 축으로 2D 마스크를 구성합니다. 현재 블록과 주축 방향의 이웃 블록을 비교합니다.

| 현재 블록 / 이웃 블록 | 마스크 |
|---|---|
| 둘 다 Air 또는 둘 다 불투명 | 면 없음 |
| 현재만 불투명 | 현재 블록 종류와 양의 법선 |
| 이웃만 불투명 | 이웃 블록 종류와 음의 법선 |

소스는 `Air`가 아닌 블록을 불투명으로 취급합니다. 투명 블록과 반투명 재질 사이의 면 처리까지 일반화한 구현은 아닙니다.

### 2. 폭과 높이 확장

유효한 마스크에서 시작해 같은 `Block`·`Normal`을 가진 항목만 가로로 확장합니다. 이후 구한 폭 전체가 다음 행에서도 같은지 검사하며 세로로 확장합니다. `CompareMask`가 블록 종류와 법선의 일치를 판단합니다.

### 3. Quad 생성과 마스크 제거

찾은 직사각형의 네 모서리로 정점 4개와 삼각형 2개를 추가합니다. 법선 방향에 맞게 인덱스 순서를 구성하고 처리한 영역을 마스크에서 지운 뒤 다음 영역을 찾습니다.

`CreateQuad`에서는 다음 데이터를 함께 만듭니다.

- 블록 단위 좌표를 Unreal 좌표로 변환한 정점
- 앞·뒤 방향을 구분하는 삼각형 인덱스
- 법선
- 병합한 폭·높이에 대응하는 UV
- 블록·면 방향의 텍스처 인덱스를 담은 Vertex Color Alpha

이론상 넓은 동일 평면에서는 생성 Quad 수가 줄어들 수 있지만, 현재 문서에는 같은 지형·빌드·하드웨어에서 측정한 전후 수치를 싣지 않았습니다.

## 런타임 블록 수정

`AChunkBase::ModifyVoxel`은 청크 내부 좌표의 유효 범위를 먼저 검사합니다. 유효하면 블록 데이터를 수정하고 전체 메시 데이터를 비운 뒤 다시 생성·적용합니다.

```text
ModifyVoxel(청크 내부 좌표, 블록 종류)
  → ModifyVoxelData
  → ClearMesh
  → GenerateMesh
  → ApplyMesh
```

이 경로는 수정한 블록을 화면에 즉시 반영하기 위한 단순한 구조입니다. 수정 부분의 메시만 교체하는 Dirty 영역 갱신이 아니라 해당 청크 전체 재생성입니다.

`UVoxelFunctionLibrary`는 월드 좌표에서 블록·청크·로컬 좌표를 계산합니다. 음수 좌표 보정 코드가 있지만 정확한 음수 경계에서도 올바르다는 회귀 검증이 완료된 것은 아닙니다. 특히 정수 나눗셈 뒤 음수 축을 추가 보정하는 방식이므로 원점과 청크 경계에 대한 테스트가 필요합니다.

## 현재 한계와 다음 검증

| 현재 상태 | 후속 작업 |
|---|---|
| `GetBlock`이 청크 밖을 Air로 반환 | 이웃 청크 데이터 조회, 경계 블록 변경 시 인접 청크 갱신 |
| 시작 시 고정 범위 동기 생성 | 플레이어 위치 기반 로딩·언로딩과 생성 예산 관리 |
| 수정 청크 전체 메시 재생성 | Dirty 영역 분리와 비동기 생성 가능성 검토 |
| 음수 좌표 보정 존재 | 원점·음수 블록·정확한 청크 경계 사례 테스트 |
| 실행 중 월드 데이터 | Seed와 수정 복셀 Delta의 저장·복원 설계 |
| 알고리즘 구현·디버그 화면 | 같은 입력에서 생성 시간·정점·삼각형·프레임 비용 비교 |

예를 들어 성능을 비교하려면 Noise뿐 아니라 나무·자원 난수, 청크 수·크기, 재질·충돌 설정, 빌드 모드도 같아야 합니다. 이 조건의 벤치마크를 새로 수행하기 전에는 최적화 배수나 FPS 보장으로 표현하지 않습니다.
