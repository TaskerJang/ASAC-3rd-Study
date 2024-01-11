 ### 데이터베이스와 DBMS

   # DBMS 정의

데이터에 대한 스키마(테이블) **정의(DDL)**, **저장 및 분석(DML)** 그리고 **관리** 제공하는 응용 프로그램

프로그램(바이너리 파일)을 생성하는 다양한 **언어 및 엔진**이 있는 것처럼 DB도 **다양한 DBMS**가 존재

- **(1) 스키마 + (2) 데이터**
    - **정의, DDL** : **스키마**를 만들고, 변경하고 삭제하고
    - **조작, DML** (저장, 분석) : **데이터**에 대한 CRUD + JOIN, AGG 등을 통한 분석
    - **관리** : 계정, 노드 개수, 메모리, 네트워크, 커넥션 풀 관리 등

## 1) 두 종류의 DBMS: 관계형 데이터베이스(SQL) / 비관계형 데이터베이스(NoSQL)

특징 및 차이점 그리고 각각 언제 사용하는가?

### 1.1) 관계형 데이터베이스 (SQL) ⇒ High Reliability (ACID-compliant, ACID 준수)

> **2차원(행렬)** 구조 데이터 + **관계(Relation)** 기반 데이터 종속성 표현
>
> - **Guaranteed Consistency** (강력한 Consistency)
> - **Data Integrity**

---

- **수직적 확장**에 유리
- **장점**
    - 고신뢰성, 데이터 무결성 (Transaction에 의해)
        - **Guaranteed Consistency**
    - 빠른 분류, 정렬, 탐색 속도
        - 자체 쿼리 최적화 (Query Optimization ← **Query Plan** ← Parsing)
- **단점**
    - 스키마를 수정하기가 어렵다.
    - **수평적 확장**에 한계
        - 수평적 확장을 해도, Guaranteed Consistency 보장을 위해 대기시간이 길 수 있다.
    - **2차원 구조 데이터** - **객체(트리 구조) 데이터** 간 이형 데이터 ⇒ ORM으로 보강
- **Transaction** : Operation의 독립성, 관계형 데이터베이스가 고신뢰성을 보장하는 이유

매번 DB 사용 시 어플리케이션은 트랜잭션을 생성 (개발자에게 있어 트랜잭션은 DB의 전부)

- **패키지 여행 예약 서비스** 예시를 통한 이해

    패키지 여행의 구매는 아래의 요소들이 모두 완벽히 예약 완료되어야 한다.

    - **항공권** (1) 가는 편, (2) 오는 편
    - **기차표** (1) 가는 편, (2) 오는 편
    - **숙소 1박**

    ---

    본 예시에서 트랜젝션은 아래와 같이 상위 1개, 하위 3개로 구성된다.

    - **패키지 여행 구매 Transaction**
        - **왕복 항공권 구매 Transaction**
        - **왕복 기차권 구매 Transaction**
        - **숙소 1박 구매 Transaction**

    > **롤백 케이스 1) 하위 트랜젝션**
    >
    > - **왕복 항공권 구매 Transaction**에서
    >     - (1) 가는 편 예약 완료
    >     - (2) 오는 편 예약 중 에러 발생 시
    >     - **왕복 항공권 구매 Transaction**에 Rollback 발생
    >         - (1) 가는 편 예약 취소

    > **롤백 케이스 2) 상위 트랜젝션**
    >
    > - **왕복 항공권 구매 Transaction,** **왕복 기차권 구매 Transaction** 예약 모두 완료
    > - **숙소 1박 구매 Transaction** 도중 해당 숙소에 방이 존재하지 않아 예약 실패
    > - **숙소 1박 구매 Transaction**에 Rollback 발생
    >     - 상위 **패키지 여행 구매 Transaction**에 Rollback 전이
    > - 하위 **왕복 항공권 구매 Transaction** Rollback 전이 (1) (2) 왕복 예약 취소
    > - 하위 **왕복 기차권 구매 Transaction** Rollback 전이 (1) (2) 왕복 예약 취소
    >
    > = 결과적으로는 성공했었던 모든 예약 Rollback

![Transaction](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/18f5f3a8-acca-4eca-9031-665f5ab970e7)

