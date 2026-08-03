# SpesIGNOTA

> 붕괴 중인 우주 관측 정거장 **SPES**에서 전력을 복구하고 셔틀로 탈출하는 VR SF 어드벤처.

SpesIGNOTA는 Unity 기반으로 제작 중인 졸업작품 VR 프로젝트입니다. 플레이어는 정비 대원이 되어 우주선 내부와 무중력 선외 구역을 오가며, 손으로 잡고·스캔하고·소켓에 장착하고·수리하는 물리 상호작용을 통해 탈출 경로를 복구합니다.

<img width="1920" height="1080" alt="1페이지 용 대표 이미지" src="https://github.com/user-attachments/assets/25061fb2-5ae8-4133-91bc-1c024ac7d222" />

## 프로젝트 개요

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | SpesIGNOTA / Project: ACCRETION |
| 장르 | VR SF 탈출 어드벤처 |
| 플랫폼 | Android XR / Meta Quest OpenXR 프로파일 구성 |
| 엔진 | Unity 6 (`6000.0.71f1`) |
| 진행 상태 | 제작 중 |
| 목표 플레이 시간 | 핵심 진행 15-17분, 첫 플레이 최대 20분 |

## 게임 목표와 플레이 흐름

블랙홀 강착원반의 플라스마 교란으로 관측 정거장 SPES의 주 전력과 셔틀 항법이 끊어집니다. 플레이어는 내부 정비를 마친 뒤 무중력 선외 구역에서 항법 릴레이를 회수하고, 셔틀의 점화 회로를 복구해 탈출합니다.

<img width="1920" height="1080" alt="1페이지용 추가 이미지" src="https://github.com/user-attachments/assets/965b6b04-6cc5-47d3-8d02-f4373ff6fbda" />


1. 경보와 안내를 따라 정비 구역으로 이동합니다.
2. 스캐너로 손상 패널과 부품 위치를 확인하고, 퓨즈를 회수해 소켓에 장착합니다.
3. 에어록을 통과해 무중력 구역으로 진입하고, 테더를 안전 고리에 고정합니다.
4. 드릴로 통로를 막은 자성 전류석을 제거하고, 추적하는 위장 소행성을 공간 구조물로 유인합니다.
5. 항법 릴레이와 전력 부품을 셔틀에 장착한 뒤, 레버·버튼 조작으로 점화하여 탈출합니다.

## 핵심 VR 경험

### 무중력 이동과 안전한 선외 활동

- `ZeroGPlayerMotorRB`를 중심으로 부스터 이동, 자세 제어, 중력/무중력 전환을 Rigidbody 물리와 결합했습니다.
- `RopeSocketSnapTetherRB`는 테더 최대 길이를 FixedUpdate에서 제한하고, 초과 이동은 위치 및 바깥 방향 속도를 보정합니다. 무중력 구역에서 이탈 위험을 게임 규칙이자 안전장치로 설계했습니다.
- 에어록 전환에서는 플레이어와 주변 Rigidbody의 중력 상태·속도를 함께 관리해 내부 보행과 선외 부유 상태를 연결합니다.

### 손 기반 물리 상호작용

- XR Interaction Toolkit과 OpenXR 입력을 기반으로 잡기, 장비 사용, 소켓 스냅, 레버·버튼 조작을 구성했습니다.
- 휴대 스캐너로 목표물과 손상 부위를 탐색하고, 드릴 도구로 자성 전류석의 에너지를 소진시키는 정비 상호작용을 구현했습니다.
- 부품 회수와 장착은 월드 공간 인벤토리·소켓 시스템과 연결해, HUD 메뉴보다 손의 위치와 오브젝트 상태로 진행을 이해하도록 설계했습니다.

### VR 멀미 저감과 가독성

- 보행 시 터널 비네팅의 개구부·페더링·전환 시간을 제어할 수 있도록 구성했습니다.
- 수리 모드에서는 플레이어를 작업 앵커에 정렬하고 이동 및 몸통 회전을 잠가, 정밀 조작 중 불필요한 카메라 이동을 줄입니다.
- 목표 안내는 강제 카메라 회전 대신 노바 AI의 음성·월드 자막·점멸등·스캐너 방향 정보로 전달합니다.
- 셔틀 탈출은 고속 직접 조종 대신 플레이어가 앉은 실내의 안정된 기준점을 유지하는 연출로 계획했습니다.

