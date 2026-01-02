---
spec_id: SPEC-DATA-001
version: "1.0.0"
status: "draft"
created: "2025-01-02"
updated: "2025-01-02"
---

# SPEC-DATA-001 수용 기준: 대항해시대 2 원작 데이터 추출

## 개요

본 문서는 SPEC-DATA-001의 완료 조건과 검증 시나리오를 정의한다.

---

## 수용 기준 시나리오

### Scenario 1: 항구 데이터 완전성

```gherkin
Feature: 항구 데이터 완전성 검증

  Scenario: 전체 항구 수 검증
    Given ports.json 파일이 생성되었을 때
    When 항구 데이터 배열의 길이를 카운트하면
    Then 129개의 항구가 포함되어야 함

  Scenario: 항구 필수 필드 검증
    Given ports.json 파일의 각 항구 데이터에 대해
    When 필수 필드를 검증하면
    Then 다음 필드가 모두 존재해야 함:
      | 필드명      | 타입     | 필수 |
      | id          | string   | Yes  |
      | name.ko     | string   | Yes  |
      | name.en     | string   | Yes  |
      | region      | string   | Yes  |
      | coordinates | object   | Yes  |
      | economy     | object   | Yes  |
      | facilities  | array    | Yes  |
      | allegiance  | string   | Yes  |

  Scenario: 항구 좌표 유효성 검증
    Given ports.json 파일의 각 항구 좌표에 대해
    When 좌표 값을 검증하면
    Then x 값은 0 이상 1000 이하여야 함
    And y 값은 0 이상 1000 이하여야 함
```

---

### Scenario 2: 선박 스펙 유효성

```gherkin
Feature: 선박 데이터 유효성 검증

  Scenario: 선박 필수 필드 검증
    Given ships.json 파일이 생성되었을 때
    When 각 선박 스펙을 검증하면
    Then 다음 필드가 모두 존재해야 함:
      | 필드명          | 타입     | 필수 |
      | id              | string   | Yes  |
      | name.ko         | string   | Yes  |
      | name.en         | string   | Yes  |
      | stats.capacity  | number   | Yes  |
      | stats.speed     | number   | Yes  |
      | stats.crew.min  | number   | Yes  |
      | stats.crew.max  | number   | Yes  |

  Scenario: 선박 수치 범위 검증
    Given ships.json 파일의 각 선박에 대해
    When 수치 데이터를 검증하면
    Then capacity는 10 이상 1000 이하여야 함
    And speed는 1 이상 20 이하여야 함
    And crew.min은 crew.max보다 작거나 같아야 함

  Scenario: 선박 종류 수 검증
    Given ships.json 파일이 생성되었을 때
    When 선박 데이터 배열의 길이를 카운트하면
    Then 최소 30개 이상의 선박이 포함되어야 함
```

---

### Scenario 3: 교역품 데이터 검증

```gherkin
Feature: 교역품 데이터 유효성 검증

  Scenario: 교역품 수 검증
    Given goods.json 파일이 생성되었을 때
    When 교역품 데이터 배열의 길이를 카운트하면
    Then 46개의 교역품이 포함되어야 함

  Scenario: 교역품 가격 논리성 검증
    Given goods.json 파일의 각 교역품에 대해
    When 가격 범위를 검증하면
    Then priceRange.min은 priceRange.max보다 작아야 함
    And basePrice는 priceRange.min과 priceRange.max 사이여야 함

  Scenario: 교역품 필수 필드 검증
    Given goods.json 파일의 각 교역품에 대해
    When 필수 필드를 검증하면
    Then 다음 필드가 모두 존재해야 함:
      | 필드명     | 타입    | 필수 |
      | id         | string  | Yes  |
      | name.ko    | string  | Yes  |
      | name.en    | string  | Yes  |
      | category   | string  | Yes  |
      | basePrice  | number  | Yes  |
      | weight     | number  | Yes  |
```

---

### Scenario 4: 다국어 지원 검증

```gherkin
Feature: 다국어 데이터 지원 검증

  Scenario: 필수 언어 키 검증
    Given 모든 JSON 데이터 파일이 생성되었을 때
    When 각 항목의 name 필드를 확인하면
    Then ko 키가 존재해야 함
    And en 키가 존재해야 함

  Scenario: 다국어 값 비어있지 않음 검증
    Given 모든 JSON 데이터 파일의 name 필드에 대해
    When 값을 검증하면
    Then ko 값은 빈 문자열이 아니어야 함
    And en 값은 빈 문자열이 아니어야 함

  Scenario: 선택적 일본어 키 검증
    Given 다국어 지원이 확장된 경우
    When name 필드에 ja 키가 있으면
    Then ja 값은 빈 문자열이 아니어야 함
```

