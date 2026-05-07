# Salesforce 어드민 교육 위키
**대유넥스티어 | 2026.04.21 ~ 05.06 | 강사: 장혜성 외**

---

## 목차
1. [Day 1 (04.21) — Salesforce 프로젝트 전체 프로세스](#day1)
2. [Day 2 (04.22) — 오브젝트 설계 & 필드 구성](#day2)
3. [Day 3 (04.24) — Person Account · SIR · Order of Execution · 마이그레이션](#day3)
4. [Day 4 (04.28) — Flow: Assignment vs Update Records](#day4)
5. [Day 5 (04.29) — 오브젝트 관계 & Flow 자동화](#day5)
6. [Org 데이터 구조 & 마이그레이션 설계 (04.22~24)](#migration)
7. [Day 6 (04.30) — Flow Builder 심화 실습](#day6)
8. [Day 7 오전 (05.06) — Junction Object · Subflow · SOQL 서브쿼리](#day7am)
9. [Day 7 오후 (05.06) — 중복 체크 Flow · Screen Flow · 레코드 복제](#day7pm)

---

<a name="day1"></a>
## Day 1 (04.21) — Salesforce 프로젝트 전체 프로세스

### 프로젝트 라이프사이클

```
영업/제안 → 착수 & 인원 투입 → 요건 수집 → 요건 정리 & 설계
→ Org 구성 → 중간 회의 → 내부 테스트
→ 배포(Deploy) → 시나리오 작성 → UAT → 매뉴얼 작성
→ 데이터 마이그레이션 → 운영 & 유지보수
```

> 모든 단계는 프로젝트 규모·고객사 특성에 따라 유동적. 정답 프로세스 없음.

### 영업 단계 산출물

| 구분 | 설명 | 특징 |
|------|------|------|
| Demo | 고객 요구에 맞는 시연 환경 | 빠른 구성, 상상력 기반 |
| 제안서 | 솔루션 구성 방안 문서화 | 발표 가능한 형태 |
| PoC | 실제 구현 가능성 증명 | Demo보다 깊은 구현 |

- 영업팀에서 간단한 요청만 전달 → **어드민이 직접 리서치 필요**

### 팀 구성

| 역할 | 약어 | 주요 업무 |
|------|------|----------|
| Project Manager | PM | 일정·의사소통 관리 |
| Business Analyst | BA | 요건 분석, 설계 |
| Administrator | Admin | Org 구성, 배포 |
| Developer | Dev | 커스텀 개발 |
| Pre-sales | Ps | 영업 지원 |
| Project Director | PD | 최종 승인 |

### Org 환경 4단계

| 환경 | 데이터 | 용도 |
|------|--------|------|
| Dev | 메타데이터만 | 개발 |
| Ps | 일부 데이터 | UAT |
| Full | 전체 데이터 | 성능 테스트 |
| PD | 전체 데이터 | 운영 (직접 테스트 금지) |

### 요건 수집 파악 항목
- 기존 시스템 / As-Is 프로세스 / Pain point / To-Be / 조직 구조

### 주요 산출물
- As-Is / To-Be 프로세스 구조도
- Object & Field 정의서 (Object API명, Field Label·API명, 타입, 비고)
- Standard vs Custom vs 개발 필요 여부 판단

### 내부 테스트 3단계

| 유형 | 주체 | 설명 |
|------|------|------|
| 단위 | 개인 | 기능 검증 |
| 크로스 | 팀원 | 상호 검증 |
| 통합 | 전체 | 전체 테스트 |

- **어드민 = 테스터 역할 병행**

### 배포 절차
1. Org 등록 → 2. Deployment 생성 → 3. Component 선택 → 4. Validate → 5. Deploy

### 사용 도구

| 도구 | 용도 |
|------|------|
| Drawio | 프로세스 구조도 |
| Figma | UI 설계 |
| Excel | 정의서 |
| PPT | 매뉴얼 |
| Copado | 메타데이터 배포 |
| Inspector Reloaded | 데이터 마이그레이션 |
| ChangeSet | 배포 대안 |

> **핵심 메시지**: "BA/어드민의 역할은 고객의 As-Is를 파악하고 Salesforce로 구현하는 것"

---

<a name="day2"></a>
## Day 2 (04.22) — 오브젝트 설계 & 필드 구성

### 설계 출발점
> "이 필드는 어느 오브젝트에 속하는가?"

플랫 테이블(엑셀) → 관계형 오브젝트 구조로 재설계하는 사고법이 핵심.

### 4개 오브젝트 설계

| 오브젝트 | 역할 | 비고 |
|----------|------|------|
| Order | 주문 헤더 (날짜·담당·배송정보) | Account와 Lookup |
| Account | 고객(거래처) 정보 | B2C면 Person Account |
| Product | 제품 마스터 | PricebookEntry와 연결 |
| OrderItem | 주문 라인 (Junction Object) | Order + Product 참조 |

**Order vs OrderItem 비유**: Order = 영수증 헤더, OrderItem = 품목 라인

### PriceBook 구조
- `Product2.UnitCost__c` → 원가 관리
- `PricebookEntry.UnitPrice` → 정가 (Product당 1개)
- `OrderItem.UnitPrice` → 거래별 실제 단가 (협상가 반영 가능)

### Person Account (B2C)
- UI상 1개 레코드처럼 보이지만 내부에 Account + Contact 동시 생성
- 필드는 Contact 기준으로 생성 권장 (MCE Connector는 Contact에만 R/W)

### Record Type + Page Layout
- 동일 오브젝트에서 다른 레이아웃·Picklist 값 분기
- Record Type → Page Layout 순서로 설정

### Dependent Picklist
- Controlling Field가 먼저 선택되면 Dependent Field 값이 필터링
- 주소 필드 예: Country → State → City 3단계 종속

---

<a name="day3"></a>
## Day 3 (04.24) — Person Account · SIR · Order of Execution · Formula vs Flow · 마이그레이션

### Person Account 상세

| 구분 | Business Account | Person Account |
|------|-----------------|----------------|
| 대상 | B2B | B2C 개인 소비자 |
| 생성 레코드 | Account 1개 | Account + Contact 동시 |
| 스토리지 | 기본 | 연 10~15% 추가 가능 |
| Account Hierarchy | 지원 | **미지원** |
| 비활성화 | 가능 | **불가 (영구 활성화)** |

**활성화 전 체크리스트**
- Sandbox/Trial에서 충분히 테스트 후 운영 적용
- Sharing Settings 사전 설정 (Contact: Controlled by Parent 또는 양쪽 Private)
- AppExchange 앱 호환성 사전 확인
- Account Flow가 모두 트리거됨 → 성능 저하 가능성 확인
- Lead의 Company 필드가 비어 있으면 자동 Person Account 변환

**추가 제약**
- Joined Report 제약 (Order+Email 한 리포트 불가)
- Person Account 간 병합만 가능 (Business Account·Contact와 직접 병합 불가)
- Geocoding 미지원
- Campaign Member 기능 제한

**Record Type 변환**
- UI에서 Business ↔ Person Account 변환 불가
- 방법: CSV (Id, RecordTypeId 두 컬럼만) → Data Loader 또는 SIR Data Import
- 조건: Contact 정확히 1개, Portal User 미연결

**MCE Connector 관련**
- Account: Read Only / Contact: Read & Write
- 따라서 생일·이메일·모바일 등 개인 정보는 **Contact 오브젝트에 필드 생성**

---

### Salesforce Inspector Reloaded (SIR)

Chrome 확장 프로그램 (오픈소스, Thomas Prouvot). 단축키: `Ctrl+Shift+I`

| 기능 | 설명 | 활용 |
|------|------|------|
| Show All Data | 모든 필드/API명/값 한 화면. Production은 빨간 배경 | RecordTypeId 확인·복사 |
| Data Export | SOQL 쿼리 + CSV 내보내기, Query Plan 확인 | Picklist 값 추출 |
| Data Import | Insert/Update/Upsert, Batch Size 조정 | 마이그레이션, RecordType 변환 |
| REST Explorer | 인증 없이 GET/POST/PUT/PATCH/DELETE | RecordTypeId 업데이트 |
| Dependencies Explorer | 메타데이터 의존 관계 분석 | 필드·Flow 삭제 전 영향 파악 |
| Flow Scanner | Best Practice 위반 자동 검사 | 배포 전 품질 검토 |
| Flow Compare | Flow 버전 간 시각적 비교 (Spring '26) | 수정 전후 변경 확인 |

**Batch Size 권장값**

| 상황 | Batch Size |
|------|----------|
| 일반 | 200 (기본) |
| Flow/Apex 많은 Org | 50~100 |
| API Limit·CPU Timeout 오류 | 1~10 |

**Picklist 값 SOQL 추출**
```sql
SELECT DeveloperName, MasterLabel
FROM PicklistValueInfo
WHERE EntityParticle.EntityDefinition.QualifiedApiName = 'Account'
AND EntityParticle.QualifiedApiName = 'Industry'
```

---

### Order of Execution (20단계)

```
── 저장 전 ──────────────────────────────────────
① 레코드 초기화 (DB 로드 또는 신규 초기화)
② 신규 필드값 덮어쓰기
③ 시스템 Validation 1차 (필수, 타입, 길이, FK, Unique)
④ Before Record-Triggered Flow  ← Before Trigger보다 먼저!
⑤ Before Trigger (Apex)
⑥ 시스템 Validation 2차 + Custom Validation Rules
⑦ Duplicate Rules

── DB 저장 ───────────────────────────────────────
⑧ DB 저장 (미커밋)

── 저장 후 ──────────────────────────────────────
⑨ After Trigger (Apex)
⑩ Assignment Rules (Lead, Case Owner 설정)
⑪ Auto-response Rules
⑫ Workflow Rules + Field Update
    └─ Field Update 발생 시 Before/After Trigger 1회 재실행 ⚠️
⑬ Escalation Rules
⑭ After Record-Triggered Flow + Process Builder
    └─ 실행 순서 보장 안 됨
⑮ Entitlement Rules
⑯ Roll-up Summary 계산 → 부모 저장 절차 재실행
⑰ 조부모 Roll-up Summary
⑱ Criteria-based Sharing 재평가

── 최종 확정 ──────────────────────────────────────
⑲ 최종 Commit (DML)

── Post-commit 비동기 ────────────────────────────
⑳ Post-commit Logic (이메일, Async Apex, 비동기 Flow)
```

> ⚠️ **교육에서 정정된 내용**: Validation → Trigger → Flow 순서가 아니라 **Before Flow → Before Trigger → Validation(2차)** 순서.

---

### Formula Field vs Flow

| 구분 | Formula Field | Flow + 일반 필드 |
|------|---------------|----------------|
| 값 갱신 | 항상 실시간 계산 | 저장 시점에 1회 고정 |
| 과거 데이터 보존 | ❌ 참조 필드 변경 시 자동 업데이트 | ✅ 저장 당시 값 유지 |
| 수정 가능 | 읽기 전용 | 일반 필드 (수정 가능) |
| 참조 범위 | Lookup 연결 최대 10단계 | Get Records로 어디서든 |
| 성능 | 빠름 (DML 없음) | DML 발생 |

**시나리오: 제품 원가(UnitCost) 관리**
- ❌ Formula: Product 원가 변경 시 과거 OrderItem 원가도 자동 업데이트 → 이력 왜곡
- ✅ After-Save Flow: 생성 시점에 박제(stamp) → 이후 변경 영향 없음

---

### 마이그레이션 순서 & VLOOKUP

**삽입 순서** (참조되는 오브젝트를 먼저):
```
Account → Contact → Product2 → Pricebook/PricebookEntry → Order → OrderItem
```

- 적재 후 새 ID가 부여되므로 다음 단계에서 **VLOOKUP으로 Name → ID 매핑** 필요
- Name 중복 시: CONCATENATE로 고유값 생성 후 VLOOKUP
- ⚠️ VLOOKUP 전 참조 키 Unique 검증 필수

---

<a name="day4"></a>
## Day 4 (04.28) — Flow: Assignment vs Update Records

### Record-Triggered Flow 두 가지 모드

| 구분 | Before-Save | After-Save |
|------|-------------|------------|
| 실행 시점 | DB 저장 전 (OoE Step 4) | DB 저장 후 (OoE Step 14) |
| 수정 대상 | 트리거 레코드만 | 관련 레코드 포함 모두 |
| DML 소비 | 없음 | 있음 |
| 속도 | ~10배 빠름 | 상대적으로 느림 |
| Scheduled Path | 불가 | 가능 |

### Assignment vs Update Records

| 항목 | Assignment | Update Records |
|------|-----------|----------------|
| 분류 | Logic 요소 | Data 요소 |
| DB 반영 | ❌ 즉시 저장 안 됨 | ✅ 즉시 저장 |
| DML 소비 | 없음 | 있음 (After-Save 한정) |
| 비유 | 메모장에 적기 | Ctrl+S 저장 |

**Before-Save 권장 패턴**: Assignment만 사용 (Best Practice)
```
// Before-Save에서
Assignment: OrderProduct.UnitCost__c = $Record.Product2.UnitCost__c
// Update Records 불필요 → 저장 전 메모리에서 가로채서 변경
```

**After-Save 필수 패턴**:
```
// After-Save에서 Assignment만 쓰면 DB에 아무것도 반영 안 됨
// 반드시 Update Records 추가 필요
Assignment: 값 변경
Update Records: $Record 저장
```

### 의사결정 가이드
- 트리거 레코드 자기 필드만 수정 → **Before-Save** (Assignment로 충분)
- 다른 오브젝트 수정 / 이메일 / 외부 API → **After-Save** (Update Records 필수)

### ISBLANK vs ISNULL

| 함수 | 감지 범위 | 권장 |
|------|----------|----- |
| `ISNULL` | null만 감지 | △ |
| `ISBLANK` | null + 빈 문자열 + 공백 모두 | ✅ |

**연관 오브젝트 필드로 조건 걸기**: `Formula evaluates to true` 사용
```
NOT ISBLANK({!$Record.Product2.UnitCost__c})
```

---

<a name="day5"></a>
## Day 5 (04.29) — 오브젝트 관계 & Flow 자동화

### 오브젝트 관계 4종

| 관계 유형 | 카디널리티 | 핵심 특징 |
|----------|-----------|----------|
| Lookup | 1:1 또는 1:N | 느슨한 연결, 자식 독립 존재 가능, Roll-up Summary 불가 |
| Master-Detail | 1:N | 강한 종속, 부모 삭제 시 자식도 삭제, Roll-up Summary 가능 |
| Self-Lookup | 1:N (자기참조) | 계층 구조를 단일 오브젝트로 표현 (예: 대중소 분류) |
| Junction Object | N:N | 다대다 관계를 위한 중간 오브젝트 |

**Self-Lookup 예시**
- Parent Account: 대분류 / 중분류 레코드 생성 시 대분류 Lookup
- 각각 Record Type을 별도로 만듦

**Junction Object 설계**
- 시험(Salesforce 교재): Master-Detail 권장
- 실무: OwnerId 관리 등의 이유로 Lookup을 쓰기도 함
- M-D로 만든 오브젝트를 Lookup으로 전환은 가능하지만 제약 있음 (숙제 확인)

### UnitCost Flow 설계 (두 개의 Flow)

**Flow 1 — OrderItem 생성 시 (Trigger: OrderItem)**
1. 시작 조건: OrderItem 생성, `NOT ISBLANK({!$Record.Product2.UnitCost__c})`
2. Assignment: `OrderProduct.UnitCost__c = $Record.Product2.UnitCost__c`
3. Before-Save이므로 Get Records 불필요 (연결된 Product 직접 참조)
4. 결과: 주문 생성 시점 원가 영구 박제

**Flow 2 — Product UnitCost 신규 입력 시 (Trigger: Product2)**
1. 시작 조건: Product2.UnitCost__c 변경, **Prior Value가 Null**일 때만
2. Get Records: 해당 Product 참조 OrderItem 중 UnitCost__c IS NULL인 레코드
3. Loop + Update Records: 새 UnitCost 값으로 소급 적용
4. 결과: 원가 등록 전에 생성된 과거 OrderItem 한 번만 소급 적용

> ⚠️ Prior Value가 Null 조건이 없으면 단가 변경 시마다 과거 이력 덮어쓰기됨

### Bulkification "박스 옮기기 모델"

```
❌ 잘못된 패턴 (Loop 안 DML)
Loop 시작 → Assignment → Update Records ← 루프 N번 = DML N회

✅ 올바른 패턴 (Loop 밖 일괄 DML)
Loop 시작 → Assignment (Collection에 누적) → Loop 종료 → Update Records 1회
```

- Loop 안에 핑크색 Element (Get/Update/Create/Delete) 절대 금지
- 루프 1,000번 = DML 1,000번 → Governor Limit(150) 초과

### Flow Trigger Explorer
- 특정 오브젝트에 걸린 모든 Flow를 실행 순서대로 확인
- Spring '22 이후 Run Order 수동 지정 가능

---

<a name="migration"></a>
## Org 데이터 구조 & 마이그레이션 설계 (04.22~24)

### 실습 Org 적재 현황

| Object | 레코드 수 | 비고 |
|--------|----------|----- |
| User | 5명 | 담당 관리자 마스터 |
| Account | ~797건 | PersonAccount |
| Product2 | 2,118건 | ProductCode 기준 unique |
| Pricebook2 | 1건 | Standard Pricebook |
| PricebookEntry | 2,118건 | Product당 1행 |
| Order | 2,120건 | 주문 헤더 |
| OrderItem | 4,346건 | Order당 평균 2건 |

### 데이터 품질 현황
- OrderItem 4,346건 중 1,783건(41%)이 PricebookEntry 정가와 다른 실제 거래가로 저장됨
- 할인율 10%~67%까지 들쭉날쭉 → 거래별 협상가 반영 설계 유효성 확인

### 주요 설계 결정사항

| 항목 | 결정 | 이유 |
|------|------|------|
| 가격 구조 | 원가: Product2.UnitCost__c / 정가: PricebookEntry / 거래가: OrderItem.UnitPrice | 시점 데이터 보존 |
| 중복 제거 | ProductCode 기준 | 이름 같아도 가격 다른 케이스 존재 |
| 데이터 범위 | Master 5,000행 + OrderItem 11,000행. Account 매핑 불가 773건 제외 | Lookup 없으면 조회 불가 |

### 마이그레이션 Insert 순서
```
Product2 → PricebookEntry → Account → Order → OrderItem
```

### 핵심 팁
- VLOOKUP 기준: Id 대신 Unique 필드(ProductCode 등)도 사용 가능
- Batch Size: 기본 200, Flow/Apex 많으면 50~100, 오류 시 1~10
- Inspector Show Field API Names 켜두고 작업
- 각 단계 결과 CSV 반드시 저장 (다음 단계 Id 매핑용)
- ⚠️ 생소한 기능 사용 전 Considerations 공식 문서 먼저 확인

### 미결 확인 사항
- 동일 주문 + 다른 배송지 케이스 → Shipment Object 신설 여부
- 제품명 / 제품코드 생성 규칙
- 담당자 필드 → OwnerId vs 커스텀 User Lookup
- Account RecordTypeId 변경 건

---

<a name="day6"></a>
## Day 6 (04.30) — Flow Builder 심화 실습

### 1. Update Element vs Loop — 언제 무엇을 써야 하나?

| 구분 | Update Records 단독 | Loop + Assignment + Update Records |
|------|--------------------|------------------------------------||
| 용도 | 단순 필드 일괄 업데이트 | 레코드마다 다른 값/조건, 생성·삭제 포함 |
| 필터링 | 조건 지정 가능 | Loop 내 Decision으로 개별 제어 |
| DML 소비 | 1회 | Loop 밖에서 1회 (안에 넣으면 N회) |

**핵심 원칙**
- 업데이트만 할 때 → Update Records Element 하나면 충분
- Loop는 컬렉션을 순회하면서 **다른 작업(생성·삭제)이 뒤따를 때** 사용

```
❌ Loop 안에 핑크색 Element 절대 금지
✅ Get Records → Loop → Assignment(누적) → Loop 종료 → Update Records(1회)
```

---

### 2. Null vs Blank

| 구분 | Null | Blank |
|------|------|-------|
| 의미 | 값 자체가 존재하지 않음 | 빈 문자열 `""` 또는 공백 `" "` |
| Is Null 체크 | True | False |
| ISBLANK() | True | True |

> 실무 팁: `ISBLANK(field)`는 Null과 공백 모두 True. `field = null`은 순수 Null만 True.

---

### 3. 필드 수정 제한 — 3가지 방법

| 방법 | 설정 위치 | 적용 범위 | 선택 기준 |
|------|----------|---------|---------|
| ① 필드 권한 제거 (FLS) | Permission Set / Profile | 화면·API·Inspector 등 모든 곳 | 완전 차단 |
| ② Page Layout Read-Only | Page Layout | 해당 레이아웃 적용 화면만 | 특정 화면만 |
| ③ Validation Rule | Object Manager | 저장 시점 전체 (화면+API) | 조건부 차단 |

> ① Page Layout Read-Only로 설정해도 Inspector나 Flow에서는 수정 가능 → 목적에 따라 선택

---

### 4. Formula Field vs Flow 선택 기준

| 상황 | 선택 | 이유 |
|------|------|------|
| 항상 최신값, 수정 불필요, 단순 계산 | **Formula Field** | 실시간, DML 없음, 빠름 |
| 조건부 계산, 타 오브젝트 값 필요, 이력 보존 | **Flow** | 저장 시점 고정, 유연한 참조 |

> **교육 현장 인사이트**: TotalPrice는 "항상 자동 계산, 수정 불가" 성격 → Formula Field가 더 자연스러움. Flow로 구현하면 수정 방지 로직을 별도 추가해야 함.

---

### 5. Total Price 자동 계산 Flow 설계

**계산 공식**
```
Total Price = UnitPrice × Quantity × (1 - BLANKVALUE(Discount, 0))
```

**트리거 조건** (세 값 중 하나라도 변경 OR Total Price가 NULL):
- `Unit Price IS CHANGED`
- `Quantity IS CHANGED`
- `Discount IS CHANGED`
- `Total Price IS NULL` (과거 데이터 처리)

> ⚠️ IS CHANGED 사용: 값이 같아도 저장 버튼 누르면 불필요한 재계산 발생 가능 → IS CHANGED 연산자로 "이전 값 ≠ 현재 값"일 때만 실행

**BLANKVALUE 활용**
```
BLANKVALUE({!$Record.Discount__c}, 0)
```
- Discount가 Null이든 공백이든 0으로 대체
- IF 조건 없이 한 줄로 처리 → 실무 권장

**테스트 필수 케이스**

| 케이스 | 기대 결과 |
|--------|----------|
| Unit Price만 변경 | Total Price 재계산 |
| Quantity만 변경 | Total Price 재계산 |
| Discount 0 → 3 변경 | Total Price 재계산 |
| Discount 3 → 3 (변경 없음) | 재계산 안 함 |
| Total Price 비어있는 기존 레코드 | 저장 시 자동 계산 |
| Discount 없음 (Null) | 0으로 처리하여 정상 계산 |

---

### 6. Before-Save vs After-Save 실행 순서 (요약)

```
① 유효성 검사(시스템) → ② Before-Save Flow → ③ Before Trigger(Apex)
→ ④ Validation Rule 재실행 → ⑤ DB 저장
→ ⑥ After Trigger(Apex) → ⑦ After-Save Flow → ⑧ DB Commit
```

**같은 오브젝트에 Flow가 여러 개일 때**
- 기본: Flow 생성 날짜 순
- Flow A 결과가 Flow B에 필요하면 → Setup에서 **Run Order** 수동 지정

---

### 7. Order Total 자동 집계 — 부모 레코드 업데이트 패턴

**문제**: OrderItem(자식)의 Total Price 변경 시 Order(부모)의 Total Amount 자동 갱신

**Flow 설계**
```
트리거: OrderItem Total Price 변경
→ Get Records (같은 Order의 형제 OrderItem 전체)
→ Loop 시작
→ Assignment: totalAmount = totalAmount + 현재아이템.Total_Price__c (Add 연산자)
→ Loop 종료
→ Update Records: Order.Total_Amount__c = totalAmount
```

**핵심 구현 포인트**

| 포인트 | 설명 |
|--------|------|
| 변수 타입 | Currency 타입, Collection 체크 **해제** (단수 변수) |
| Default 값 | 반드시 **0**으로 설정 |
| Collection 변수 위험 | Add하면 값이 나열(String concat)됨 → 숫자 합산에 단수 변수 사용 |

**냉장고 비유**
- 냉장고 = DB / 꺼내기 = Get Records / 하나씩 확인 = Loop / 라벨 붙이기 = Assignment / 다시 넣기 = Update Records

**Update Records 타입 선택**

| 타입 | 사용 시점 |
|------|----------|
| Use the IDs... | Collection 변수(여러 레코드) 한 번에 업데이트 |
| Specify conditions | 관련 오브젝트를 조건으로 찾아 업데이트 |
| Update triggering record | After-Save에서 트리거 레코드 자체 업데이트 |

---

### 8. Compact Layout과 Flow 디버그

**Compact Layout이란?**
- 레코드 상단 하이라이트 패널에 표시되는 필드 목록
- Flow Builder 디버그 화면에서 레코드 검색 시 이 필드들이 표시됨

**Order Product(OrderItem) Compact Layout 미설정 시**
- Flow 디버그에서 OrderItem 레코드가 보이지 않거나 필드가 표시 안 됨
- 버그 아님 → Compact Layout 미설정이 원인

**해결 방법**
1. Setup → Object Manager → Order Product → Compact Layouts → New
2. 필드 추가: Order Product Name, Unit Price, Quantity, Total Price 등
3. Compact Layout Assignment → Primary 설정 (저장만 하면 적용 안 됨!)

---

### 9. 여러 Flow 합치기 — 언제 합치고 언제 나누나?

**Spring '22 이후** Flow Trigger Order 도입으로 "오브젝트당 1개" 절대 원칙이 유연화됨

| 상황 | 권장 방식 | 이유 |
|------|----------|----- |
| 로직 간단, 연관성 높음 | 합치기 (1개) | 관리 포인트 감소 |
| 로직 복잡, 독립적 | 나누기 | 유지보수 용이 |
| Flow A 결과가 Flow B 입력 | 합치기 | 실행 순서 보장 |

**합칠 때 생성/업데이트 분기 방법**
```
Prior Value of {Record.Id} is Null → 레코드 생성
Prior Value of {Record.Id} is NOT Null → 레코드 업데이트
```

> 가독성 원칙: Decision Element로 분기 조건을 명시적으로 표현. Formula 안에 조건 숨기면 로직 파악 어려움.

---

### 10. 삭제(Delete) 트리거 Flow 설계

**핵심**: 삭제 전(Before-Delete)에 계산해야 함. 삭제 후에는 레코드가 없어 계산 불가.

```
Before-Delete 트리거
→ Get Records (같은 Order의 형제 Items, 삭제 대상 제외)
   조건 1: Order__c = $Record.Order__c
   조건 2: Id ≠ $Record.Id  ← 이 조건 필수!
→ Loop + Assignment 합계 계산
→ Update Records: Order.Total_Amount
→ 실제 삭제 실행
```

**마지막 OrderItem 삭제 시**
- Get Records 결과 비어있음 → 집계 변수 초기값 0 그대로 → Order.Total_Amount = 0

**과거 데이터 마이그레이션 주의**
- Order Status가 Activated면 OrderItem 수정 차단될 수 있음
- 해결: Draft 상태만 선택 또는 관련 Validation Rule/Flow 임시 비활성화

**M-D vs Lookup과 Roll-up Summary**
- Master-Detail → Roll-up Summary Field로 자동 집계 (위 Flow 불필요)
- Lookup → Roll-up Summary 불가 → Flow로 직접 구현

---

### 11. Custom App 배포 주의사항

> ⚠️ Standard App(Sales, Service 등)을 직접 수정하면 Change Set에 포함되지 않아 배포 불가!  
> 반드시 **Custom App을 만들어서** 개발할 것.

**Custom App 만들기**
1. Setup → App Manager → New Lightning App
2. App 이름, 로고 설정
3. 필요한 Object Tab 추가
4. 사용자 프로필 할당

**Sandbox 환경 종류**

| 환경 | 용도 |
|------|------|
| Developer Sandbox | 개발·테스트 |
| Partial Sandbox | 일부 실제 데이터로 테스트 |
| Full Sandbox | 실제 데이터 전체 복사, 성능 테스트 |
| Production | 실제 운영 |

---

<a name="day7am"></a>
## Day 7 오전 (05.06) — Junction Object · Subflow · SOQL 서브쿼리

### 배경: Process Builder / Workflow Rules 지원 종료

2025년 12월 31일부로 Workflow Rules와 Process Builder 공식 지원 종료.

| 도구 | 상태 | 대응 |
|------|------|------|
| Workflow Rules | ⚠️ 지원 종료 (실행은 유지) | Flow Builder로 마이그레이션 |
| Process Builder | ⚠️ 지원 종료 (실행은 유지) | Flow Builder로 마이그레이션 |
| Flow Builder | ✅ 유일한 공식 지원 도구 | 신규 자동화는 모두 여기서 |

---

### Order Item 합산 Flow → Subflow 리팩토링

**왜 Flow가 2개였나?**
- Flow ①: OrderItem 생성/업데이트 시 → 전체 형제 합산 → Order.Total
- Flow ②: OrderItem 삭제 시 → 나(삭제 대상) 빼고 나머지 합산 → Order.Total

**왜 합칠 수 없나?**
- Flow 트리거는 라디오 버튼 — 딱 하나만 선택 가능
- Flow①은 생성/업데이트, Flow②는 삭제 → 공통 트리거 없음

**해결책: Subflow**
```
Main Flow A (생성/업데이트 트리거) → Order ID 전달 → Subflow 호출
Main Flow B (삭제 트리거) → Order ID + 삭제 대상 ID 전달 → Subflow 호출
Subflow (공통 로직): Get Records → Loop → 합산 → Order 업데이트
```

총 3개의 Flow (Main 2 + Subflow 1)

---

### Subflow (Autolaunched Flow) 개념

| 구분 | 메인 Flow | 서브 Flow |
|------|----------|-----------|
| 트리거 | 있음 (Record-triggered 등) | **없음** (No Trigger) |
| 역할 | 트리거 + 서브플로우 호출 | 공통 로직 처리 |
| 파라미터 | Available for **Output** | Available for **Input** |

**생성 방법**
1. New Flow → Autolaunched Flow (No Trigger) 선택
2. Resources에서 Variable 추가 → Available for Input/Output 체크
3. 공통 로직 구성: Get Records → Loop → Assignment → Update Records
4. **반드시 활성화(Activate)** → 활성화 안 하면 메인 Flow에서 목록에 안 보임

**서브플로우에서 Update Records 제약**
- Autolaunched Flow는 트리거 레코드 개념 없음
- "트리거 레코드 업데이트", "트리거 관련 레코드 업데이트" 옵션 없음
- → 반드시 **Get Records로 명시적으로 레코드를 조회**한 후 업데이트

---

### Flow Parameter — 메인↔서브 데이터 전달

**택배 비유**
- 메인 Flow = 발송인 (Available for Output 체크, 값 담아서 내보냄)
- 서브플로우 = 수신인 (Available for Input 체크, 받을 준비된 빈 박스)

**파라미터 설정**

| Flow 위치 | 변수 예시 | 체크 옵션 |
|----------|---------|-----------|
| 메인 Flow | varOrderId | ✅ Available for output |
| 서브플로우 | varOrderId | ✅ Available for input |
| 메인 Flow (삭제 시) | varExcludeId | ✅ Available for output |
| 서브플로우 (삭제 시) | varExcludeId | ✅ Available for input |

**조립**: 메인 Flow 캔버스 → Subflow 엘리먼트 추가 → 활성화된 서브플로우 선택 → Include 체크 후 파라미터 매핑

**삭제 Flow의 Get Records 조건**
```
// 조건 1: 같은 부모(Order) 소속인 형제
Order_Id__c = {!varOrderId}

// 조건 2: 나 자신 제외 (삭제 케이스만)
Id ≠ {!varExcludeId}
```

**생성/업데이트 시 varExcludeId가 없으면?**
- Include 체크 안 하면 null이 넘어옴
- `AND 조건에서 null ≠ ID는 항상 true` → 나 자신도 포함 (정상 동작)

---

### Junction Object (BOM) 실습

**비즈니스 시나리오**: 제조업의 BOM (Bill of Materials, 자재 명세서)
- 제품 A = 부품 a × 2개 + 부품 b × 5개
- 제품 : 부품 = **N:M 관계** → Junction Object 필요

**오브젝트 구조**
```
Product (제품) ← M-D ← BOM (Junction Object) → M-D → Part (부품)
```

**Record Type으로 제품과 부품을 같은 오브젝트에서 관리**
- 제품과 부품은 구조가 거의 동일 (이름, 코드, 가격 등)
- 별도 오브젝트 대신 같은 Product 오브젝트에서 Record Type으로 구분

| Record Type | 의미 | BOM에서의 역할 |
|-------------|------|-----------------|
| Product | 완성 제품 | BOM.Product__c 필드에 연결 |
| Part | 부품/원자재 | BOM.Part__c 필드에 연결 |

**BOM 필드 구성**

| 필드 | 타입 | 설명 |
|------|------|------|
| Product__c | Master-Detail → Product (Record Type: Product) | 어떤 제품에 들어가는지 |
| Part__c | Master-Detail → Product (Record Type: Part) | 어떤 부품인지 |
| Quantity__c | Number | 몇 개 필요한지 |

> ⚠️ 같은 Object(Product)를 가리키는 두 개의 Master-Detail → Salesforce 허용 여부 실습에서 검증 필요 (일반적으로 Object당 최대 2개 M-D 가능)

---

### SOQL 서브쿼리

**방법 1: 서브쿼리 (Inner Query)**
```sql
SELECT Id, Name, (
    SELECT Id, Part__c, Quantity__c
    FROM BOMs__r  -- __r: Child Relationship Name
)
FROM Product__c
WHERE RecordType.DeveloperName = 'Product'
```

**`__r` 접미사**: 커스텀 오브젝트 자식 관계 참조 시 Object API Name 대신 `Child Relationship Name + __r` 사용

**방법 2: GROUP BY + HAVING COUNT**
```sql
SELECT Product__c, COUNT(Id) bomCount
FROM BOM__c
WHERE Quantity__c >= 1
GROUP BY Product__c
HAVING COUNT(Id) >= 1
```

**두 방법 비교**

| 구분 | 방법 1: 서브쿼리 | 방법 2: GROUP BY |
|------|----------------|------------------|
| 시작점 | 부모(Product)에서 | 자식(BOM)에서 |
| 결과 | Product + BOM 목록 함께 | BOM 있는 Product ID만 |
| Flow 활용 | Get Records 2번 | Get Records 1번 |

---

<a name="day7pm"></a>
## Day 7 오후 (05.06) — 중복 체크 Flow · Screen Flow · 레코드 복제

### 1. Before-Save Flow + Custom Error — 중복 체크 기본 구조

**왜 Before-Save인가?**
- Before-Save: 저장 전 실행 → Custom Error로 저장 자체 차단 가능
- After-Save: 저장 후 실행 → 저장 차단 불가

**Salesforce 표준 Duplicate Rule 한계**
- Account, Contact, Lead 등 일부 오브젝트만 지원
- **Product2는 미지원** → Flow로 직접 구현

**접근 순서**: ① 표준 Duplicate Rule 가능 여부 확인 → ② 불가 시 Flow → ③ Flow도 복잡하면 Apex

**Flow 기본 구조**
```
Product 생성 또는 업데이트
→ 진입 조건: ProductCode is not null AND ProductCode is changed
→ Get Records: 같은 코드 + 나 자신 제외 (1개만)
→ Decision: 중복 있음?
   YES → Custom Error 발생 (저장 차단)
   NO → 정상 저장
```

**Custom Error 엘리먼트 (Winter '24 출시)**
- Before-Save / After-Save 모두 사용 가능
- 에러 발생 시 트랜잭션 자동 롤백
- 에러 표시 위치: 페이지 상단(Page Level) 또는 특정 필드 아래(Field Level)

> ⚠️ 에러 메시지에 URL 링크 넣으면 URL 문자열이 그대로 표시될 수 있음 → 텍스트로만 구성 권장

---

### 2. 중복 체크 Flow 상세 로직

**진입 조건 트레이드오프**

| 조건 | 동작 | 장단점 |
|------|------|--------|
| `is changed = true` | 코드 변경될 때만 실행 | ✅ 효율적 / ⚠️ 기존 데이터 중복 체크 안 됨 |
| `is not null` | 코드 있을 때마다 실행 | ✅ 모든 업데이트 체크 / ⚠️ 불필요한 실행 많음 |

> 실무 권장: 신규 프로젝트 → `is changed = true` / 기존 데이터 있는 경우 → `is not null`

**Get Records 조건**
```
// 조건 1: 같은 ProductCode
ProductCode = {!$Record.ProductCode}

// 조건 2: 나 자신 제외 ← 이 조건 빠뜨리면 항상 자기 자신이 중복으로 잡혀서 저장 불가!
Id ≠ {!$Record.Id}

// 가져오는 개수: 1개만 (중복 하나만 있어도 차단)
```

**단건 vs 다건 Get Records**

| 옵션 | 결과 없을 때 | 주의사항 |
|------|------------|----------|
| Only First Record | 뒤 엘리먼트 자동 스킵 | Decision 없어도 되는 경우 있음 |
| All Records | 빈 컬렉션 반환 (null 아님) | 반드시 Loop 또는 Decision으로 존재 여부 체크 |

---

### 3. Hard-code 금지 원칙 — Record Type ID

> 🚨 **절대 원칙**: ID 값을 직접 코드에 넣지 말 것!

- Salesforce는 Sandbox/Dev/Production이 각각 별도 Org
- 같은 Record Type이라도 **Org마다 ID가 다름**
- ID 하드코딩 → 배포 시 반드시 오류

**환경별 ID 비교**

| 환경 | Record Type ID | DeveloperName |
|------|---------------|---------------|
| 개발 Sandbox | 012AB000000111AAA | Product (동일) |
| QA Sandbox | 012CD000000222BBB | Product (동일) |
| Production | 012EF000000333CCC | Product (동일) |

**올바른 방법**
```
❌ 잘못된 방법
RecordTypeId = "012xx000000ABCDEF"  -- 절대 사용 금지!

✅ 방법 1: DeveloperName으로 비교 (Flow Get Records 조건)
RecordType.DeveloperName = "Product"

✅ 방법 2: Validation Rule에서
$RecordType.DeveloperName = "Product"

✅ 방법 3: 트리거 레코드 RecordTypeId 필드 vs 조회한 레코드 RecordTypeId 필드끼리 비교
```

> 핵심 원리: "필드(껍데기)"끼리 비교. 필드 안의 값이 환경마다 달라도 필드 참조 경로가 같으면 어디서나 동일하게 동작.

---

### 4. 필드 필수화(Required) 3가지 방법

**Page Layout 표시 기호**

| 표시 | 의미 |
|------|------|
| ⬤ (동그란 점) | Always on Layout — 화면에서 절대 제거 불가 |
| ✱ (빨간 별표) | Required — 필수 입력 |
| ⬤ + ✱ | 항상 있어야 하고, 반드시 입력도 해야 함 |

**3가지 방법 비교**

| 방법 | 설정 위치 | Flow/API 적용? | 조건 가능? | 추천 상황 |
|------|----------|---------------|-----------|----------|
| ① 필드 레벨 Required | Object Manager → Field | ✅ 항상 | ❌ | 어디서나 무조건 필수 |
| ② Page Layout Required | Page Layout Editor | ❌ UI에서만 | ❌ | 사용자 UI에서만 필수 |
| ③ Validation Rule | Object Manager | ✅ 모든 경로 | ✅ 복잡한 조건 | 조건부 필수 |

> ⚠️ 필드 레벨 Required 설정 시 Flow, Apex, API, 데이터 마이그레이션 등 모든 경로에 적용됨 → 기존 로직 오류 가능성 확인 필수

**Validation Rule 조건부 필수화 예시**
```
// 활성화된 제품일 때만 ProductCode 필수
AND(
  IsActive = true,
  ISBLANK(ProductCode)
)

// 특정 프로필 사용자일 때만 필수 (Profile Name은 환경 불변)
AND(
  $Profile.Name = "Sales User",
  ISBLANK(ProductCode)
)
```

---

### 5. Screen Flow로 레코드 복제 (BOM 포함 Clone)

**비즈니스 요구사항**: 아이폰 15 빨간색 → 아이폰 15 분홍색 복제 (BOM 동일)

**표준 Clone 버튼 한계**
- 레코드 자체만 복제, Related List(BOM)는 복제 안 됨

**전체 Flow 구조**
```
① 변수 생성: recordId (Text, Available for Input)
   ⚠️ API Name은 반드시 recordId (대소문자 일치 필수)

② Get Records: Product.Id = {!recordId}, 단건, 모든 필드

③ Screen 엘리먼트: 기존 값 Default로 채움
   - 제품명, 제품코드: 수정 가능
   - 나머지: Read-only (Disabled=true)

④ Create Records: 새 제품 생성 (ID는 null로 비워서 새 레코드 처리)

⑤ Get Records: 기존 제품의 BOM 목록 (BOM.Product__c = {!recordId}, 다건)

⑥ Loop: 각 BOM의 Product 필드를 새 제품 ID로 교체 + Collection에 추가
   ⚠️ Create Records는 루프 밖에서!

⑦ Create Records: BOM Collection 일괄 생성 (1회 DML)

⑧ Screen: 성공 메시지 + 새 레코드 링크
```

---

### 6. 복제 로직 — ID 비워서 새 레코드 만드는 패턴

```
// Get Records로 기존 레코드 가져옴 (ID 있는 상태)
// 바꿀 필드만 Assignment로 업데이트
Assignment: varProduct.Name = {!screenName}
Assignment: varProduct.ProductCode = {!screenCode}

// ★ 핵심: ID를 null로 비우기
Assignment: varProduct.Id = null  // ID 없으면 INSERT로 처리

// Create Records에 이 변수 사용 → 새 레코드 생성
```

**왜 ID를 비워야 하나?**
- Salesforce: 레코드 변수에 ID 있음 → UPDATE / ID 없음(null) → INSERT
- Get Records로 가져오면 ID 있는 상태 → 그대로 사용하면 기존 레코드 덮어씌움

**BOM 복제 시 필드 처리**

| 필드 | 처리 방법 | 이유 |
|------|----------|----- |
| Id | null로 비우기 | 새 BOM 레코드로 생성 |
| Product__c | 새 제품 ID로 교체 | 복제된 새 제품과 연결 |
| Part__c | 그대로 유지 | 같은 부품 사용 |
| Quantity__c | 그대로 유지 | 같은 수량 |

**제품명 중복 방지 Formula**
```
IF(
  화면에서_입력한_제품명 = 기존_제품명,
  기존_제품명 & "_" & 화면에서_입력한_코드,
  화면에서_입력한_제품명
)
```

---

### 7. recordId 파라미터 설정

> ⚠️ API Name은 반드시 `recordId` (r 소문자, I 대문자) — `RecordId`나 `recordID`는 동작 안 함

**Data Type 선택 기준**

| Data Type | 버튼(Quick Action) | Lightning Page 임베드 |
|-----------|-------------------|-----------------------|
| Text | ✅ 작동 | ❌ 임베드 시 미작동 |
| Record | ❌ 버튼 미작동 | ✅ 작동 |

**Quick Action으로 버튼 연결**
1. Object Manager → Product → Buttons, Links, and Actions → New Action
2. Action Type: Flow 선택 → Screen Flow 선택
3. Lightning Record Page 편집 → 버튼 추가

**Pause 버튼 숨기기 권장**
- Pause 누르면 Flow Interview가 저장됨 → 재개 안 하고 새로 실행하는 경우 많음 → 불필요한 데이터 쌓임
- 짧은 작업용 Flow에서는 숨기는 것 좋음

---

### 8. Governor Limits

Salesforce 멀티테넌트 환경 보호를 위한 강제 한도.

| 제한 항목 | 동기(Sync) 한도 | Flow에서 소비 |
|----------|--------------|--------------|
| SOQL 쿼리 횟수 | 100회 | Get Records |
| DML 문 횟수 | **150회** | Create/Update/Delete Records |
| SOQL 조회 총 레코드 수 | 50,000건 | Get Records 결과 합산 |
| DML 처리 총 레코드 수 | 10,000건 | Create/Update 합산 |
| CPU 시간 | 10,000ms | 모든 로직 실행 시간 |

**루프 안 DML → 반드시 실패**
- BOM 160개 → Loop 안에 Create Records → DML 160회 → 150회 한도 초과 → Flow 강제 종료

**트랜잭션 공유 주의**
- Flow가 Apex와 같은 트랜잭션에서 실행되면 한도를 공유
- Apex가 SOQL 90회 소비했으면 Flow는 10회밖에 안 남음

---

## 주요 숙제 목록

| 항목 | 상태 |
|------|------|
| OrderItem TotalPrice 계산 Flow (생성·수정 시, Before-Save) | — |
| Order TotalAmount 계산 Flow (생성·수정·삭제 시, Subflow 구조) | — |
| Product 중복 체크 Flow (Before-Save + Custom Error) | — |
| Screen Flow (BOM 생성) | — |
| M-D → Lookup 전환 가능 여부 조사 | — |
| Page Layout vs Lightning Page 차이 정리 | — |
| Parts를 Product 오브젝트의 Record Type으로 재구성 | — |
| Cost 오브젝트 별도 구성 방식 조사 | — |

---

## 핵심 설계 원칙 종합

### Formula vs Flow 선택 기준

| 상황 | 선택 |
|------|------|
| 항상 최신값, 수정 불필요, 단순 계산 | Formula Field |
| 시점 고정 필요 (원가, 주문 시점 단가 등) | Flow (Before-Save 박제) |
| 다른 오브젝트 값 필요, 이력 보존 | Flow (After-Save) |

### Standard vs Custom 구분 기준

| 상황 | 방법 |
|------|------|
| M-D 관계 합계 집계 | Roll-up Summary (Standard) |
| Lookup 관계 합계 집계 | Flow 직접 구현 |
| Discount 반영 TotalPrice | Custom Field + Flow |
| 원가 시점 고정 | Flow (Formula 불가) |
| 중복 체크 (Product) | Flow (Duplicate Rule 미지원) |
| 중복 체크 (Account 등) | Duplicate Rule 먼저 확인 |

### Flow 개발 체크리스트

- [ ] ID 하드코딩 금지 (DeveloperName으로 비교)
- [ ] Loop 안에 핑크색 Element 없음
- [ ] Before-Save → Assignment 사용 (Update Records 지양)
- [ ] After-Save → Update Records 반드시 포함
- [ ] Collection 변수 타입 확인 (숫자 합산은 단수 Currency 변수)
- [ ] ISBLANK 사용 (ISNULL보다 안전)
- [ ] Label과 Description 명확하게 작성
- [ ] Flow Trigger Explorer로 동일 오브젝트 Flow 실행 순서 확인
- [ ] 서브플로우 반드시 활성화 후 메인에서 호출
- [ ] Compact Layout 설정 (Flow 디버그 필수)
- [ ] Custom App으로 개발 (Standard App 직접 수정 금지)

---

*대유넥스티어 어드민 교육 | 2026.04.21 ~ 05.06 | Salesforce 공식 문서(Spring '26) 기반 검증 완료*
