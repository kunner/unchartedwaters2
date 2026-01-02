# Uncharted Waters 2 - 기술 스택 문서

## 프로젝트 개요

**프로젝트명**: Uncharted Waters 2 (대항해시대 2)
**플랫폼**: iOS, Android (모바일 우선)
**개발 방식**: 솔로 개발자
**문서 작성일**: 2025-01-02
**문서 상태**: Active

---

## 기술 스택 명세

### 게임 엔진

#### Primary: Unity

| 항목 | 사양 |
|------|------|
| **엔진** | Unity 2022.3 LTS (장기 지원 버전) |
| **언어** | C# 10.0 |
| **렌더링** | Universal Render Pipeline (URP) |
| **대상 API** | OpenGL ES 3.0, Metal |
| **최소 Unity 버전** | 2022.3.0f1 |

**선택 이유**:
- 2D/모바일 개발에 최적화된 성능
- 내장 물리 엔진과 Box2D 통합 용이
- 광범위한 에셋 스토어 지원
- 크로스 플랫폼 빌드 지원

#### Alternative: Defold

| 항목 | 사양 |
|------|------|
| **엔진** | Defold 1.6.x |
| **언어** | Lua 5.1 (LuaJIT) |
| **라이선스** | 무료 (King 개발) |
| **특징** | 2D 전문, 경량화 |

**대안 고려 이유**:
- 경량화된 런타임으로 빠른 로딩
- 완전 무료 라이선스
- 2D 게임에 특화된 워크플로우

---

### 물리 엔진

#### Box2D Integration

| 항목 | 사양 |
|------|------|
| **버전** | Box2D 2.4.1 |
| **Unity 통합** | Physics2D API |
| **타임스텝** | 60Hz 고정 (0.01667초) |

**핵심 물리 구현**:

1. **선박 점성 마찰 (Viscous Friction)**
   - 물의 저항을 시뮬레이션
   - 속도에 비례하는 저항력 적용
   - 공식: F_drag = -C_d * v

2. **방향별 저항 (Directional Resistance)**
   - 선박 형태에 따른 비등방성 저항
   - 전진 저항 < 측면 저항
   - 공식: F_lateral = -C_lateral * v_perpendicular

3. **파도 시뮬레이션 (Wave Simulation)**
   - 사인파 기반 수면 시뮬레이션
   - 선박에 부력 및 흔들림 적용
   - 날씨 상태에 따른 파도 강도 조절

4. **바람 역학 (Wind Dynamics)**
   - 돛에 대한 바람 힘 계산
   - 풍향과 선박 방향에 따른 효율 계산
   - 공식: F_wind = 0.5 * rho * C_sail * A * v^2 * cos(theta)

---

### 아키텍처 패턴

#### Component-Based Architecture (ECS Pattern)

**구조**:
```
Entity (순수 ID)
  ↓
Components (데이터만 보유)
  ├── TransformComponent
  ├── PhysicsComponent
  ├── RenderComponent
  └── ShipComponent
  ↓
Systems (로직 처리)
  ├── MovementSystem
  ├── PhysicsSystem
  ├── RenderSystem
  └── NavigationSystem
```

**장점**:
- 데이터와 로직의 명확한 분리
- 컴포넌트 조합으로 유연한 엔티티 구성
- 시스템 단위 최적화 가능
- 멀티스레딩 친화적 구조

#### 핵심 디자인 패턴

| 패턴 | 적용 영역 | 설명 |
|------|-----------|------|
| **Game Loop** | Core | 60Hz 고정 타임스텝 |
| **State Machine** | Game States | 메뉴, 항해, 전투 상태 전환 |
| **Object Pooling** | Entities | 선박, UI 요소 재사용 |
| **Observer** | Event Bus | 시스템 간 이벤트 통신 |
| **Singleton** | Managers | GameManager, AudioManager |
| **Factory** | Entity Creation | 선박, 아이템 생성 |
| **Command** | Input | 입력 명령 패턴 |

#### 데이터 흐름 패턴

**비동기 메시지 라우팅**:
```
Producer → Event Bus → Consumer(s)
   ↓           ↓           ↓
 발행자     중앙 허브     구독자(복수)
```

**이점**:
- 시스템 간 느슨한 결합
- 새 기능 추가 시 기존 코드 수정 최소화
- 이벤트 로깅 및 디버깅 용이

---

### 개발 환경

#### IDE 및 도구

| 도구 | 버전 | 용도 |
|------|------|------|
| **Unity Editor** | 2022.3 LTS | 게임 개발 |
| **Visual Studio 2022** | 17.x | C# 코드 편집 |
| **Rider** | 2024.x (대안) | JetBrains IDE |
| **Git** | 2.40+ | 버전 관리 |
| **Git LFS** | 3.x | 대용량 에셋 관리 |

#### 빌드 도구

| 도구 | 용도 |
|------|------|
| **Unity Build Automation** | CI/CD 빌드 |
| **fastlane** | iOS/Android 배포 자동화 |
| **Gradle** | Android 빌드 |
| **Xcode** | iOS 빌드 |

