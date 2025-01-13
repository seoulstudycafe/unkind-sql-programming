### 15.1.1 ROWNUM 방식

ROWNUM 방식은 오라클 데이터베이스의 전통적인 Top-N 쿼리 방식이다. ORDER BY 절로 행을 정렬하고, 정렬된 행을 ROWNUM 슈도 칼럼으로 제한한다.
ROWNUM 슈도 칼럼은 행이 반환되는 순서대로 순번을 반환한다. 1부터 시작하고 행이 반환될 때마다 순번이 증가한다.

```sql
SELECT empno, sal, ROWNUM AS rn FROM emp;
```

아래 쿼리는 결과가 반환되지 않는다. BOWNUM 슈도 칼럼은 1부터 시작하고 행이 반환될 때 마다 순번이 증가하기 때문에 ROWNUM = 2 조건은 항상 FALSE다.

```sql
SELECT empno, sal, ROWNUM AS rn FROM emp WHERE ROWNUM = 2;
선택된 레코드가 없습니다.
```

스칼라 서브 쿼리의 정렬 조건이 반환 값과 다른 경우 아래와 같이 KEEP 절을 사용해야한다

```sql
SELECT a.deptno
, (SELECT MAX (x. empno) KEEP (DENSE_RANK FIRST ORDER BY sal DESC)
FROM emp x
WHERE X. deptno = a. deptno) AS empno
FROM dept a;
```

### 15.1.2 분석 함수 방식

분석 함수 방식은 14장에서 살펴본 순위 분석 함수를 사용한다. 아래는 ROW_NUMER 함수를 사용한 Top-N쿼리다.

```sql
SELECT *
	FROM (SELECT empno, sal
							,ROW_NUMBER() OVER (ORDER BY sal, empno) AS rn
						FROM emp)
	WHERE rn <= 5
ORDER BY sal, empno;

EMPNO  SAL  RN
---------------
7369   800   1
7900   950   2
7876  1100   3
7521  1250   4
7654  1250   5 
```

아래는 ROW_NUMBER 함수를 사용한 페이징 쿼리다. ROWNUM방식에 비해 간결하게 작성할 수 있다

```sql
SELECT *
	FROM (SELECT empno, sal
							,ROW_NUMBER() OVER (ORDER BY sal, empno) AS rn
	WHERE rn BETWEEN (:V_pr * (:v_pn - 1)) + 1 AND :v_pr * v:pn
ORDER BY sal, empno;

EMPNO  SAL  RN
---------------
7934  1300   6
7844  1500   7
7499  1600   8
7782  2450   9
7698  2850  10
```

### 15.1.3 ROW LIMITING 절

12.1 버전부터 ROW LIMITING 절로 Top-N 쿼리를 작성할 수 있다. ROW LIMITING 절은 ANSI 표준 SQL 문법이다.
아래는 ROW LIMITING 절의 구문이다. ROW LIMITING 절은 ORDER BY 절 다음에 기술 며, ORDBR BY 절과 함께 수행된다. ROW와 ROWS는 구분하지 않아도 된다.

```sql
[OFFSET offset {ROW | ROWS}]
[FETCH {FIRST | NEXT} [{rowcount | percent PERCENT}] {ROW | ROWS} {ONLY | WITH TIES}]
```

| 항목 | 설명 |
| --- | --- |
| OFFSET offset | 건너뛸 행의 개수를 지정 |
| FETCH | 반환할 행의 개수나 백분율을 지정 |
| ONLY | 지정된 행의 개수나 백분율만큼 행을 반환 |
| WITH TIES | 마지막 행에 대한 동순위를 포함해서 반환 |

아래는 ROW LIMITING 절을 사용한 Top-N쿼리다.

```sql
SELECT empno, sal FROM emp ORDER BY sal, empno FETCH FIRST 5 ROWS ONLY;

EMPNO   SAL
-----------
7369    800
7900    950
7876   1100
7654   1250
7521   1250
```

## 15.2 고급주제

### 15.2.1 Top-N쿼리와 조인

아래 쿼리는 조인 후 Top-N 처리를 수행한다. emp 테이블이 14건이므로 조인을 14번 수행하고 2건의 결과를 반환한다. emp 테이블과 dept 테이블은 조인 차수가 M:1 이고, 아우터 조인으로 조인했기 때문에 emp 테이블을 Top-Nㅋ 처리한 후 dept 테이블을 조인해도 동일한 결과를 얻을 수 있다.

```sql
SELECT empno, sal, deptno, dname
	FROM (SELECT a.empno, a.sal, a.deptno, b.dname
					FROM emp a, dept b
				WHERE b.deptno(+) = a.deptno
				ORDER BY a.sal, a.empno)
WHERE ROWNUM <=2;

EMPNO  SAL    DEPTNO   DNAME
-----------------------------
7369   800      20   RESEARCH
7900   950      30   SALES
```

아래 쿼리는 위 쿼리와 결과가 동일히다. 인라인 뷰에서 Top-N 처리한 결과 집합으로 dept 테이블을 아우터 조인한다. 조인은 2번만 수행된다.

```sql
SELECT empno, sal, deptno, dname
	FROM (SELECT empno, sal, deptno FROM emp ORDER BY sal, empno)
				WHERE ROWNUM <=2) a
				, dept b
	WHERE b.deptno(+) = a.deptno
ORDER BY a.sal, a.empno;
```

### 15.2.2 Top-N 쿼리와 UNION ALL 연산자

아래 쿼리는 dept테이블과 emp 테이블을 UNION ALL 연산자로 연결한 결과 집합에 Top-N 처리를 수행한다. 결과 집합을 정렬해야 하므로 소트 부하가 발생할 수 있다

```sql
SELECT *
	FROM (SELECT 1 AS tp, deptno AS no, dname AS name FROM dept
				UNION ALL
				SELECT 2 AS tp, empno AS no, ename AS name FROM emp
				ORDER BY tp, no)
	WHERE ROWNUM <=3;
```

아래 쿼리 처럼 데이터 집합 별로 Top-N 처리를 수행하면 소트 부하를 경감 시킬 수 있다. UNION ALL 연산자는 순차적으로 수행된다. 아래 쿼리는 emp 테이블을 읽지 않고 결과를 반환한다.

```sql
SELECT * 
	FROM (SELECT *
					FROM (SELECT 1 AS tp, deptno AS no, dname AS name FROM dept ORDER BY no)
					WHERE ROWNUM <=3
					UNION ALL
				SELECT *
					FROM (SELECT 2 AS tp, empno AS no, ename AS name FROM emp ORDER BY no)
					WHERE ROWNUM <=3)
	WHERE ROWNUM <=3;

TP NO   NAME
-----------------
1  10   ACCOUNTING
1  20   RESEARCH
1  30   SALES
```