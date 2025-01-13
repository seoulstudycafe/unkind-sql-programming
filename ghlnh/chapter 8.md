ORDER BY 절은 SELECT 문의 마지막에 기술하며, SELECT절이 수행된 후 수행된다

1️⃣FROM 절

2️⃣WHERE 절

3️⃣SELECT절

4️⃣ORDER BY 절

## 8.1 기본 문법

| 항목 | 설명 |
| --- | --- |
| ASC | 오름차순으로 정렬(기본) |
| DESC | 내림차순으로 정렬 |
| NULL FIRST | 널을 앞쪽으로 정렬 (내림차순 정렬 시 기본값) |
| NULL LAST | 널을 뒤쪽으로 정렬 (오름차순 정렬 시 기본값) |

SELECT절의 열 위치를 지정할 수 있다. SELECT 절이 ORDER BY절보다 먼저 수행되기 때문에 열 위치를 참조할 수 있는 것이다. 아래 쿼리는 위 쿼리와 동일하다

```sql
SELECT ename, sal, comm FROM emp WHERE deptno = 30 ORDER BY sal DESC, comm;

SELECT ename, sal, comm FROM emp WHERE deptno = 30 ORDER BY 2 DESC, 3;
```

## 8.2 활용예제

ORDER BY절의 열을 가공하면 쿼리의 성능이 저하 될 수 있다. 아래의 예제는 정렬할 데이터가 크지 않은 경우에만 사용해야 한다.

### 조건 정렬

ORDER BY 절에 CASE 표현식을 사용하면 조건에 따라 다른 정렬 기준을 지정할 수 있다.

아래 쿼리는 job을 MANAGER, CLERK 순서로, sal을 오름차순으로 정렬한다

```sql
SELECT job, sal FROM emp WHERE deptno = 20
ORDER BY CASE job WHEN 'MANAGER' THEN 1 WHEN 'CLERK' THEN 2 END
				, sal;
```

### 동적 정렬

바인드 변수를 사용하면 정렬 순서를 동적으로 조정할 수 있다.

아래는 ORDER BY절에 바인드 변수를 사용한 쿼리다. 첫번째 쿼리는 결과를 dname으로 정렬하고, 두번째 쿼리는 결과를 loc로 정렬한다.

```sql
VAR v1 NUMBER = 1;

SELECT * FROM dept ORDER BY DECODE(:v1, 1, dname, 2, loc);
```

```sql
EXEC :v1 := 2;

SELECT * FROM dept ORDER BY DECODE(:v1, 1, dname, 2, loc);
```

### 정렬 순서 조정

캐릭터 셋에 따라 특수문자, 숫자, 영문, 한글의 정렬순서가 달라질 수 있다.

오름차순으로 정렬 시, 특수문자-숫자-영문-한글 순으로 정렬된다.