#### 개발 환경 설정

```bash
# Git LFS 설정
git lfs install
git lfs track "*.psd"
git lfs track "*.png"
git lfs track "*.mp3"
git lfs track "*.wav"

# Unity 프로젝트 설정
# ProjectSettings에서:
# - Scripting Backend: IL2CPP
# - Target Architecture: ARM64
# - .NET Framework: .NET Standard 2.1
```

---

### 테스트 전략

#### 테스트 프레임워크

| 프레임워크 | 용도 | 커버리지 목표 |
|------------|------|---------------|
| **Unity Test Framework** | 단위/통합 테스트 | 70%+ |
| **NUnit** | 단위 테스트 | 핵심 로직 |
| **Unity Test Runner** | Play Mode 테스트 | 게임플레이 |

#### 테스트 계층

1. **Unit Tests (단위 테스트)**
   - 개별 컴포넌트 및 시스템 테스트
   - 물리 계산 검증
   - 데이터 모델 검증
   - 커버리지: 80%

2. **Integration Tests (통합 테스트)**
   - 시스템 간 상호작용 테스트
   - 이벤트 버스 통신 검증
   - 저장/로드 시스템 검증
   - 커버리지: 70%

3. **Play Mode Tests (플레이 모드 테스트)**
   - 실제 게임플레이 시나리오
   - 항해 시스템 동작 검증
   - 무역 트랜잭션 검증
   - 커버리지: 60%

4. **Performance Tests (성능 테스트)**
   - 프레임레이트 모니터링
   - 메모리 사용량 추적
   - 배터리 소모 측정

#### 테스트 실행

```bash
# Edit Mode 테스트 실행
Unity -runTests -testPlatform editmode -projectPath . -testResults results.xml

# Play Mode 테스트 실행
Unity -runTests -testPlatform playmode -projectPath . -testResults results.xml

# 특정 테스트 카테고리 실행
Unity -runTests -testPlatform editmode -testCategory "Physics" -projectPath .
```

---

### 빌드 및 배포 파이프라인

#### CI/CD 구성

```
Git Push → Unity Build Automation → Testing → Artifact → Store
    ↓              ↓                   ↓         ↓        ↓
 트리거       빌드 실행            테스트     APK/IPA   배포
```

#### 빌드 프로필

| 프로필 | 대상 | 최적화 | 디버깅 |
|--------|------|--------|--------|
| **Development** | 개발자 | 없음 | 전체 |
| **Staging** | QA | 부분 | 로그만 |
| **Production** | 스토어 | 전체 | 없음 |

#### Android 빌드 설정

```groovy
// build.gradle
android {
    defaultConfig {
        minSdkVersion 26  // Android 8.0
        targetSdkVersion 34  // Android 14
        versionCode 1
        versionName "0.5.2"
    }
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
        }
    }
}
```

#### iOS 빌드 설정

```ruby
# Podfile
platform :ios, '13.0'

target 'UnchartedWaters2' do
  use_frameworks!
  pod 'Firebase/Analytics'
  pod 'Firebase/Crashlytics'
end
```

#### 배포 자동화 (fastlane)

```ruby
# Fastfile
lane :deploy_android do
  gradle(task: "clean assembleRelease")
  upload_to_play_store(track: "internal")
end

lane :deploy_ios do
  build_app(scheme: "UnchartedWaters2")
  upload_to_testflight
end
```

---

### 성능 및 최적화

#### 목표 성능 지표

| 지표 | 목표값 | 측정 방법 |
|------|--------|-----------|
| **프레임레이트** | 60 FPS (최소 30 FPS) | Unity Profiler |
| **로딩 시간** | 5초 이내 (콜드 스타트) | Unity Profiler |
| **메모리 사용** | 500MB 이하 | Memory Profiler |
| **APK 크기** | 150MB 이하 | Build Report |
| **배터리 소모** | 1시간당 20% 이하 | 실기기 테스트 |

#### 최적화 전략

1. **렌더링 최적화**
   - 스프라이트 아틀라스 사용
   - 드로우 콜 배칭
   - LOD 시스템 (거리별 디테일)
   - 오클루전 컬링

2. **물리 최적화**
   - 고정 타임스텝 사용
   - 물리 레이어 분리
   - 비활성 객체 물리 비활성화
   - 간소화된 콜라이더 사용

3. **메모리 최적화**
   - 오브젝트 풀링
   - 에셋 번들 동적 로딩
   - 텍스처 압축 (ASTC/ETC2)
   - 스트리밍 에셋

4. **네트워크 최적화**
   - 데이터 압축
   - 배치 요청
   - 캐싱 전략
   - 델타 동기화

---

### 보안 요구사항

#### 데이터 보안

| 영역 | 방법 | 적용 |
|------|------|------|
| **저장 데이터** | AES-256 암호화 | 로컬 세이브 |
| **통신** | TLS 1.3 | 모든 API 호출 |
| **API 키** | 환경 변수 | 빌드 시 주입 |
| **무결성** | HMAC 검증 | 세이브 데이터 |