- **Transaction**가 고신뢰성을 보장하기 위해선 **ACID** 특성을 필수로 가져야한다.

**A**tomicity (원자성), **C**onsistency (일관성, 정합성), **I**solation (격리성), **D**urability

- **A**tomicity: 완벽하게 수행이 되거나 / 완벽하게 수행이 되지 않거나
- **C**onsistency: **Guaranteed Consistency**
    - 데이터 정합성(**Data Integrity**)
- **I**solation: 동시에 다수 트랜잭션이 발생시 모든 트랜잭션은 독립(고립)되어야 한다.
    - **Isolation Level (격리 수준)**을 통해 독립(고립)의 정도를 정의
        - 데이터베이스: 데이터베이스에 기본 설정
        - 어플리케이션: DB 호출 함수에 각각에 대해 세부 설정 가능 (Spring JPA)
- **D**urability: 지속성 (데이터 지속적으로 존재, 로그를 통한 보완)

- **Isolation Level (격리 수준)**: Transaction 독립(고립)의 **정도**에 대한 정의

DB 표준 에러 (SQL Standard Error) 와 그걸 방지하기 위한 방책들

- **1 Level**: **Read Uncommitted** 정책 하에서는 에러 **Dirty Read** 발생
- **2 Level**: **Read Committed** 정책 하에서는 에러 **Non-Repeatable Read** 발생
- **3 Level**: **Repeatable Read** 정책 하에서는 에러 **Phantom Read** 발생
- **4 Level**: **Serializable** 정책에서는 어떠한 에러도 발생하지 않으나, 시간(성능) 하락
    - *“Could not serialize access due to concurrent update”*
# 1.2) 비관계형 데이터베이스 (NoSQL) ⇒ High Scalability, Availability (분산 시스템)

> **트리** 구조 데이터 = **트리 기반** 데이터 종속성 표현
>
> - **Eventual Consistency** (어쨌든 Consistency)
> - **Schema-less**

---

- **수평적 확장**에 유리: *웹 2.0 과 빅데이터 시대를 맞이함에 따라, 무한 확장 가능한 DB 필요*
    - 웹 2.0 JSON, XML 포맷 및 빅데이터 등장으로 복잡한 데이터 형태 저장 필요
        - 불확실성이 높은 데이터, 우선 쌓고보자
    - 방대한 트래픽 및 데이터양의 처리
        - 몇일안에 수테라바이트가 쌓이는 상황에서 수직적 확장 시, 매번 인스턴스 교체 필요
- **장점**
    - **Schema-less**
    - 복잡한 데이터 구조 저장 가능 (예, **트리(객체) 구조 저장 가능**)
- **단점**
    - 데이터 무결성 제약이 강하지 않음
- NoSQL은 **Transaction**을 지원하지 않는다.
- NoSQL은 **ACID**를 준수하지 않고, 대신 **BASE**를 준수한다.

> BASE databases abandon the consistency requirements of the ACID model pretty much completely. One of the basic concepts behind BASE is that **data consistency is the developer's problem and should not be handled by the database**
>
> - **B**asically **A**vailable (기본적인 가용성): 분산 시스템의 특징, 부분 고장 시 나머지가 커버
> - **S**oft state (소프트 상태)
>   - 저장소는 쓰기 일관성이 있을 필요가 없다.
>   - 서로 다른 복제본이 항상 상호 일관성이 있을 필요도 없다.
> - **E**ventual consistency (최종 일관성)
>   - **ACID 에서의 Consistency 보다 훨씬 느슨**
>       - UPDATE, DELETE, INSERT 시 순간적(일시적)으로는 일관성이 깨지는 상태
>           - 분산 시스템 특성 상, 일부에선 최신 상태를 가지나, 나머지 일부에선 아님
>       - 일정시간 후에는 전체 분산 시스템에 일관성이 있는 상태가 되는 성질
- NoSQL 종류
    - **Key-Value DB**: Redis
    - **Document DB** (Key-Value 확장): MongoDB, CouchDB
    - **Column DB**: Cassandra
        - Row-Oriented vs Column-Oriented
        - 데이터 분석을 위해 사용, 빅데이터 쿼리 시 굉장히 빠른 결과 도출
        - **단점**: SELECT * 하면 DB 가 멎는다, 굉장히 느린 Update (배치로 매번 리인덱싱)