<img width="1920" height="1080" alt="2페이지용 추가 이미지" src="https://github.com/user-attachments/assets/06ccafcc-53ef-4b16-ac31-5276139008e3" />

## 기술 구현

### 시나리오와 퀘스트

- `VRScenarioManager`는 시나리오 ID, 시작 조건, 순차 스텝, 목표 갱신, 완료 플래그와 저장 데이터를 관리합니다.
- 입력 차단, 대화, 조명 변화, 수리 모드, 타임라인, UnityEvent를 시나리오 스텝으로 연결해 씬 연출과 게임플레이 이벤트를 분리했습니다.
- `QuestManager`는 ScriptableObject 기반 퀘스트 세트와 이벤트 페이로드를 연결하여, 회수·장착·수리 완료 같은 행동을 진행 상태와 HUD에 반영합니다.

### 우주 환경 렌더링 최적화

- 대량의 배경 소행성은 개별 GameObject 대신 `AsteroidInstancingController`에서 데이터로 관리하고, 같은 Mesh/Material 조합을 묶어 `Graphics.DrawMeshInstanced`로 배치 렌더링합니다. Unity 인스턴싱 한도에 맞춰 최대 1,023개 단위 버퍼를 사용합니다.
- 소행성 표면 변형은 알베도·노멀·메탈릭 Texture2DArray와 MaterialPropertyBlock의 인스턴스별 값을 사용해, 재질을 늘리지 않고 시각적 다양성을 제공합니다.
- 생성 시 `Physics.OverlapSphereNonAlloc`과 재사용 Collider 버퍼로 금지 구역을 검사해 GC 할당을 줄이고, 충돌·제거 대상은 데이터 슬롯을 재활용하는 방식으로 설계했습니다.
- 상호작용이 필요한 소행성만 실제 물리 오브젝트로 전환하는 구조를 두어, 배경 밀도와 물리 상호작용 비용을 분리합니다.

### 렌더링·플랫폼 구성

- Universal Render Pipeline(URP), Unity Input System, OpenXR, XR Interaction Toolkit을 사용합니다.
- Android OpenXR 설정에 Meta Quest 지원과 Touch Plus 컨트롤러 프로파일을 활성화했습니다.
- PC 화면 확인을 위해 XR HMD 카메라와 별도로 XR 렌더링을 끈 16:9 관전자 카메라를 동기화하는 `XRSpectatorCameraMirror`를 구현했습니다.

## 프로젝트 구조

```text
Assets/@Scripts/
├── GamePlay/
│   ├── Player/       # 무중력 모터, 손 입력·그립, 아바타
│   ├── RopeTether/   # 테더, 소켓 스냅
│   ├── Asteroid/     # 인스턴싱, 표면 데이터, 채굴
│   ├── Quest/        # ScriptableObject 기반 퀘스트·이벤트
│   ├── Dialogue/     # 노바 안내 및 월드 자막
│   └── UI/           # HUD, 미니맵, 홀로그램 맵
├── Manager/Scenario/ # 시나리오 시퀀스와 이벤트 제어
└── Manager/          # 수리 모드, 화면 페이드, 게임 상태
```

## 실행 방법

1. Unity Hub에서 Unity `6000.0.71f1`로 프로젝트를 엽니다.
2. Package Manager가 의존 패키지를 복원할 때까지 기다립니다.
3. Android 빌드 대상에서는 OpenXR과 Meta Quest 관련 Project Settings를 확인합니다.
4. XR HMD를 연결해 Play Mode 또는 Android 빌드에서 동작을 확인합니다.

## 사용 기술

`Unity 6` `C#` `URP` `OpenXR` `XR Interaction Toolkit` `Input System` `Odin Inspector` `DOTween` `VFX Graph`


## 참고 사항
- 본 저장소는 제작 중인 프로젝트입니다. 
- 대표 이미지와 게임플레이 영상은 추후 추가 예정입니다.
