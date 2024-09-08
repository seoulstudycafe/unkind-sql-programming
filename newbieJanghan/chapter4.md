# SQL
> Structured English Query Language 
## 역사
## 특징
## 종류
- SELECT
- DML(Data Manipulation Language): INSERT, UPDATE, DELETE, MERGE
- Transaction Control Statement: COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION
- DDL(Data Definition Language): CREATE, ALTER, DROP, TRUNCATE, COMMENT
- DCL(Data Control Language): GRANT, REVOKE
- Session Control Statement: ALTER SESSION, SET ROLE

## 처리 과정
### 단계
- syntax check: SQL 의 문법 검사
- semantic check: 오브젝트와 권한의 존재 유무를 검사
- shared pool check: shared pool 의 library cache 에 SQL 이 저장되어 있는지 검사
- optimization: SQL 의 쿼리 변환과 최적화를 수행
- row source generation: SQL 엔진에 의해 수행될 로우 소스 생성
### 흐름
- parsing: syntax check -> semantic check -> shared pool check
- hard parse: parsing -> optimization -> rows source generation
- soft parse: parsing -> execution. (shared pool 에서 이미 저장된 cursor 를 바로 수행)

## 수행 과정
> 주요 쿼리들 먼저 확인
### SELECT
- data file 에 바로 들르기 전 buffer cache 에 데이터 블록 있는지 확인하는 과정이 특징

### DML 문
1. undo segment 할당 후 buffer cache 찌르기
2. 없으면 data file 들러서 buffer cache 갱신 후, 해당 block 에 락 설정
3. data block, undo block 변경 사항을 redo log buffer 에 기록: 백그라운드 프로세스 LGWR (Log Writer)는 이를 online redo log에 저장한다.
4. undo block 에 변경 전 데이터를 저장하고 data block 변경: 백그라운드 프로세스 DBWn (Database Writer)는 이를 data file 에 저장한다.
5. 변경 결과를 클라이언트로 전달

### COMMIT 문