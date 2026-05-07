# Salesforce Admin 온보딩 학습 위키

> 대유넥스티어 | Salesforce MCAE Admin | 2026.04.21 ~ 05.06

---

## 이 레포지토리에 대해

대유넥스티어 입사 후 진행한 **Salesforce Administrator 실습 교육** 전 과정을 기록한 학습 포트폴리오입니다.

- **교육 기간**: 2026년 4월 21일 ~ 5월 6일 (총 7일)
- **교육 내용**: Salesforce 프로젝트 라이프사이클 → 오브젝트 설계 → Flow 자동화 → 데이터 마이그레이션
- **실습 환경**: Salesforce Developer Org (Spring '26)

---

## 학습 영역

| 영역 | 주요 내용 |
|------|----------|
| **프로젝트 방법론** | 라이프사이클, 팀 구성(PM/BA/Admin/Dev), Org 환경 4단계, 배포 절차 |
| **데이터 모델링** | Lookup / Master-Detail / Junction Object / Self-Lookup 설계 |
| **Person Account** | B2C 구현, MCE Connector 제약, Record Type 변환 |
| **Flow Builder** | Before/After-Save, Assignment vs Update Records, Bulkification |
| **Flow 심화** | Subflow, Screen Flow, Custom Error, 삭제 트리거 |
| **Order of Execution** | 20단계 전체, Before Flow → Before Trigger → Validation 정정 사항 포함 |
| **Formula vs Flow** | 실시간 계산 vs 시점 고정(박제), 선택 기준 |
| **SOQL** | 서브쿼리(Inner Query), GROUP BY/HAVING, Child Relationship Name(`__r`) |
| **데이터 마이그레이션** | SIR, VLOOKUP 매핑, Batch Size 전략, Insert 순서 |
| **보안 & 검증** | FLS, Validation Rule, Custom Error, ID 하드코딩 금지 원칙 |

---

## 실습 Org 데이터 현황

교육 중 직접 설계하고 마이그레이션한 실습 데이터:

| Object | 레코드 수 | 비고 |
|--------|----------|----- |
| Account | 767건 | PersonAccount (B2C) |
| Product2 | 2,118건 | ProductCode 기준 Unique 키 |
| PricebookEntry | 2,118건 | Standard Pricebook, Product당 1행 |
| Order | 2,120건 | 주문 헤더 |
| OrderItem | 4,346건 | 평균 2건/주문, 41%가 협상 할인가 |

---

## 상세 학습 노트

전체 학습 내용은 **[salesforce_training_wiki.md](./salesforce_training_wiki.md)** 에 정리되어 있습니다.

| 챕터 | 주요 학습 내용 |
|------|---------------|
| [Day 1](./salesforce_training_wiki.md#day1) | 프로젝트 전체 프로세스, 팀 구성, Org 환경, 배포 도구 |
| [Day 2](./salesforce_training_wiki.md#day2) | 오브젝트 설계(Order/Account/Product/OrderItem), PriceBook 구조 |
| [Day 3](./salesforce_training_wiki.md#day3) | Person Account 상세, SIR 7가지 기능, Order of Execution 20단계, Formula vs Flow |
| [Day 4](./salesforce_training_wiki.md#day4) | Record-Triggered Flow Before/After-Save 비교, Assignment vs Update Records |
| [Day 5](./salesforce_training_wiki.md#day5) | 오브젝트 관계 4종, UnitCost Flow 2개 설계, Bulkification |
| [마이그레이션](./salesforce_training_wiki.md#migration) | 마이그레이션 설계 결정사항, Insert 순서, 데이터 품질 현황 |
| [Day 6](./salesforce_training_wiki.md#day6) | Flow 심화 10가지 (TotalPrice, OrderTotal, 삭제 트리거, Compact Layout 등) |
| [Day 7](./salesforce_training_wiki.md#day7am) | Junction Object(BOM), Subflow 리팩토링, SOQL 서브쿼리, Screen Flow 복제 |

---

## 핵심 설계 원칙

교육을 통해 체득한 실무 원칙 요약:

```
Flow 개발
  ✅ Loop 안에 DML(핑크 엘리먼트) 절대 금지 — Governor Limit 150회 초과
  ✅ Before-Save에서는 Assignment만 사용 (DML 소비 없음, ~10배 빠름)
  ✅ After-Save에서는 반드시 Update Records 포함
  ✅ ID 하드코딩 금지 — 환경마다 ID가 다름, DeveloperName으로 비교
  ✅ 공통 로직은 Subflow로 추출하여 재사용
  ✅ Custom App으로 개발 (Standard App 수정 시 ChangeSet 배포 불가)

필드 설계
  ✅ ISBLANK() 사용 권장 (ISNULL보다 Null + 빈 문자열 모두 처리)
  ✅ 시점 고정이 필요한 값(원가, 거래 단가)은 Flow로 박제
  ✅ 항상 최신값이 필요한 단순 계산은 Formula Field 사용
  ✅ Person Account 필드는 Contact 기준으로 생성 (MCE Connector R/W)
```

---

## 사용 도구

| 도구 | 용도 |
|------|------|
| **Salesforce Inspector Reloaded** | SOQL 쿼리, 데이터 이관, 메타데이터 의존 관계 분석 |
| **Flow Builder** | 자동화 로직 전체 구현 |
| **Copado / ChangeSet** | Sandbox → Production 메타데이터 배포 |
| **Draw.io** | As-Is / To-Be 프로세스 구조도 |
| **Figma** | UI 화면 설계 |
| **Excel + VLOOKUP** | 마이그레이션 데이터 가공 및 ID 매핑 |

---

*Salesforce Spring '26 공식 문서 기반 검증 · 대유넥스티어 어드민 교육 2026*
