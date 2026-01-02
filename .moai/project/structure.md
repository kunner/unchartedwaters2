# Uncharted Waters 2 - 아키텍처 및 구조 문서

## 프로젝트 개요

**프로젝트명**: Uncharted Waters 2 (대항해시대 2)
**아키텍처 패턴**: Component-Based Architecture (ECS Pattern)
**게임 엔진**: Unity (C#) / 대안: Defold (Lua)
**문서 작성일**: 2025-01-02
**문서 상태**: Active

---

## 전체 아키텍처 개요

### 아키텍처 다이어그램

```
+------------------------------------------------------------------+
|                         Presentation Layer                         |
|  +------------+  +------------+  +------------+  +------------+   |
|  |  UI System |  | Input Mgr  |  | Audio Mgr  |  | Scene Mgr  |   |
|  +------------+  +------------+  +------------+  +------------+   |
+------------------------------------------------------------------+
                                |
+------------------------------------------------------------------+
|                          Game Logic Layer                          |
|  +------------------+  +------------------+  +------------------+ |
|  | Navigation Sys   |  | Trading System   |  | Combat System    | |
|  +------------------+  +------------------+  +------------------+ |
|  +------------------+  +------------------+  +------------------+ |
|  | Ship Management  |  | Crew Management  |  | Quest System     | |
|  +------------------+  +------------------+  +------------------+ |
+------------------------------------------------------------------+
                                |
+------------------------------------------------------------------+
|                        Core Systems Layer                          |
|  +------------+  +------------+  +------------+  +------------+   |
|  | ECS Engine |  | Physics    |  | Event Bus  |  | State Mgr  |   |
|  +------------+  +------------+  +------------+  +------------+   |
+------------------------------------------------------------------+
                                |
+------------------------------------------------------------------+
|                         Data Layer                                 |
|  +------------------+  +------------------+  +------------------+ |
|  | Save/Load System |  | Asset Manager    |  | Config Manager   | |
|  +------------------+  +------------------+  +------------------+ |
+------------------------------------------------------------------+
                                |
+------------------------------------------------------------------+
|                     External Services Layer                        |
|  +------------+  +------------+  +------------+  +------------+   |
|  | Analytics  |  | IAP        |  | Cloud Save |  | Push Notif |   |
|  +------------+  +------------+  +------------+  +------------+   |
+------------------------------------------------------------------+
```

### 핵심 아키텍처 원칙

1. **Component-Based Design (ECS)**: 엔티티-컴포넌트-시스템 패턴으로 유연한 게임 오브젝트 구성
2. **Fixed Timestep Game Loop**: 60Hz 고정 타임스텝으로 일관된 물리 시뮬레이션
3. **State Machine**: 게임 상태 관리를 위한 유한 상태 기계
4. **Object Pooling**: 메모리 효율성을 위한 오브젝트 풀링
5. **Asynchronous Message Routing**: 비동기 메시지 라우팅을 통한 시스템 간 통신

---

## 디렉토리 구조

### Unity 프로젝트 구조

```
UnchartedWaters2/
├── Assets/
│   ├── Scenes/
│   │   ├── Main/
│   │   │   ├── MainMenu.unity
│   │   │   ├── GameWorld.unity
│   │   │   └── Loading.unity
│   │   ├── Test/
│   │   │   └── TestScene.unity
│   │   └── UI/
│   │       ├── HUD.unity
│   │       └── Dialogs.unity
│   │
│   ├── Scripts/
│   │   ├── Core/
│   │   │   ├── ECS/
│   │   │   │   ├── Entity.cs
│   │   │   │   ├── Component.cs
│   │   │   │   └── System.cs
│   │   │   ├── GameLoop/
│   │   │   │   ├── GameManager.cs
│   │   │   │   └── TimeManager.cs
│   │   │   ├── Events/
│   │   │   │   ├── EventBus.cs
│   │   │   │   └── GameEvents.cs
│   │   │   └── StateMachine/
│   │   │       ├── StateMachine.cs
│   │   │       └── GameStates.cs
│   │   │
│   │   ├── Systems/
│   │   │   ├── Navigation/
│   │   │   │   ├── NavigationSystem.cs
│   │   │   │   ├── WindSystem.cs
│   │   │   │   ├── CurrentSystem.cs
│   │   │   │   └── WeatherSystem.cs
│   │   │   ├── Physics/
│   │   │   │   ├── ShipPhysics.cs
│   │   │   │   ├── WaveSimulation.cs
│   │   │   │   └── CollisionHandler.cs
│   │   │   ├── Trading/
│   │   │   │   ├── TradingSystem.cs
│   │   │   │   ├── MarketEconomy.cs
│   │   │   │   └── InventoryManager.cs
│   │   │   ├── Combat/
│   │   │   │   ├── CombatSystem.cs
│   │   │   │   ├── WeaponSystem.cs
│   │   │   │   └── DamageCalculator.cs
│   │   │   └── Quest/
│   │   │       ├── QuestSystem.cs
│   │   │       ├── QuestTracker.cs
│   │   │       └── RewardHandler.cs
│   │   │
│   │   ├── Entities/
│   │   │   ├── Ships/
│   │   │   │   ├── ShipEntity.cs
│   │   │   │   ├── ShipComponents.cs
│   │   │   │   └── ShipTypes.cs
│   │   │   ├── Crew/
│   │   │   │   ├── CrewMember.cs
│   │   │   │   └── CrewManager.cs
│   │   │   ├── Ports/
│   │   │   │   ├── PortEntity.cs
│   │   │   │   └── PortServices.cs
│   │   │   └── Items/
│   │   │       ├── ItemBase.cs
│   │   │       ├── Cargo.cs
│   │   │       └── Equipment.cs
│   │   │
│   │   ├── UI/
│   │   │   ├── HUD/
│   │   │   │   ├── NavigationHUD.cs
│   │   │   │   ├── ResourceBar.cs
│   │   │   │   └── MiniMap.cs
│   │   │   ├── Menus/
│   │   │   │   ├── MainMenu.cs
│   │   │   │   ├── SettingsMenu.cs
│   │   │   │   └── PauseMenu.cs
│   │   │   ├── Dialogs/
│   │   │   │   ├── DialogSystem.cs
│   │   │   │   ├── TradeDialog.cs
│   │   │   │   └── QuestDialog.cs
│   │   │   └── Common/
│   │   │       ├── UIManager.cs
│   │   │       ├── UIAnimations.cs
│   │   │       └── TouchHandler.cs
│   │   │
│   │   ├── Data/
│   │   │   ├── Models/
│   │   │   │   ├── ShipData.cs
│   │   │   │   ├── PortData.cs
│   │   │   │   ├── ItemData.cs
│   │   │   │   └── QuestData.cs
│   │   │   ├── Persistence/
│   │   │   │   ├── SaveSystem.cs
│   │   │   │   ├── SaveData.cs
│   │   │   │   └── CloudSave.cs
│   │   │   └── Config/
│   │   │       ├── GameConfig.cs
│   │   │       ├── BalanceConfig.cs
│   │   │       └── LocalizationConfig.cs
│   │   │
│   │   ├── Services/
│   │   │   ├── Analytics/
│   │   │   │   └── AnalyticsService.cs
│   │   │   ├── IAP/
│   │   │   │   └── IAPManager.cs
│   │   │   ├── Ads/
│   │   │   │   └── RewardedAdsManager.cs
│   │   │   └── Network/
│   │   │       └── NetworkManager.cs
│   │   │
│   │   └── Utilities/
│   │       ├── ObjectPool.cs
│   │       ├── Extensions.cs
│   │       ├── MathHelpers.cs
│   │       └── DebugTools.cs
│   │
│   ├── Prefabs/
│   │   ├── Ships/
│   │   │   ├── Caravel.prefab
│   │   │   ├── Galleon.prefab
│   │   │   └── Junk.prefab
│   │   ├── UI/
│   │   │   ├── Buttons/
│   │   │   ├── Panels/
│   │   │   └── Icons/
│   │   ├── Effects/
│   │   │   ├── WaterEffects/
│   │   │   ├── WeatherEffects/
│   │   │   └── CombatEffects/
│   │   └── World/
│   │       ├── Ports/
│   │       └── Environment/
│   │
│   ├── Art/
│   │   ├── Sprites/
│   │   │   ├── Ships/
│   │   │   ├── Characters/
│   │   │   ├── Items/
│   │   │   └── UI/
│   │   ├── Textures/
│   │   │   ├── Water/
│   │   │   ├── Land/
│   │   │   └── Sky/
│   │   ├── Animations/
│   │   │   ├── Ships/
│   │   │   ├── Characters/
│   │   │   └── UI/
│   │   └── Materials/
│   │       ├── Water/
│   │       └── Standard/
│   │
│   ├── Audio/
│   │   ├── Music/
│   │   │   ├── MainTheme.mp3
│   │   │   ├── Navigation.mp3
│   │   │   ├── Combat.mp3
│   │   │   └── Port.mp3
│   │   ├── SFX/
│   │   │   ├── Ships/
│   │   │   ├── Weather/
│   │   │   ├── Combat/
│   │   │   └── UI/
│   │   └── Voice/
│   │       └── Narration/
│   │
│   ├── Data/
│   │   ├── ScriptableObjects/
│   │   │   ├── Ships/
│   │   │   ├── Items/
│   │   │   ├── Ports/
│   │   │   └── Quests/
│   │   ├── JSON/
│   │   │   ├── WorldMap.json
│   │   │   ├── TradeRoutes.json
│   │   │   └── HistoricalEvents.json
│   │   └── Localization/
│   │       ├── en.json
│   │       ├── ko.json
│   │       └── ja.json
│   │
│   ├── Plugins/
│   │   ├── Box2D/
│   │   ├── DOTween/
│   │   ├── Analytics/
│   │   └── IAP/
│   │
│   ├── Resources/
│   │   └── (Runtime loaded assets)
│   │
│   └── Editor/
│       ├── Tools/
│       │   ├── MapEditor.cs
│       │   ├── ShipEditor.cs
│       │   └── QuestEditor.cs
│       └── Tests/
│           └── EditorTests/
│
├── Packages/
│   └── manifest.json
│
├── ProjectSettings/
│   ├── ProjectSettings.asset
│   ├── QualitySettings.asset
│   ├── InputManager.asset
│   └── TagManager.asset
│
├── Tests/
│   ├── EditMode/
│   │   ├── Core/
│   │   ├── Systems/
│   │   └── Data/
│   └── PlayMode/
│       ├── Navigation/
│       ├── Trading/
│       └── Combat/
│
├── Builds/
│   ├── Android/
│   └── iOS/
│
├── Documentation/
│   ├── GameDesign/
│   ├── Technical/
│   └── API/
│
└── .moai/
    ├── project/
    ├── specs/
    └── config/
```

---

## 핵심 모듈 및 의존성

### 1. Core Module (핵심 모듈)

**책임**: 게임의 기반 시스템 제공

| 컴포넌트 | 설명 | 의존성 |
|----------|------|--------|
| ECS Engine | 엔티티-컴포넌트-시스템 관리 | None |
| Game Loop | 60Hz 고정 타임스텝 게임 루프 | ECS Engine |
| Event Bus | 비동기 이벤트 발행/구독 | None |
| State Machine | 게임 상태 전환 관리 | Event Bus |

### 2. Navigation Module (항해 모듈)

**책임**: 항해 관련 모든 시스템 관리

| 컴포넌트 | 설명 | 의존성 |
|----------|------|--------|
| Navigation System | 항해 경로 및 이동 관리 | Physics, Wind |
| Wind System | 바람 방향 및 세기 시뮬레이션 | Weather |
| Current System | 해류 시뮬레이션 | None |
| Weather System | 날씨 상태 관리 | Event Bus |

### 3. Physics Module (물리 모듈)

**책임**: Box2D 기반 물리 시뮬레이션

| 컴포넌트 | 설명 | 의존성 |
|----------|------|--------|
| Ship Physics | 선박 물리 (마찰, 저항, 관성) | Box2D |
| Wave Simulation | 파도 시뮬레이션 | Box2D |
| Collision Handler | 충돌 감지 및 처리 | Box2D |

### 4. Trading Module (무역 모듈)

**책임**: 경제 및 무역 시스템

| 컴포넌트 | 설명 | 의존성 |
|----------|------|--------|
| Trading System | 무역 거래 처리 | Inventory, Market |
| Market Economy | 동적 시장 가격 계산 | Data Layer |
| Inventory Manager | 화물 관리 | Data Layer |

### 5. Combat Module (전투 모듈)

**책임**: 해상 전투 시스템

| 컴포넌트 | 설명 | 의존성 |
|----------|------|--------|
| Combat System | 전투 흐름 관리 | Physics, Ships |
| Weapon System | 무기 발사 및 효과 | Physics |
| Damage Calculator | 피해 계산 | Ships, Crew |

### 6. Quest Module (퀘스트 모듈)

**책임**: 퀘스트 및 미션 시스템

| 컴포넌트 | 설명 | 의존성 |
|----------|------|--------|
| Quest System | 퀘스트 상태 관리 | Event Bus |
| Quest Tracker | 퀘스트 진행 추적 | UI |
| Reward Handler | 보상 처리 | Inventory |

---

## 데이터 흐름

### 게임 루프 데이터 흐름

```
Input → Game Manager → Systems → Entities → Renderer → Output
  ↑                       ↓
  └──────── Event Bus ────┘
```

### 항해 시스템 데이터 흐름

```
Weather System
      ↓
  Wind Data → Ship Physics → Ship Position → Navigation System
      ↓           ↓              ↓
  Current Data   Velocity    Path Update
      ↓           ↓              ↓
  Wave Effect   Rotation    UI Update
```

### 무역 시스템 데이터 흐름

```
Port Data → Market Economy → Price Calculation
                 ↓
          Trading System → Inventory Update
                 ↓
          Transaction Log → Save System
```

---

## 외부 시스템 통합

### 플랫폼 서비스

| 서비스 | Android | iOS | 설명 |
|--------|---------|-----|------|
| Analytics | Firebase | Firebase | 사용자 행동 분석 |
| IAP | Google Play Billing | StoreKit 2 | 인앱 결제 |
| Cloud Save | Google Play Games | Game Center | 클라우드 저장 |
| Push | Firebase Cloud Messaging | APNs | 푸시 알림 |

### 통신 프로토콜

- **저장 데이터**: JSON 직렬화
- **네트워크 통신**: REST API (HTTPS)
- **실시간 통신**: WebSocket (멀티플레이어 확장 시)

### 오류 처리 전략

- **네트워크 실패**: 지수 백오프 재시도 (최대 3회)
- **저장 실패**: 로컬 캐시 후 재시도
- **서비스 불가**: 오프라인 모드 전환

---

## 비기능 요구사항 (NFR)

### 성능 요구사항

| 항목 | 목표값 | 우선순위 |
|------|--------|----------|
| 프레임레이트 | 60 FPS (최소 30 FPS) | High |
| 로딩 시간 | 초기 5초 이내, 씬 전환 2초 이내 | High |
| 메모리 사용 | 500MB 이하 | High |
| 배터리 소모 | 1시간 플레이 시 20% 이하 | Medium |
| 네트워크 사용 | 세션당 10MB 이하 | Medium |

### 확장성 요구사항

| 항목 | 현재 | 목표 |
|------|------|------|
| 동시 엔티티 | 50 | 200+ |
| 맵 크기 | 1000x1000 | 5000x5000 |
| 항구 수 | 10 | 50+ |
| 저장 슬롯 | 3 | 10 |

### 보안 요구사항

- **저장 데이터 암호화**: AES-256
- **통신 암호화**: TLS 1.3
- **치트 방지**: 서버 검증 (IAP)
- **개인정보보호**: GDPR/CCPA 준수

### 접근성 요구사항

- **터치 영역**: 최소 44x44 dp
- **색맹 모드**: 지원
- **폰트 크기**: 조절 가능
- **진동 피드백**: 토글 가능

---

## 씬 계층 구조

### 메인 씬 구성

```
GameWorld.unity
├── Managers (DontDestroyOnLoad)
│   ├── GameManager
│   ├── AudioManager
│   ├── UIManager
│   └── SaveManager
│
├── World
│   ├── Ocean
│   │   ├── WaterSurface
│   │   ├── WaveSystem
│   │   └── WeatherEffects
│   ├── Land
│   │   ├── Continents
│   │   └── Islands
│   └── Ports
│       ├── PortPrefabs (Pool)
│       └── PortMarkers
│
├── Ships
│   ├── PlayerShip
│   └── NPCShips (Pool)
│
├── UI
│   ├── HUD
│   │   ├── NavigationPanel
│   │   ├── ResourceBar
│   │   └── MiniMap
│   ├── Overlays
│   │   └── WeatherOverlay
│   └── Dialogs (Pooled)
│
└── Systems
    ├── NavigationSystem
    ├── PhysicsSystem
    ├── TradingSystem
    └── CombatSystem
```

---

## 아키텍처 결정 기록

### ADR-001: ECS 패턴 채택

**결정**: Component-Based Architecture (ECS Pattern) 채택
**이유**: 모바일 게임의 유연한 엔티티 구성과 시스템 분리를 통한 성능 최적화
**대안 고려**: 전통적 OOP 상속 구조
**결과**: 코드 재사용성 향상, 시스템 간 결합도 감소

### ADR-002: Box2D 물리 엔진 선택

**결정**: Box2D 통합 물리 엔진 사용
**이유**: 2D 항해 시뮬레이션에 최적화된 물리 연산, 모바일 성능에 적합
**대안 고려**: Unity 내장 물리, 커스텀 물리 엔진
**결과**: 사실적인 선박 물리 시뮬레이션 구현 가능

### ADR-003: 고정 타임스텝 게임 루프

**결정**: 60Hz 고정 타임스텝 적용
**이유**: 물리 시뮬레이션의 일관성과 재현성 보장
**대안 고려**: 가변 타임스텝
**결과**: 디바이스 간 일관된 게임플레이 경험

### ADR-004: 비동기 이벤트 버스

**결정**: 중앙 집중형 이벤트 버스 시스템 도입
**이유**: 시스템 간 느슨한 결합, 확장성 확보
**대안 고려**: 직접 참조, Observer 패턴
**결과**: 새로운 시스템 추가 시 기존 코드 수정 최소화

---

## 문서 이력

| 버전 | 날짜 | 작성자 | 변경 내용 |
|------|------|--------|-----------|
| 1.0.0 | 2025-01-02 | Project Manager Agent | 초기 문서 작성 |

---

*이 문서는 MoAI-ADK 프로젝트 초기화 과정에서 자동 생성되었습니다.*
