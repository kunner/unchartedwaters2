---
spec_id: SPEC-DATA-001
version: "1.0.0"
status: "draft"
created: "2025-01-02"
updated: "2025-01-02"
---

# SPEC-DATA-001 구현 계획: 대항해시대 2 원작 데이터 추출

## 개요

대항해시대 2 원작 게임 데이터를 체계적으로 추출하여 JSON 형식으로 구조화하는 구현 계획이다.

---

## 마일스톤

### Primary Goal: 핵심 데이터 추출

#### Phase 1: 항구 데이터 추출 (ports.json)

**목표**: 129개 전체 항구 데이터 완성

**데이터 소스**:
- Uncharted Waters Wiki
- GameFAQs Port Guide
- 기존 커뮤니티 스프레드시트

**추출 항목**:
- 항구명 (ko/en/ja)
- 지역 분류
- 좌표 (맵 기준)
- 경제 지표 (산업/상업)
- 시설 목록
- 소속 국가
- 특산품 목록

**검증 방법**:
- 항구 수 카운트 (129개)
- 필수 필드 존재 여부
- 좌표 범위 유효성
- JSON Schema 검증

**산출물**:
- `src/data/ports.json`
- `src/data/schemas/ports-schema.json`

---

#### Phase 2: 선박 데이터 추출 (ships.json)

**목표**: 30종 이상 선박 데이터 완성

**데이터 소스**:
- Ship List FAQ
- Koei Wiki Ship Database
- 게임 내 선박 스펙 캡처

**추출 항목**:
- 선박명 (ko/en)
- 선박 유형 (탐험/전투/교역)
- 적재량 (capacity)
- 속도 (speed)
- 내구도 (durability)
- 승무원 (min/max)
- 대포 장착 수
- 구매 조건 (rank, shipyard_level)
- 가격

**검증 방법**:
- 선박 종류 수 확인
- 수치 범위 유효성 (원작 기준)
- 밸런스 데이터 교차 검증

**산출물**:
- `src/data/ships.json`
- `src/data/schemas/ships-schema.json`

---

#### Phase 3: 교역품 데이터 추출 (goods.json)

**목표**: 46종 교역품 데이터 완성

**데이터 소스**:
- Trading Guide FAQ
- Price Chart Community Data
- In-game Trade Database

**추출 항목**:
- 교역품명 (ko/en)
- 카테고리 (식품/공예품/사치품 등)
- 기본 가격
- 가격 변동 범위 (min/max)
- 무게
- 특산품 여부
- 원산지 목록

**검증 방법**:
- 교역품 수 확인 (46종)
- 가격 범위 논리성
- 카테고리 분류 일관성

**산출물**:
- `src/data/goods.json`
- `src/data/schemas/goods-schema.json`

---

### Secondary Goal: 확장 데이터 추출

#### Phase 4: 캐릭터 데이터 추출 (characters.json)

**목표**: 주요 캐릭터 데이터 완성

**추출 항목**:
- 캐릭터명 (ko/en/ja)
- 국적
- 직업/역할
- 초기 스탯
- 스토리라인
- 플레이어블 여부
- 관련 이벤트

**데이터 소스**:
- Character Guide FAQ
- Story Walkthrough
- Fan Wiki Character Pages

**산출물**:
- `src/data/characters.json`
- `src/data/schemas/characters-schema.json`

---

#### Phase 5: 지역 데이터 추출 (regions.json)

**목표**: 7개 해역 및 지역 데이터 완성

**추출 항목**:
- 지역명 (ko/en)
- 해역 분류
- 포함 항구 목록
- 주요 교역품
- 위험도
- 항해 조건

**데이터 소스**:
- World Map Guide
- Region Navigation FAQ

**산출물**:
- `src/data/regions.json`
- `src/data/schemas/regions-schema.json`

---

### Final Goal: 검증 및 스키마 완성

#### Phase 6: 검증 및 스키마 생성

**목표**: 전체 데이터 무결성 검증

**작업 내용**:
1. JSON Schema 정의 완료
2. 모든 데이터 파일 스키마 검증
3. 교차 참조 무결성 검증 (예: 항구-지역 관계)
4. 다국어 데이터 완전성 검증
5. 데이터 문서화

**검증 도구**:
- JSON Schema Validator
- Custom validation script
- 교차 참조 검증 스크립트

**산출물**:
- 검증 보고서
- 데이터 사전 (Data Dictionary)
- API 연동 가이드

---

## 기술적 접근

### 디렉토리 구조

```
src/
  data/
    ports.json
    ships.json
    goods.json
    characters.json
    regions.json
    schemas/
      ports-schema.json
      ships-schema.json
      goods-schema.json
      characters-schema.json
      regions-schema.json
    validation/
      validate-all.ts
      cross-reference-check.ts
```

### 데이터 추출 프로세스

1. **수집**: 데이터 소스에서 원시 데이터 수집
2. **정규화**: 일관된 형식으로 변환
3. **검증**: 스키마 및 비즈니스 규칙 검증
4. **병합**: 여러 소스 데이터 통합
5. **최종 검토**: 수동 품질 검사

### 다국어 처리 전략

- 기본 언어: 영어 (en) - 원작 기준
- 필수 지원: 한국어 (ko)
- 선택 지원: 일본어 (ja)

```typescript
interface LocalizedString {
  ko: string;
  en: string;
  ja?: string;
}
```

---

## 리스크 및 대응

### 리스크 1: 데이터 소스 불일치

- **설명**: 여러 소스 간 데이터 차이 발생 가능
- **대응**: 원작 게임을 기준으로 검증, 우선순위 소스 지정

### 리스크 2: 불완전한 데이터

- **설명**: 일부 데이터가 누락되거나 불명확할 수 있음
- **대응**: 필수 필드와 선택 필드 구분, 누락 시 플래그 표시

### 리스크 3: 다국어 번역 품질

- **설명**: 번역 데이터의 정확성 문제
- **대응**: 공식 로컬라이제이션 데이터 우선 사용

---

## 의존성

### 선행 작업

- 없음 (최초 데이터 작업)

### 후행 작업

- SPEC-UI-001: 지도 UI 구현 (항구 좌표 활용)
- SPEC-GAME-001: 게임 로직 구현 (선박/교역품 데이터 활용)

---

## 추적성 태그

```
[TAG:SPEC-DATA-001]
[TAG:PLAN]
[TAG:DATA-EXTRACTION]
[TAG:JSON-SCHEMA]
```
