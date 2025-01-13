# WHERE 절
## 비교 조건
### 새롭게 알게 된 사실
- `<>` 라는 불일치 조건 연산자도 있다.

## 논리 조건
### `UNKNOWN`
평가 되기 이전이라 무엇인지 모른다는 것.
```oracle 
TRUE AND UNKNOWN -- UNKNOWN. UNKNOWN 이 TRUE인 경우 평가가 달리질 수 있기 때문.
FALSE AND UNKNOWN -- FALSE. UNKNOWN 과 상관 없이 이미 평가는 FALSE

TRUE OR UNKNOWN -- TRUE. UNKNOWN 과 상관 없이 이미 평가는 TRUE
FALSE OR UNKNOWN -- UNKNOWN. UNKNOWN 이 TRUE 인 경우 평가가 달라질 수 있기 때문.
```

## BETWEEN 조건
- `BETWEEN` 은 등호를 내재한 범위이다. 따라서 `NOT BETWEEN` 은 초과, 또는 미만의 범위에 해당한다.  
- 컬럼을 BETWEEN 대상에 포함할 수도 있다.
```oracle
SELECT * FROM tableA WHERE value BETWEEN colA AND colB;
```
## IN 조건
- 목록을 1000개까지만 지정할 수 있는 제약이 있다.

## LIKE 조건
### ESCAPE 활용
```oracle
SELECT * FROM tableA WHERE c1 LIKE '_\%_' ESCAPE '\';
```
### INSTR 함수
- 매개변수 문자열의 일부가 포함된 행을 조회한다.
- 아래 LIKE 문 활용보다 성능 측면에서 유리할 수 있다.(p.153)
```oracle
SELECT name FROM table1 WHERE 'AGENT SMITH' LIKE '%' || name || '%';
```

## NULL 조건
### NVL 활용
```oracle
SELECT * FROM table1 WHERE (col1 IS NULL or col1 = 0); -- 는 아래와 같다.

SELECT * FROM table1 WHERE NVL(col1, 0) = 0; -- 만약 col이 NULL이면 WHERE 문의 평가가 TRUE가 되기 때문이다.
```
#### LNNVL (condition)
조건이 FALSE, UNKNOWN 이면 TRUE, TRUE 면 FALSE 를 리턴한다.
따라서 위의 로직은 아래와 동일하다
```oracle
SELECT * FROM table1 WHERE LNNVL(col1 <> 0);
```

## 조건 우선순위
> 사견: 이러한 우선순위 규칙이 있지만 아무래도 쿼리 가독성을 위해 괄호를 무조건 작성하는 것이 낫지 않나?

## 활용
### 열 가공
- WHERE 문에서 열을 가공하는 경우 해당 열이 선두인 인덱스를 사용할 수 없는 성능 문제가 있다. (추후 다룸)
```oracle
SELECT * FROM emp WHERE SUBSTR(ename, 1, 1) = 'A'; -- 열 가공 중
SELECT * FROM emp WHERE ename LIKE 'A%'; -- 로 변경하면, 굳이 열을 가공하지 않고 있는 그대로의 값을 조회함.
```
- CASE 표현식에 다수의 열을 사용하는 것 또한 성능의 문제를 일으킨다. 다중 조건식을 사용하자.

### 날짜 기간 조회
- 기간 조회 시 `LIKE` 보단 `BETWEEN`을 사용하는 것이 성능 측면에서 유리하다.
- DATE, TIMESTAMP 타입에 대해서는 부등호와 `TO_DATE(문자열, 포맷)` 함수를 써보자.