![NoSQL](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/d1b90b06-6ae4-4352-889e-a81cde3357f6)

- **CAP Theory**: 분산 시스템은 3개를 모두 보장할 수 없다. 최대 2개 보장 가능

> NoSQL 은 분산 데이터베이스이기에 분산 시스템의 CAP 이론으로 분류할 수 있다.
>
> - CA 데이터베이스는 고가용성, 고일관성인데 이는 NoSQL 이 아닌 SQL 로 분류
>     - 즉, 분산 데이터베이스인 NoSQL 는 CA 데이터베이스로 분류될 수 없다.
- **Consistency 일관성**: UPDATE, INSERT, DELETE 시 모든 노드가 같은 값을 가져야함
- **Availability 가용성**: 분산 시스템 중 일부가 실패해도 나머지는 정상적으로 서비스
- **Partition Tolerance 파티션 허용 오차**: 네트워크 장애에 대한 내결함성(FT)

# 2) 데이터베이스 확장 (Scaling): 대규모 데이터베이스 시스템에서의 **부하 분산** 및 **확장성**을 위해
- **Partitioning**: 하나의 테이블을 여러 데이터로 쪼개어 저장하는것
    - **Vertical Partitioning**: 컬럼(열) 쪼개기

![Vertical Partitioning](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/18c271a8-3647-46ca-a74f-4fa1f9f68cc4)

- **Horizontal Partitioning**: 로우(행) 쪼개기

![Horizontal Partitioning](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/ac2b8619-ff5e-4f69-b6a4-1658d8e71290)

- **Sharding** (Horizontal Partitioning): 로우(행) 쪼개기

![Sharding](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/52f9e60c-b14a-4d9d-9a6e-ff7ac9595c8c)

![Sharding](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/36dd7135-3fbe-4c1a-ad2b-345dd7b400b4)

- **Replication**: 같은 데이터를 여러 분산 노드에 동일하게 저장
    - 쓰기 담당 / 읽기 담당을 나누어, 쓰기 발생 시 읽기 담당에 모두 동기화를 맞춰야한다

# 3) 데이터베이스 동시성 제어 (Concurrency Control)
- **Pessimistic Locking**: “분명히 충돌날거야” 비관주의적
    - 충돌을 예상하고, **매 요청마다 Lock 설정 (성능 하락)**
    - 읽기 / 쓰기 중 **쓰기 비중이 높을때** 사용
        - “쓰기 Lock” 이 “읽기 Lock” 을 기다린다.
        - *Lock 으로 충돌을 미리방지하기 때문에 롤백을 통해 작업한 내용을 날릴일이 적다*
    - **데이터 무결성 보장 수준이 높으나** | **동시성 떨어짐 + 데드락 발생**
- **Optimistic Locking**: “에이, 충돌나지않을거야” 낙관주의적
    - 충돌을 예상하지 않고, **Lock 없이 모든걸 수행 (성능 좋음)**
    - 읽기 / 쓰기 중 **읽기 비중이 높을때** 사용
        - *데이터별로 Version 컬럼 같은것을 통해 Versioning*
        - *OptimisticLockException: 쓰기 시 충돌이 나면 롤백을 통해 작업한 내용을 날려야한다*
    - **동시성 좋으나** | **데이터 무결성 보장 수준이 낮고 + Versioning, Exception 처리에 대한 개발 공수**
- **세부 방식 (방법론)**
    - **Pessimistic Locking**
        - **Lock-based Concurrency Control** (Lock-based protocol)
            - 2PL (2 Phase Locking)
            - 더 많은 방법론들 존재
    - **Optimistic Locking**
        - **Timestamp-based Concurrency Control**
        - **Multiversion Concurrency Control** (MVCC)

![Concurrency Control](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/9356fed6-d80a-4075-b2c1-4a44a90f9e6a)

## SQL 과 ORM

# 0) ERD 설계 (Entity Relationship Diagram) 및 정규화 (Normalization)

