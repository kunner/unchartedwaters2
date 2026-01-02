---
id: SPEC-DATA-001
version: "1.0.0"
status: "draft"
created: "2025-01-02"
updated: "2025-01-02"
author: "MoAI-ADK"
priority: "high"
parent_spec: "SPEC-001"
lifecycle_level: "spec-anchored"
---

# SPEC-DATA-001: 대항해시대 2 원작 데이터 추출

## 목적

대항해시대 2 (Uncharted Waters 2: New Horizons) 원작 게임의 핵심 데이터를 JSON 형식으로 체계적으로 추출하여 웹 기반 리메이크 프로젝트의 데이터 기반을 구축한다.

## 범위

### 추출 대상 데이터

| 데이터 유형 | 예상 수량 | 우선순위 |
|------------|----------|---------|
| 항구 (Ports) | 129개 | High |
| 선박 (Ships) | 30+ 종류 | High |
| 교역품 (Goods) | 46종 | High |
| 캐릭터 (Characters) | 주요 캐릭터 | Medium |
| 지역 (Regions) | 7개 해역 | Medium |

### 데이터 소스

- Fan Wiki 사이트
- GameFAQs 가이드
- Koei Wiki
- 기존 커뮤니티 데이터베이스

---

## 요구사항

### Ubiquitous (시스템 전반 적용)

- **[REQ-U-001]** 시스템은 **항상** 모든 데이터를 UTF-8 인코딩으로 저장해야 한다
- **[REQ-U-002]** 시스템은 **항상** JSON 파일에 대해 유효한 JSON 문법을 유지해야 한다
- **[REQ-U-003]** 시스템은 **항상** 다국어 필드(name)에 ko, en 키를 포함해야 한다
- **[REQ-U-004]** 시스템은 **항상** 각 데이터 항목에 고유 ID를 부여해야 한다

### Event-Driven (데이터 추출 트리거)

- **[REQ-E-001]** **WHEN** 새로운 데이터 소스가 발견되면 **THEN** 기존 데이터와 병합 검증을 수행한다
- **[REQ-E-002]** **WHEN** 데이터 추출이 완료되면 **THEN** JSON Schema 검증을 자동 실행한다
- **[REQ-E-003]** **WHEN** 중복 데이터가 감지되면 **THEN** 경고를 출력하고 수동 검토를 요청한다
- **[REQ-E-004]** **WHEN** 데이터 파일이 수정되면 **THEN** 버전 번호를 증가시킨다

### Unwanted Behavior (에러 처리)

- **[REQ-W-001]** 시스템은 불완전한 데이터를 최종 JSON 파일에 포함**하지 않아야 한다**
- **[REQ-W-002]** 시스템은 검증되지 않은 소스의 데이터를 자동 병합**하지 않아야 한다**
- **[REQ-W-003]** 시스템은 필수 필드가 누락된 항목을 저장**하지 않아야 한다**
- **[REQ-W-004]** 시스템은 원본 데이터를 덮어쓰기**하지 않아야 한다** (백업 필수)

### State-Driven (상태별 동작)

- **[REQ-S-001]** **IF** 항구 데이터에 좌표 정보가 있으면 **THEN** 지도 렌더링용 coordinates 필드를 포함한다
- **[REQ-S-002]** **IF** 선박 데이터에 업그레이드 정보가 있으면 **THEN** upgrades 배열을 포함한다
- **[REQ-S-003]** **IF** 교역품에 특산품 여부가 있으면 **THEN** specialty 플래그를 설정한다
- **[REQ-S-004]** **IF** 캐릭터가 플레이어블이면 **THEN** playable: true를 설정한다

### Optional Feature (선택적 기능)

- **[REQ-O-001]** **가능하면** 일본어(ja) 데이터도 포함한다
- **[REQ-O-002]** **가능하면** 원작 이미지 참조 URL을 포함한다
- **[REQ-O-003]** **가능하면** 역사적 배경 설명을 description 필드에 포함한다
- **[REQ-O-004]** **가능하면** 게임 내 이벤트 연관 정보를 포함한다

---

## 데이터 스키마

### 항구 데이터 (ports.json)

```json
{
  "$schema": "ports-schema.json",
  "version": "1.0.0",
  "data": [
    {
      "id": "port_lisbon",
      "name": {
        "ko": "리스본",
        "en": "Lisbon",
        "ja": "リスボン"
      },
      "region": "iberia",
      "coordinates": {
        "x": 120,
        "y": 340
      },
      "economy": {
        "industry": 85,
        "commerce": 90
      },
      "facilities": ["shipyard", "market", "guild", "palace"],
      "allegiance": "portugal",
      "specialties": ["wine", "olive_oil"]
    }
  ]
}
```

### 선박 데이터 (ships.json)

```json
{
  "$schema": "ships-schema.json",
  "version": "1.0.0",
  "data": [
    {
      "id": "ship_caravel",
      "name": {
        "ko": "카라벨",
        "en": "Caravel"
      },
      "type": "exploration",
      "stats": {
        "capacity": 50,
        "speed": 8,
        "durability": 60,
        "crew": {
          "min": 15,
          "max": 40
        }
      },
      "cannons": {
        "max": 10
      },
      "requirements": {
        "rank": 3,
        "shipyard_level": 2
      },
      "price": 15000
    }
  ]
}
```

### 교역품 데이터 (goods.json)

```json
{
  "$schema": "goods-schema.json",
  "version": "1.0.0",
  "data": [
    {
      "id": "good_spice",
      "name": {
        "ko": "향신료",
        "en": "Spice"
      },
      "category": "luxury",
      "basePrice": 1200,
      "priceRange": {
        "min": 800,
        "max": 2000
      },
      "weight": 5,
      "specialty": true,
      "origins": ["southeast_asia", "india"]
    }
  ]
}
```

---

## 제약사항

### 기술적 제약

- JSON 파일은 UTF-8 인코딩 필수
- 단일 JSON 파일 크기 1MB 이하 권장
- ID 형식: `{type}_{snake_case_name}` (예: `port_lisbon`, `ship_caravel`)

### 데이터 품질 제약

- 모든 수치 데이터는 원작 게임 기준 검증 필요
- 다국어 데이터 중 ko, en은 필수, ja는 선택
- 좌표 데이터는 게임 내 맵 기준 상대 좌표

---

## 연관 SPEC

- **SPEC-001**: Complete Remake (상위 SPEC)
- **SPEC-UI-001**: 지도 UI (항구 좌표 데이터 활용 예정)
- **SPEC-GAME-001**: 게임 로직 (선박/교역품 데이터 활용 예정)

---

## 추적성 태그

```
[TAG:SPEC-DATA-001]
[TAG:DATA-EXTRACTION]
[TAG:JSON-SCHEMA]
[TAG:MULTILINGUAL]
[TAG:PORTS]
[TAG:SHIPS]
[TAG:GOODS]
```
