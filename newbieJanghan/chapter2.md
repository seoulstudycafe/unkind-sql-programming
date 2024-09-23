# 데이터 모델링
> 데이터 모델을 설계하는 일련의 과정

## 데이터 모델
> 현실 세계를 데이터베이스로 구축할 수 있도록 추상화한 것.  
> 데이터베이스 모델이 건축물 유형이라면 데이터 모델은 건축물의 설계도로 비유할 수 있다.  
1. 요구 사항에 맞게 개념 데이터 모델을 구축하고
2. 데이터베이스 모델에 따라 개념 데이터 모델을 논리 데이터 모델로 상세화한 후
3. DBMS에 따라 논리 데이터 모델을 물리 데이터 모델로 전환한다.

## E-R 모델
> Entity-Relationship Model  
> - 여기서 Relationship 은 Entity 간의 관계를 의미한다.   
> - 오히려 Relational Model 의 Relation 은 오히려 Entity 의 개념과 가깝다.

| 개념       | 집합           | 개별              |
|----------|--------------|-----------------|
| 어떤 것     | Entity       | Instance        |
| 어떤 것의 관계 | Relationship | Pairing         |
| 어떤 것의 특징 | Attribute    | Attribute Value |

용어 구분

| E-R 모델    | 관계형 모델    | 오라클 데이터베이스 |
|-----------|-----------|------------|
| entity    | relation  | table      |
| instance  | tuple     | row        |
| attribute | attribute | column     |

### 엔터티
- 개체로 인식할 수 있는 데이터의 집합

#### 기본 식별자
- 엔터티에서 인스턴스를 고유하게 식별할 수 있는 속성
- 단일 식별자
- 복합 식별자는 인스턴스를 고유하게 식별할 수 있는 최초의 속성으로 구성되어야 한다.

### 속성
- 엔터티에서 관리되는 데이터의 최소 단위

#### 도메인
- 속성 값의 범위
- 물리 모델에서는 데이터 타입과 제약 조건

### 관계
- 엔터티 간의 업무적 연관
- 자식 엔터티는 부모 엔터티의 기본 식별자를 상속받는다. 이를 외래 식별자라고 한다.
- 관계는 페어링의 집합이다. 부모 엔터티가 두 개 이상의 자식 엔터티와 관계를 맺은 경우 여러 페어링의 집합을 관계라 칭한다.

#### 카디널리티
- 하나의 부모가 몇 개의 자식과 페어링될 수 있는지를 나타낸다. 1:1, 1:M, M:M 관계가 존재한다.

#### 옵셔널리티
- 페어링 필수 여부를 나타낸다. 필수 Mandatory, 선택 Optional 관계로 나뉜다.

#### 관계 유형
- 외래 시별자가 기본 식별자 속성으로 상속되면 식별 관계, 일반 속성으로 상속되면 비식별 관계이다.

### 기타
#### 서브타입
- 배타 서브타입과 포함 서브타입
#### 연관 엔터티
- 2개 이상의 부모 엔터티와 식별 관계를 맺어 M:M 관계를 해소하는 엔터티

#### 상호 배타, 상호 포함 관계
#### 이력 엔터티
- 점 이력
- 선분 이력

## 정규형 (Normal Form)
정규형은 데이터 이상 (anomaly) 현상을 제거하기 위한 관계형 모델의 설계 방침.

### 정규화
정규형을 위배한 릴레이션을 정규형으로 만드는 과정

#### 1정규형
   - 다중 값을 가지는 속성의 경우 1정규형 위배.
   - 반복 그룹: 단일 값을 저장해도 유사한 속성이 반복되는 경우, 1정규형 위배.

#### 2정규형
부분 종속: 일반 속성이 식별자의 일부 속성에만 종속되는 경우

#### 3정규형
이행 종속: 일반 속성이 다른 일반 속성에 종속되는 것으로 3정규형 위배.

### 반정규화 (Denormalization)
- 성능을 위해 의도적으로 데이터를 중복시키는 것.
- 비정규화는 Denormalization 이라는 같은 영단어에서 번역된 단어. 같은 뜻
- 역정규화는 Denormalization 의 방법 중 하나. 이 둘의 차이를 명확히 이해하기 어렵다.

### 물리 데이터 모델
논리 데이터 몰델은 DBMS 에 따른 물리 데이터 모델로 전환된다. 이는 DDL 문을 통해 데이터베이스 오브젝트로 생성된다.
- 엔터티 : 테이블
- 속성 : 열
- 도메인 : 데이터 타입, NOT NULL 제약 조건, CHECK 제약 조건 (?)
- 기본 식별자 : PK 제약 조건
- 외래 식별자 : FK 제약 조건