> 개체-관계 모델. 테이블간의 관계를 설명해주는 다이어그램. 모델 구조도.
> 
- **Entity** (Attribute 와 분리하지 않고, 모두 Entity 내 합쳐넣는 방식으로 표기)
- **Relationship: PK — FK**
    - **Notation (표기법**, 그 중 **Crow's Foot Notation)**
  
   ![ERD Notation](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/49d64b06-8d35-46e9-8549-e6cfce1fd0ae)

    - **Cardinality (관계 형태)** = Cadinality(최대) and Ordinality(최소)
        - **1:1** → 예, 학생과 성적 : **특성** (**소유**, 한 Entity 의 입장에서)
        - **1:N** → 예, 학생과 반 : **소속** (**귀속**, 한 Entity 의 입장에서)
        - **N:M** → 예, 학생과 과목 : **관계**
- **Normalization 정규화**

    정규화 전 필수 개념 (1) Anomaly (2) Functional Dependency
    
    - **Anomaly 문제 상황**
        - **삭제 문제**: 삭제 시 세상에서 사라져버리는것 (A 수업을 듣던 유일한 학생의 퇴학)
        - **삽입 문제**: 삽입 시 아직 결정되지 않은 값에 NULL 삽입 (새로운 신입생)
        - **수정 문제**: 모두 수정되어야하는 경우 (전화번호 바꾼 학생에 대한 우리 모두의 전화번호부)
    - **Functional Dependency 함수 종속성**
        - 결정자 A → B, C, D … = **종속관계** (일반적인 예시 A 로는 ID = **기본키** = 결정자)
            - 하나의 관계 안에서 여러 종속관계들을 뽑아낼 수 있을것
    - **Normalization 정규화** : 정규화는 **“관계를 분해하는것”**이며 아래 기준으로 수행 ([출처1](https://mangkyu.tistory.com/28), [2](https://mangkyu.tistory.com/110))
        - *예) **“하나의 테이블(관계, Relationship) 내 여러 결정자를 허용할 수 없다.” 는 원칙***

            ![Normalization Principle](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/97594d6e-6a0c-4dba-9b56-d8e58c631394)

            - 아래처럼 쪼개진다.
          
          ![Normalized Tables](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/93a857a7-29b2-4fdb-a5e9-a674458c7d70)

       - # 제 1 정규형: 한 컬럼에 한 값
          
            <img width="633" alt="Untitled (1)" src="https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/601cfd64-1a26-4337-ae0e-faa5cfcb2c8a">


      - # 제 2 정규형: 1 테이블 1 결정자 (완전 함수 종속)

           ![Table Image](https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/14c6485a-7936-4bac-8d72-5d379ea5d684)

           <img width="501" alt="Image1" src="https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/681f90ec-d1b0-4eaf-a3e1-05914b55dea7">

      - # 제 3 정규형: 같은 값(피결정)을 바라보는 결정자들 분리 (이행적 종속 분리)

          <img width="956" alt="Image2" src="https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/f83c885c-bc0a-4fbc-b966-587802e24817">

          <img width="818" alt="Image3" src="https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/15c231d0-6f80-49d9-825a-f5e5d147315d">

      - # BCNF: 모든 결정자가 후보키

          <img width="956" alt="Image4" src="https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/2128af61-2d73-4562-9e5a-120b60a2177b">

          <img width="771" alt="Image5" src="https://github.com/TaskerJang/ASAC-3rd-Study/assets/124780552/9b985a39-cf1d-4881-8b16-df0c3d684b67">

        - Denormalization 반/비정규화: 관심사 및 ORM 활용도 측면에서 고려

      ## 1) SQL (Structured Query Language)

    - 정의(DDL, Data Definition Language): 데이터 정의어 = Table 조작
    - 조작(DML, Data Manipulation Language): 데이터 조작어 = CRUD
    - Functions: DBMS마다 Date, String 등 조작을 위한 유용한 함수 제공
    - DBMS마다 다르기 때문에 MySQL, MSSQL, PostgresQL 등 모두 다른 함수를 가짐
    - Function → 직접 함수를 만들 수도 있고
    - (Stored) Procedure → 직접 프로시져를 만들 수도 있다.

