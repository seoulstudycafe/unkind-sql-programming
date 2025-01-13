이번 장은 PIVOT 절과 UNPIVOT 절을 살펴보자. PIVOT은 회전시킨다는 의미를 가지고 있 다. PIVOT 절은 행을 열로 회전시키고, UNPIVOT 절은 열을 행으로 회전시킨다. PIVOT 절과 UNPIVOT 절은 11.1 버전부터 사용할 수 있다.

## 17.1 PIVOT절

PIVOT절은 행을 열로 전환한다. 좌측 데이트 집합을 우측 형태로 변경할 수 있다. deptno 행의 값이 10, 20, 30열로 전환되었다.

| JOB | DEPTNO | SAL |
| --- | --- | --- |
| ANALYST | 20 | 6000 |
| CLERK | 10 | 1300 |
| CLERK | 20 | 1900 |
| CLERK | 30 | 950 |
| MANAGER | 10 | 2450 |
| MANAGER | 20 | 2975 |
| MANAGER | 30 | 2850 |
| PRESIDENT | 10 | 5000 |
| SALESMAN | 30 | 5600 |

--

| JOB | 10 | 20 | 30 |
| --- | --- | --- | --- |
| ANALYST |  | 6000 |  |
| CLERK | 1300 | 1900 | 950 |
| MANAGER | 2450 | 2975 | 2850 |
| PRESIDENT | 5000 |  |  |
| SALESMAN |  |  | 5600 |

### 17.1.1 기본문법

PIVOT절의 구문은 아래와 같다

```sql
PIVOT [XML]
			(aggregate_function (expr)  [[AS] alias]
		[, aggregate_function (expr)  [[AS] alias]]...
			FOR {column | (column [, column]...))
			IN ({{{expr | (expr [, Eexpr]...)} [AS] alias]}...
			     | subquery
			     | ANY [, ANY]...
			     })
		)
```

| 항목 | 설명 |
| --- | --- |
| aggregate_function  | 집계할 열을 지정 |
| FOR 절 | PIVOT할 열을 지정 |
| IN 절 | PIVOT할 열 값을 지정 |

아래는 PIVOT 절을 사용한 쿼리다. PIVOT 절은 집계함수와 FOR절에 지정되지 않은 열을 기준으로 집계되기 때문에 인라인 뷰를 통해 사용할 열을 지정해야 한다.

```sql
SELECT * 
	FROM (SELECT job, deptno, sal FROM emp)
	PIVOT (SUM(sal) FOR deptno IN (10,20,30))
ORDER BY 1;

JOB       10      20    30
---------------------------
ANALYST         6000
CLERK     1300  1900    950
MANAGER   2450  2975   2850
PRESIDENT 5000
SALESMAN               5600
```

집계함수와 IN절이 지정한 별칭에 따라 아래와 같은 규칙으로 열명이 부여된다. 집계 함수와 IN절 모두 별칭을 지정하는 편이 바람직한다

|  | 10 | 10 AS d10 |
| --- | --- | --- |
| SUM (sal) | 10 | D10 |
| SUM (sal) AS sal | 10_SAL | D10_SAL |

PIVOT 절은 다수의 집계 함수를 지원한다. 아래 쿼리는 SUM 함수와 COUNT 함수를 사용했다.

```sql
SELECT *
FROM (SELECT job, depto, sal FROM emp)
PIVOT (SUM (sal) AS sal, COUNT (*) AS cnt FOR depto IN (10 AS d10, 20 AS d20))
ORDER BY 1;
```

## 17.2 UNPIVOT절

UNPIVOT절은 PIVOT절과 반대로 동작한다. 열을 행으로 전환한다

| JOB | 10 | 20 | 30 |
| --- | --- | --- | --- |
| ANALYST |  | 6000 |  |
| CLERK | 1300 | 1900 | 950 |
| MANAGER | 2450 | 2975 | 2850 |
| PRESIDENT | 5000 |  |  |
| SALESMAN |  |  | 5600 |

--

| JOB | DEPTNO | SAL |
| --- | --- | --- |
| ANALYST | 20 | 6000 |
| CLERK | 10 | 1300 |
| CLERK | 20 | 1900 |
| CLERK | 30 | 950 |
| MANAGER | 10 | 2450 |
| MANAGER | 20 | 2975 |
| MANAGER | 30 | 2850 |
| PRESIDENT | 10 | 5000 |
| SALESMAN | 30 | 5600 |

### 17.2.1 기본 문법

UNPIVOT 절의 구문은 아래와 같다.

```sql
UNPIVOT [{INCLUDE | EXCLUDE} NULLS]
				(   {column | (column [, col]...)}
				FOR {column | (column [, col]...)}
				IN ({column | (column [, col]...)} [AS {literal | (literal [, literal]...(}]
				 [, {column | (column [, col]...)} [AS {literal | (literal [, literal]...(}]...
				   )
				)
```

| 항목 | 설명 |
| --- | --- |
| UNPIVOT column | UNPIVOT된 값이 들어갈 열을 지정 |
| FOR 절 | UNPIVOT된 값을 설명할 값이 들어갈 열을 지정 |
| IN 절 | UNPIVOT할 열과 설명할 값의 리터럴 값을 지정 |

아래는 UNPIVOT 절을 사용한 쿼리다. d10 sal, d20_sal 열이 행으로 전환된다.

```sql
SELECT job, deptno, sal
FROM t1
UNPIVOT (sal FOR deptno IN (d10_sal, d20_sal))
ORDER BY 1, 2;
```

아래와 같이 PIVOT 절과 UNPIVOT 절을 함께 사용할 수도 있다. PIVOT 절의 결과가 UNPIVOT 절에 인라인 뷰로 공급되는 방식이다. INCLUDE NULLS 키워드로 파티션 아우터 조인의 동작을 모방할 수 있다.

```sql
SELECT *
FROM (
    SELECT job, deptno, sal, comm FROM emp
)
PIVOT (
    SUM(sal) AS sal, SUM(comm) AS comm
    FOR deptno IN (10 AS d10, 20 AS d20, 30 AS d30)
)
UNPIVOT INCLUDE NULLS
    ((sal, comm) FOR deptno IN ((d10_sal, d10_comm) AS 10, 
                                (d20_sal, d20_comm) AS 20, 
                                (d30_sal, d30_comm) AS 30))
ORDER BY 1, 2;

JOB         DEPTNO  SAL   COMM
----------  ------  ----  ------
ANALYST     10      
ANALYST     20      6000  
ANALYST     30      
CLERK       10      1300  
CLERK       20      1900  
CLERK       30      

```