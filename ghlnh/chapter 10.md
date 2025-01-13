GROUP BY 절은 WHERE 절 다음에 기술하며, WHERE 절이 수행된 후 수행된다. HAVING
절은 GROUP BY 절 다음에 기술하며, GROUP BY 절이 수행된 후 수행된다.

SELECT 절 -- 5️⃣
FROM 절-- 1️⃣
WHERE 절-- 2️⃣
GROUP BY 절-- 3️⃣
HAVING 절 -- 4️⃣
ORDER BY 절-- 6️⃣

GROUP BY 절을 사용한 쿼리는 SELECT 절과 ORDER BY 절 에 GROUP BY 절의 표현식이나 집계 함수를 사용한 표현식만 기술할 수 있다. 그렇지 않으면 결과 값을 결정할 수 없기 때문에 에러가 발생한다.

중복 값 제거를 위한 목적이라면 DISTINCT 키워드를 사용하는 편이 바람직하다. GROUP BY
절은 집계 함수와 함께 사용해야 한다.

집계 함수를 사용한 표현식도 단일 행으로 처리한다. 아래쿼리는 SUM함수의 결과값에 GREATEST 함수를 사용했다.
```sql
SELECT deptno, GREATEST (SUM(sal), 5000) AS sal
	FROM dmp
    WHERE sal > 2000
GROUP BY deptnl
ORDER BY 1;

DEPTNO   SQL
------------
    10  7450
    20  8975
    30  5000
```

## 10.2 GROUP BY 절의 확장기능
GROUP BY 절의 확장 기능을 사용하면 하나의 쿼리로 다수의 행그룹을 집계할 수 있다.

### 10.2.1 ROLLUP
지정한 표현식의 계층별 소계와 총계를 집계한다.
```sql
ROLLUP {expression_list [ , expression_list]...}
```
ROLLUP은 아래와 같이 동작한다. expr을 뒤쪽으로부터 하나씩 제거하는 방식이다. 겨로가에서 (a,b,c)는 a,b,c의 소계, ()는 총계를 의미한다.

집계 함수를 사용한 쿼리는 WHERE 절을 만족하는 행이 없더라도 하나의 행을 반환하지만
GROUP BY 절을 사용하면 결과가 반환되지 않는다.

| GROUP BY | 결과 |
| --- | --- |
| ROLLUP (a) | (a), () |
| ROLLUP (a, b) | (a, b), (a), () |
| ROLLUP (a, b, c) | (a, b, c), (a, b), (a), () |

### 10.2.2 CUBE
CUBE는 지정한 표현식의 모든 조합을 집계한다.
```sql
CUBE {expression_list, [, expresstion, list]...)
```
| GROUP BY | 결과 |
| --- | --- |
| CUBE (a) | (a), () |
| CUBE (a, b) | (a, b), (a), (b), () |
| CUBE (a, b, c) | (a, b, c), (a, b), (a, c), (b, c), (a), (b), (c) () |