---

### Scenario 5: JSON Schema 검증

```gherkin
Feature: JSON Schema 유효성 검증

  Scenario: 항구 데이터 스키마 검증
    Given ports-schema.json 스키마 파일이 정의되었을 때
    When ports.json 파일을 스키마로 검증하면
    Then 스키마 검증을 통과해야 함
    And 검증 오류가 0개여야 함

  Scenario: 선박 데이터 스키마 검증
    Given ships-schema.json 스키마 파일이 정의되었을 때
    When ships.json 파일을 스키마로 검증하면
    Then 스키마 검증을 통과해야 함

  Scenario: 교역품 데이터 스키마 검증
    Given goods-schema.json 스키마 파일이 정의되었을 때
    When goods.json 파일을 스키마로 검증하면
    Then 스키마 검증을 통과해야 함

  Scenario: 전체 스키마 검증 자동화
    Given 모든 JSON Schema 파일이 정의되었을 때
    When 검증 스크립트를 실행하면
    Then 모든 데이터 파일이 해당 스키마를 통과해야 함
```

---

### Scenario 6: 데이터 무결성 검증

```gherkin
Feature: 교차 참조 무결성 검증

  Scenario: 항구-지역 참조 무결성
    Given ports.json과 regions.json이 생성되었을 때
    When 각 항구의 region 값을 검증하면
    Then 해당 region이 regions.json에 존재해야 함

  Scenario: 교역품 원산지 참조 무결성
    Given goods.json과 regions.json이 생성되었을 때
    When 각 교역품의 origins 배열을 검증하면
    Then 각 origin이 regions.json에 존재해야 함

  Scenario: ID 고유성 검증
    Given 각 JSON 데이터 파일에 대해
    When 모든 id 필드를 수집하면
    Then 동일 파일 내 중복 id가 없어야 함
```

---

### Scenario 7: 파일 형식 검증

```gherkin
Feature: 파일 형식 및 인코딩 검증

  Scenario: UTF-8 인코딩 검증
    Given 모든 JSON 파일에 대해
    When 파일 인코딩을 확인하면
    Then UTF-8 인코딩이어야 함

  Scenario: 유효한 JSON 문법 검증
    Given 모든 JSON 파일에 대해
    When JSON 파싱을 시도하면
    Then 파싱 오류가 발생하지 않아야 함

  Scenario: 파일 크기 검증
    Given 각 JSON 데이터 파일에 대해
    When 파일 크기를 확인하면
    Then 1MB를 초과하지 않아야 함
```

---

## 품질 게이트

### 필수 통과 조건

| 항목 | 기준 | 검증 방법 |
|------|------|----------|
| 항구 데이터 완전성 | 129개 항구 | 배열 길이 카운트 |
| 선박 데이터 완전성 | 30개 이상 | 배열 길이 카운트 |
| 교역품 데이터 완전성 | 46개 교역품 | 배열 길이 카운트 |
| 다국어 필수 키 | ko, en 존재 | 필드 검증 |
| JSON Schema 검증 | 100% 통과 | Schema Validator |
| ID 고유성 | 중복 없음 | 고유성 검사 |

### 권장 통과 조건

| 항목 | 기준 | 검증 방법 |
|------|------|----------|
| 일본어 지원 | ja 키 포함 | 필드 검증 |
| 이미지 참조 | 이미지 URL 포함 | 필드 존재 검사 |
| 설명 필드 | description 포함 | 필드 존재 검사 |

---

## 완료 정의 (Definition of Done)

### 필수 완료 조건

1. 모든 필수 통과 조건 충족
2. JSON Schema 검증 100% 통과
3. 교차 참조 무결성 검증 통과
4. 다국어 필수 키 (ko, en) 검증 통과
5. 데이터 문서화 완료

### 산출물 체크리스트

- [ ] `src/data/ports.json` 생성 완료
- [ ] `src/data/ships.json` 생성 완료
- [ ] `src/data/goods.json` 생성 완료
- [ ] `src/data/characters.json` 생성 완료
- [ ] `src/data/regions.json` 생성 완료
- [ ] `src/data/schemas/` 디렉토리 내 모든 스키마 파일 생성
- [ ] 검증 스크립트 작성 및 실행 완료
- [ ] 데이터 사전 (Data Dictionary) 문서화 완료

---

## 추적성 태그

```
[TAG:SPEC-DATA-001]
[TAG:ACCEPTANCE]
[TAG:GHERKIN]
[TAG:QUALITY-GATE]
```