#### 치트 방지

- IAP 거래 서버 검증
- 게임 진행 무결성 검사
- 비정상 행동 탐지 로깅
- 중요 로직 서버 사이드 처리

#### 개인정보보호

| 규정 | 적용 내용 |
|------|-----------|
| **GDPR** | 개인정보 수집 동의, 삭제 요청 처리 |
| **CCPA** | 개인정보 판매 거부 옵션 |
| **COPPA** | 13세 미만 사용자 제한 |
| **한국 게임법** | 연령 등급 표시, 결제 한도 |

---

### TRUST 5 원칙 적용

#### 현재 적용 수준: Level 3 (Production Quality)

| 원칙 | 적용 상태 | 세부 내용 |
|------|-----------|-----------|
| **Test First** | 적용 | Unity Test Framework로 TDD |
| **Readable** | 적용 | C# 코딩 규칙, XML 문서화 |
| **Unified** | 적용 | ECS 패턴, 일관된 아키텍처 |
| **Secured** | 적용 | 데이터 암호화, 통신 보안 |
| **Trackable** | 적용 | SPEC 기반 개발, Git 이력 |

#### 코드 품질 도구

| 도구 | 용도 | 적용 단계 |
|------|------|-----------|
| **ReSharper/Rider** | 정적 분석 | 개발 중 |
| **SonarQube** | 코드 품질 | CI |
| **Unity Profiler** | 성능 분석 | 개발/QA |
| **Crashlytics** | 오류 추적 | Production |

---

### 운영 및 모니터링

#### 분석 및 모니터링 스택

| 서비스 | 용도 |
|--------|------|
| **Firebase Analytics** | 사용자 행동 분석 |
| **Firebase Crashlytics** | 충돌 보고 |
| **Firebase Performance** | 성능 모니터링 |
| **Custom Backend** | 게임 메트릭스 |

#### 알림 및 경보

| 이벤트 | 알림 채널 | 임계값 |
|--------|-----------|--------|
| 충돌률 증가 | Slack, Email | 1% 초과 |
| API 오류 | Slack | 5% 초과 |
| 성능 저하 | Dashboard | P95 > 200ms |
| IAP 실패 | Email | 10건/시간 |

#### 인시던트 대응

| 심각도 | 응답 시간 | 해결 목표 |
|--------|-----------|-----------|
| P0 (서비스 불가) | 15분 | 1시간 |
| P1 (주요 기능 장애) | 1시간 | 4시간 |
| P2 (부분 장애) | 4시간 | 24시간 |
| P3 (경미한 버그) | 24시간 | 1주일 |

---

### 기술적 제약 및 고려사항

#### 플랫폼 제약

| 플랫폼 | 최소 요구사항 | 권장 사양 |
|--------|---------------|-----------|
| **iOS** | iOS 13+, iPhone 8+ | iOS 15+, iPhone 12+ |
| **Android** | Android 8.0+, 2GB RAM | Android 11+, 4GB RAM |

#### 개발 제약

- **솔로 개발**: 모든 분야 단독 개발로 인한 시간 제약
- **예산 제한**: 유료 에셋/서비스 사용 제한
- **출시 일정**: 2025년 Q3 목표

#### 기술 부채 관리

- 정기적인 리팩토링 스프린트 (월 1회)
- 기술 부채 백로그 유지
- 코드 리뷰 자체 수행 (체크리스트 활용)
- 문서화 우선 정책

---

### 라이브러리 및 의존성

#### Unity 패키지

| 패키지 | 버전 | 용도 |
|--------|------|------|
| **URP** | 14.0.x | 렌더 파이프라인 |
| **TextMeshPro** | 3.0.x | 텍스트 렌더링 |
| **Addressables** | 1.21.x | 에셋 관리 |
| **Localization** | 1.4.x | 다국어 지원 |
| **Input System** | 1.7.x | 입력 처리 |

#### 외부 플러그인

| 플러그인 | 버전 | 용도 |
|----------|------|------|
| **DOTween** | 1.2.765 | 애니메이션 |
| **UniTask** | 2.5.x | 비동기 처리 |
| **Newtonsoft.Json** | 13.0.x | JSON 처리 |
| **Firebase SDK** | 11.x | 분석/인증 |

#### 버전 관리 정책

- Unity LTS 버전 유지 (년 1회 메이저 업데이트 검토)
- 보안 패치 즉시 적용
- 주요 의존성 분기별 업데이트 검토
- 호환성 테스트 후 업그레이드

---

## 문서 이력

| 버전 | 날짜 | 작성자 | 변경 내용 |
|------|------|--------|-----------|
| 1.0.0 | 2025-01-02 | Project Manager Agent | 초기 문서 작성 |

---

*이 문서는 MoAI-ADK 프로젝트 초기화 과정에서 자동 생성되었습니다.*
