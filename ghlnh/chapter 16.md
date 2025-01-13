![](https://velog.velcdn.com/images/ghlnh/post/0dcf8dc2-090b-4ca6-aa39-148c7121c479/image.png)
| 노드 | 설명 |
| --- | --- |
| 부모 노드 | 현재 노드의 직전 상위 노드 |
| 자식 노드 | 현재 노드의 직후 하위 노드 |

순환 관계는 계층의 깊이에 따라 level이 부여된다. 루트 노드는 레벨이 1이고 계층이 전개될수록 레벨이 증가한다. 

emp테이블의 empno열과 mgr열은 순환 관계를 가진다. 아래 쿼리에서 JONES의 부모 노드는 empno가 JONES의 mgr인 KING, 자식 노드는 mgr가 JONES의 empno인 SCOTT과 FORD다.
![](https://velog.velcdn.com/images/ghlnh/post/75d8db52-15c5-46ad-aaa0-5fce1d3b9b72/image.png)

![](https://velog.velcdn.com/images/ghlnh/post/c30ecd68-e33d-4d51-b7fe-0edfb818987d/image.png)

### 16.1.1 기본 문법

계층 쿼리 절은 WHBRE 절 다음에 기술하며, FROM 절이 수행된 후 수행된다. START WITH
절과 CONNECT BY 절로 구성되며, START WITH 절이 수행된 후 CONNECT BY 절이 수행 된다. STIART WITH 절은 생략이 가능하다.

```sql
[START WITH condition] CONNECT BY [NOCYCLE] condition
```

| 절 | 설명 |
| --- | --- |
| START WITH | 루트 노드를 생성하며 1번만 수행 |
| CONNECT BY | 루트 노드의 하위 노드를 생성하며 조회 결과가 없을 때까지 반복 수행 |

----

| 유형 | 항목 | 설명 |
| --- | --- | --- |
| 연산자 | PRIOR | 직전 상위 노드의 값을 반환 |
|  | CONNECT_BY_ROOT | 루트 노드의 값을 반환 |
| 슈도 칼럼 | LEVEL | 현재 레벨을 반환 |
|  | CONNECT_BY_ISLEAF | 리프 노드인 경우 1, 아니면 0을 반환 |
|  | CONNECT_BY_ISCYCLE | 루프가 발생한 경우 1, 아니면 0을 반환 |
| 함수 | SYS_CONNECT_BY_PATH | 루트 노드에서 현재 노드까지의 경로를 반환 |

아래는 계층 쿼리 절을 사용한 쿼리다. START WITH절로 mgr가 존재하지 않는 행을 조회하고, CONNECT BY절로 현재 노드의 mgr가 직전 상위 노드의 empno인 행을 반복해서 조회한다. ename열은 LEVEL 슈도 칼럼과 LPAD함수를 사용하여 계층의 레벨에 따라 값을 들여 쓰기 했다

### | SYS_CONNECT_BY_PATH 함수

SYS CONNECT BY PATH 함수는 루트 노드에서 현재 노드까지의 column을 cher로 구분하 여 연결한 값을 반환한다. column 값에 char가 포함되어 있으면 ORA-30004 에러, 연결한 값 의 길이가 4000보다 길면 ORA-01489 에러가 발생한다.

```sql
SYS_CONNECT_BY_PATH (column, char)
```

### 16.1.3 전개 방향

순방향 전개와 역방향 전개는 데이터 모델 상의 전개방향이 반대일 뿐 동작 원리는 동일하다

|  | 전개방향 | START WITH 절 | CONNECT BY 절 |
| --- | --- | --- | --- |
| 순방향 전개 | 부모→자식 | 부모 노드 조회 | PK에 PRIOR기술 |
| 역방향 전개 | 자식→부모 | 자식 노드 조회 | FK에 PRIOR기술 |

아래 쿼리는 순방향으로 계층을 전개한다. 

```sql
SELECT LEVEL AS lv, empno, LPAD('', LEVEL -1, '') || ename AS ename, mgr
	FROM emp
START WITH mgr IS NULL
CONNECT BY mgr = PRIOR empno;

LV  EMPNO  ENMAE    MGR
------------------------
1   7839   KING
...
3   7934   MILLER   7782
```

아래 쿼리는 역방향으로 계층을 전개한다

```sql
SELECT LEVEL AS lv, empno, LPAD('', LEVEL -1, '') || ename AS ename, mgr
	FROM emp
START WITH ename = 'ADAMS'
CONNECT BY empno = PRIOR mgr;

LV  EMPNO  ENMAE    MGR
------------------------
1    7876  ADAMS    7788
2    7788   SCOTT   7566
3    7566    JONES  7839
```

### 16.1.4 계층 정렬

계층 쿼리 절은 형제 노드의 행을 정렬하기 위해 SIBLINGS 키워드를 제공한다. ORDER BY 절에 SIBLINGS 키워드르 사용하면 형제 노드 내에서만 행이 정렬 되기 때문에 계층 구조를 유지한 상태로 행을 정렬 할 수 있다

```sql
SELECT LEVEL AS lv, empno, LPAD('', LEVEL -1, '') || ename AS ename, mgr, sal
	FROM emp
START WITH mgr IS NULL
CONNECT BY mgr = PRIOR empno
	ORDER SIBLINGS BY sal;
```

### 16.1.5 루프 처리

부모 노드가 현재 노드의 자식 노드로 연결되면 루프(loop)가 발생한다. 계층 쿼리 절은 루프를 처리하기 위해 NOCYCLE키워드와 CONNBCT_BY_ISCYCLE 슈도 칼럼을 제공한다.

아래는 emp_l테이블을 조회한 결과 이다. KING→JONES→SCOTT→KING 구간에서 루프가 발생한다
![](https://velog.velcdn.com/images/ghlnh/post/268b6176-0caf-412e-99bd-4f2f5fc1d219/image.png)
CONNECT BY절에 NOCYCLE 키워드를 기술하면 루프가 발생한 노드를 전개하지 않는다. 

## 16.2 재귀 서브쿼리 팩토링

### 16.2.1 기본 문법

재귀 서브 쿼리 팩토링은 WITH 절을 사용한다. 재귀 서브 쿼리 팩토링의 WITH 절은 서브 쿼 리, SEARCH 절, CYCLE 절로 구성된다.

```sql
WITH query_name ([c_alias [, _alias] …]) AS (subquery) [search_clause] [cycle_clause]
```

서브 쿼리는 UNION ALL 연산자로 구성된다. UNION ALL 연산자의 상단 쿼리가 START WITH 절, 하단 쿼리가 CONNECT BY 절의 역할을 수행한다. WITH 절에 정의한 서브 쿼리를 하단 쿼리와 조인함으로써 재귀적으로 조인이 수행되는 방식으로 동작한다.

### 16.2.2 계층 정렬

재귀 서브 쿼리 팩토링은 계층을 정렬하기 위해 SEARCH 절을 제공한다. BREADTH 방 과 DEPTH 방식을 사용할 수 있으며, FIRST BY 뒤에 기술된 c_alias에 따라 행이 정렬된다 ordering_column은 정렬 순번이 반환될 열을 지정한다.

```sql
SEARCH {DEPTH | BREADTH} FIRST BY (_alias [, c_alias]…SET ordering_column
```

| 방식 | 설명 |
| --- | --- |
| BREADTH | 자식 행을 반환하기 전에 형제 행을 반환 (기본값) |
| DEPTH | 형제 행을 반환하기 전에 자식 행을 반환 ( = 계층 쿼리 절) |

아래는 BREADTH 방식을 사용한 쿼리다. BREADTH 방식은 너비를 기준으로 계층을 탐색한다. 2레벨이 모두 반환 된 후 3레벨이 반환된다

```sql
WITH w1 (empno, ename, mgr, lv) AS (
  SELECT empno, ename, mgr, 1 AS lv 
  FROM emp 
  WHERE mgr IS NULL
  UNION ALL
  SELECT c.empno, c.ename, c.mgr, p.lv + 1 AS lv
  FROM w1 p, emp c
  WHERE c.mgr = p.empno
)
SEARCH BREADTH FIRST BY empno SET so
SELECT lv, empno, LPAD(' ', lv - 1, ' ') || ename AS ename, mgr, so
FROM w1
ORDER BY so;

LV  EMPNO  ENAME     MGR   SO
--------------------------------
1   7839   KING             1
2   7566    JONES    7839   2
2   7698    BLAKE    7839   3
2   7782    CLARK    7839   4

```

아래는 DEPTH방식을 사용한 쿼리다. DEPTH 방식은 깊이를 기준으로 계층을 탐색한다. 계층 쿼리 절과 결과가 동일하다

```sql
WITH w1 (empno, ename, mgr, lv) AS (
  SELECT empno, ename, mgr, 1 AS lv 
  FROM emp 
  WHERE mgr IS NULL
  UNION ALL
  SELECT c.empno, c.ename, c.mgr, p.lv + 1 AS lv
  FROM w1 p, emp c
  WHERE c.mgr = p.empno
)
SEARCH DEPTH FIRST BY empno SET so
SELECT lv, empno, LPAD(' ', lv - 1, ' ') || ename AS ename, mgr, so
FROM w1
ORDER BY so;

LV  EMPNO  ENAME     MGR   SO
--------------------------------
1   7839   KING             1
2   7566    JONES    7839   2
3   7788     SCOTT   7566   3
4   7876      ADAMS  7788   4
```

### 16.2.3 루프 처리

재귀 서브 쿼리 팩토링은 루프를 처리하기 위해 CYCLE 절을 제공한다. CYCLE 절은 상위 노드 에 동일한 c_alias 값이 존재하면 루프가 발생한 것으로 인식한다.

```sql
CYCLE (c_alias [, c_alias] … SET cycle_mark_c_alias TO cycle_value DEFAULT no_cycle_value
```

| 항목 | 설명 |
| --- | --- |
| c_alias [, c_alias] | 루프 여부를 확인할 열 |
| cycle_mark_c_alias | 루프 여부를 반환할 열 |
| cycle_value | 루프가 발생한 경우 반환할 값 |
| no_cycle_value | 루프가 발생하지 않은 경우 반환할 값 |

아래는 CYCLE절을 사용한 쿼리다. 상위 노드(LV = 1)에 현재 노드 (LV = 4)의 empno인 7839와 동일한 empno가 존재하기 때문에 루프가 발생한 것으로 인식한다. 계층 쿼리 절과 달리 루프가 발생한 노드까지 반환된다. ic열이 1인 행이 루프가 발생한 행이다.

```sql
WITH w1 (empno, ename, mgr, lv) AS (
  SELECT empno, ename, mgr, 1 AS lv 
  FROM emp 
  WHERE empno = 7839
  UNION ALL
  SELECT c.empno, c.ename, c.mgr, p.lv + 1 AS lv
  FROM w1 p, emp c
  WHERE c.mgr = p.empno)
SEARCH DEPTH FIRST BY empno SET so
CYCLE empno SET ic TO '1' DEFAULT '0'
SELECT lv, empno, LPAD(' ', lv - 1, ' ') || ename AS ename, mgr, ic
FROM w1
ORDER BY so;
```

### 16.3.1 노드 제거

CONNECT BY 절이나 WHERE 절에 조건을 기술하면 조건을 만족하지 않는 노드를 제거할 수 있다.
아래 쿼리는 CONNECT BY 절에 empno <>7698 조건을 기술했다. CONNECT BY 절에 조건 을 기술하면 조건을 만족하지 않는 노드와 해당 노드의 모든 하위 노드가 제거된다. 계층 전개 시점에 노드가 제거되기 때문에 하위 노드까지 제거되는 것이다.
![](https://velog.velcdn.com/images/ghlnh/post/7ba666b6-3462-454f-98ba-7c7e28ee22a7/image.png)
아래 쿼리는 WHERE 절에 empno <>7698 조건을 기술했다. WHBRE 절에 조건을 기술하면 조건을 만족하지 않는 노드만 제거된다. CONNECT BY 절이 수행된 후 WHERE 절이 수행되 기 때문이다.
![](https://velog.velcdn.com/images/ghlnh/post/51cc80d8-fee7-46e4-b6cb-a21664ea2de4/image.png)
### 16.3.4 계층 쿼리와 조인

계층쿼리는 세 가지 방식으로 조인을 수행할 수 있다.

아래 쿼리는 계층을 전개한 후 조인을 수행한다. 인라인 뷰를 사용한 조인과 동일하다

```sql
SELECT a.lv, a.empno, a.ename, b.dname
FROM (
    SELECT LEVEL AS lv, empno, LPAD('', LEVEL - 1, ' ') || ename AS ename, deptno, ROWNUM AS rn
    FROM emp
    START WITH mgr IS NULL
    CONNECT BY mgr = PRIOR empno
) a,
dept b
WHERE b.deptno = a.deptno
ORDER BY a.rn;

```

아래 쿼리는 조인을 수행한 후 계층을 전개한다. b.loc = ‘NEW YORK’조건처럼 계층을 전개할 대상을 먼저 정의해야 할 때 사용할 수 있다

```sql
SELECT LEVEL AS lv, empno, LPAD('', LEVEL - 1, ' ') || ename AS ename, deptno, dname
FROM (
    SELECT a.*, b.dname
    FROM emp a, dept b
    WHERE b.deptno = a.deptno
      AND b.loc = 'NEW YORK'
)
START WITH mgr IS NULL
CONNECT BY mgr = PRIOR empno;

```

WHERE절에 조인 조건을 기술하면 조인을 수행한 후 계층을 전개한다. 위 쿼리처럼 인라인 뷰를 사용하는 편이 명시적이다.

```sql
SELECT  LEVEL AS lv, a.empno, LPAD('', LEVEL -1, '') || a.name AS ename, a.deptno, b.dname
	FROM emp a, dept b
	WHERE b.deptno = a.deptno
START WITH a.mgr IS NULL
CONNECT BY a.mgr = PRIOR a.empno;
```

아래 쿼리는 계층 전개 시점에서 조인을 수행한다. START WITH절과 CONNECT BY절에 조인 조건을 기술해야 한다. b.loc = ‘DALLAS’조건처럼 계층 전개 중 노드를 제한해야 할 때 사용할 수 있다

```sql
SELECT a.*, b.dname
FROM emp a, dept b
START WITH a.mgr IS NULL
  AND b.deptno = a.deptno
CONNECT BY a.mgr = PRIOR a.empno
  AND b.deptno = a.deptno
  AND b.loc = 'DALLAS';
```




