## 9.1 기본함수

### COUNT함수

```sql
COUNT ({* | [DISTINCT| ALL] expr})
```

| 유형 | 설명 |
| --- | --- |
| COUNT(*) | 전체 행의 개수를 반환 |
| COUNT(expr) | 널이 아닌 expr의 개수를 반환 |
| COUNT(DISTINCT expr) | 널이 아닌 expr의 고유한 개수를 반환 |

### MIN함수

expr의 최저값을 반환

### MAX함수

expr의 최고값을 반환

### SUM함수

expr의 합계값을 반환. expr은 숫자 값만 입력할 수 있다

```sql
SUM ([DISTINCT | ALL] expr)
```

### AVG함수

expr의 평균값을 반환한다.

```sql
AVG ([DISTINCT | ALL] expr)
```

## 9.2 통계함수

### STDDEV함수

expr의 표준편차를 반환

```sql
STDDEV ([DISTINCT | ALL] expr)
```

### VARIANCE함수

expr의 분산을 반환

```sql
VARIANCE ([DISTINCT | ALL] expr)
```

### STATS_MODE함수

expr의 최빈값을 반환

## 9.3 순위함수

순위함수는 expr로 가상의 행을 생성하고, 가상의 행에 해당하는 순위를 반환한다.

### RANK함수

expr에 대한 순위를 반환한다. expr이 동일하면 동순위를 부여하고, 다음 순위는 동순위의 수만큼 건너뛴다.

```sql
RANK (expr [,expr]...) WITHIN GROUP (ORDER BY expR [,expr]...)
```

### DENSE_RANK함수

DENSE_RANK함수도 expr에 대한 순위를 반환한다. 동순위가 존재하더라도 다음 순위를 이어서 부여한다.

```sql
DENSE_RANK (expr [,expr]...) WITHIN GROUP (ORDER BY expr [,expr]...)
```

### CUME_DIST함수

expr의 누적분포값을 반환한다. 누적분포값은 0<y≤1의 범위를 가진다

```sql
CUME_DIST (expr [,expr]...) WITHIN GROUP (ORDER BY expr [,expr]...)
```

### PERCENT_RANK함수

expr의 백분위 순위값을 반환한다. 백분위 순위 값은 0<y≤1의 범위를 가진다

```sql
PERCENT_RANK (expr [,expr]...) WITHIN GROUP (ORDER BY expr [,expr]...)
```

## 분포함수

분포함수는 분포 모형에 따른 분포값을 반환한다.

### PERCENTILE_CONT함수

연속 분포 모델에서 expr에 해당하는 백분위 값을 반환한다. expr은 0~1 범위를 지정할 수 있다.

```sql
PERCENTILE_CONT (expr [,expr]...) WITHIN GROUP (ORDER BY expr)
```

### PERCENTILE_DISC함수

이산 분포 모델에서 expr에 해당하는 백분위 값을 반환한다. expr은 0~1 범위를 지정할 수 있다.

```sql
PERCENTILE_DISC (expr [,expr]...) WITHIN GROUP (ORDER BY expr)
```

### MEDIAN함수

연속분포모형을 가정한 중앙값을 반환한다. PERCENTILE_CONT(0,5)표현식과 결과가 동일하다.

## 9.5 기타함수

### LISTAGG함수

measure_expr를 order_by_clause로 정렬한 후 delimiter로 구분하여 연결한 값을 반환한다. delimiter의 기본값은 널이다.

```sql
LISTAGG (measure_expr [, 'delimiter'][listagg_overflow_clause])
								WITHIN GROUP (order_by_clause)
```

아래는 LISTAGG함수를 사용한 쿼리다

```sql
SELECT LISTAGG (ename,',') WITHIN GROUP (ORDER BY ename) AS c1 
FROM emp WHERE deptno = 30;

C1
---------------------------
ALLEN, BLANK, JAMES, MARTIN, TUNRNER, WARD
```

연결된 문자열이 4000자보다 길면 에러가 발생한다

LISTAGG OVERFLOW절을 사용하면 4000자 이상의 무자열을 사용할 수 있다. LISTAGG OVERFLOW절은 12.2버전부터 사용할 수 있다

```sql
ON OVERFLOW {ERROR | {TRUNCATE ['truncation-indicator' [WITH | WITHOUT} COUNT]}
```

| 항목 | 설명 |
| --- | --- |
| ON OVERFLOW ERROR | 문자열이 4000자보다 길면 에러를 발생 시킴 (기본값) |
| ON OVERFLOW TRUNCATE | 문자열이 4000자보다 길면 문자열을 잘라냄 |
| truncation-indicator | 줄임기호를 지정함 (기본값은 …) |
| WITH COUNT | 잘려진 문자수를 표현함 (기본값) |
| WITHOUT COUNT | 잘려진 문자수를 표시하지 않음 |

## 9.6 KEEP키워드

KEEP키워드를 사용하면 행 그룹의 최저 또는 최고 순위 행으로 집계를 수행 할 수 있다. 기본 함수와 일부 통계 함수에 사용할 수 있다

```sql
aggregate_function KEEP (DENSE_RANK {FIRST | LAST} ORDER BY expr
```

| 항목 | 설명 |
| --- | --- |
| DENSE_RANK FIRST | 정렬된 행 그룹에서 최저 순위행을 지정 |
| DENSE_RANK_LAST | 정렬된 행 그룹에서 최고 순위행을 지정 |