분석함수는 집계 함수의 확장 기능으로 생가할 수 있다. 집계함수는 행 그룹으로 값을 집계하고, 분석함수는 파티션과 윈도우로 값을 집계한다. 집계 함수는 행 그룹 별로 단일 행을 반환하기 때문에 데이터 집합이 변경 되지만, 분석함수는 데이터 집합을 변경하지 않고 값을 집계한다. 데이터 집합이 변경되지 않기 때문에 원본값과 집계 값을 함께 분석할 수 있다.

SUM함수는 OVER절을 기술하면 분석함수, 기술하지 않으면 집계함수로 동작한다.

## 14.1 기본문법

분석함수는 OVER 키워드를 사용한다. 집계 함수에 OVER 키워드를 기술하면 분석함수로 동작한다. OVER 키워드에 ANALYTIC 절을 기술할 수 있다.

```sql
analytic_function ([arguments]) OVER (analytic_clause)
```

ANALYTIC 절은 QUERY PARTITION절, ORDER BY 절, WINDOWING 절로 구성된다.

### 14.1.1 QUERY PARTITION절

QUERY PARTITION절은 GROUP BY 절과 유사하게 동작한다. 파티션은 행 그룹과 유사하다.

```sql
PARTITION BY expr [, expr]...
```

### 14.1.2 ORDER BY절

파티션 내의 정렬 순서를 지정할 수 있다.

### 14.1.3 WINDOWING 절

```sql
{ROWS | RANGE}
{BETWEEN {UNBOUNDED PRECEDING | CURRENT ROW | value_expr {PRECEDING | FOLLOWING)}
 AND {UNBOUNDED FOLLOWING / CURRENT ROW I value_expr {PRECEDING | FOLLOWING)}
HUNBOUNDED PRECEDING | CURRENT ROW / value_expr PRECEDING}}
```

### 14.1.4 KEEP 키워드

분석 함수도 KBEP 키워드를 사용할 수 있다. KEEP 키워드를 사용하 QUERY PARTITION 절만 사용할 수 있다.

```sql
analytic _function ([arguments]) KEEP (DENSE_RANK {FIRST|LAST) ORDER BY expr)
[OVER ([query _partition_clause])]
```

- MIN 함수
MIN 함수는 expr의 최저 값을 반환한다.
    
    ```sql
    MIN (expr) OVER (analytic_clause)
    ```
    
- MAX 함수
    
    MAX 함수는 expr의 최고 값을 반환한다.
    
    ```sql
    MAX (expr) OVER (analytic_clause)
    ```
    
- SUM 함수
    
    SUM 함수는 expr의 합계 값을 반환한다.
    
    ```sql
    SUM ([DISTINCT | ALL] expr) OVER (analytic_clause)
    ```
    
- AVG 함수
    
    AVG 함수는 expr의 평균 값을 반환한다.
    
    ```sql
    AVG ([DISTINCT | ALL] expr) OVER (analytic_clause)
    ```
    
- STDDEV 함수
    
    STDDEV 함수는 expr의 표준편차를 반환한다.
    
    ```sql
    STDDEV ([DISTINCT | ALL] expr) OVER (analytic_clause)
    ```
    
- VARIANCE 함수
    
    VARIANCE 함수는 expr의 분산을 반환한다.
    
    ```sql
    VARIANCE (DISTINCT , ALL expr) OVER (analytic_clause)
    ```
    
- RANK 함수
    
    RANK 함수는 order_by_clause에 따른 순위를 반환한다. expr이 동일하면 동순위를 부여하고, 다음 순위는 동순위의 개수만큼 건너뛴다.
    
    ```sql
    RANK () OVER ([query_partition clause] order_by_clause)
    ```
    
- DENSE_RANK
    
    DENSE RANK 함수도 order_by_dlause에 따른 순위를 반환한다. 다음 순위를 이어서 부여한다.
    
    ```sql
    DENSE_RANK () OVER ([query partition clause] order_by_clause)
    ```
    
- ROW_NUMBER 함수
    
    ROW_NUMBER 함수는 order_by_clause에 따른 고유한 순번을 주더라도 다른 순번을 부여하기 때문에 결과 값이 변경될 수 있다.
    
    ```sql
    ROW_NUMBER () OVER ([query_partition clause] order_by_clause
    ```
    
- NTILE 함수
    
    NTILE 함수는 order by_clause에 따라 행을 정렬하고, expr의 개수만큼 버킷을 생성한 후, 행 에 해당하는 버킷 번호를 할당한다.
    
    ```sql
    NTILE (expr) OVER ([query_partition clause] order_by_clause)
    ```
    
- CUME_DIST 함수
    
    누적 분포 값을 반환한다. 누적분포 값은 0<y ≤ 1의 범위를 가진다.
    
    ```sql
    CUME_DIST () OVER ([query_partition clause] order_by_clause)
    ```
    
- PERCENT_RANK
    
    PERCENT_RANK 함수는 백분위 순위 값을 반환한다. 백분위 순위는 순위의 대상을 100건으 로 가정했을 때의 상대 순위다. 백분위 순위 값은 0≤ y ≤ 1의 범위를 가진다.
    
    ```sql
    PERCENT_RANK () OVER ([query_partition clause] order_by_clause)
    ```
    
- RATIO_TO_REPORT 함수
    
     expr의 합계에 대한 현재 expr의 비율을 반환한다. expr이 널이면 널을 반환한다.
    
    ```sql
    RATIO_TO_ REPORT (expr) OVER ([query_partition clause])
    ```
    

### 14.2.4 분포 함수

분포 함수는 분포 모형에 따른 분포 값을 반환한다.

- PERCENTILE_CONT 함수
    
    PERCENTILE_CONT 함수는 연속 분포 모델에서 expr에 지정한 백분위 값에 해당 계산한다. expr은 0~ 1의 범위를 지정할 수 있다.
    
    ```sql
    PERCENTILE_CONT (expr) WITHIN GROUP (ORDER BY expr [DESC | ASC])
    [OVER (query_partition_ clause) ]
    ```
    
- PERCENTILE_DISC 함수
    
    PERCENTILE_DISC 함수는 이산 분포 모델에서 expr에 지정한 백분위 값에 해당하는 값을 반 환한다. expr은 0~ 1의 범위를 지정할 수 있다.
    
    ```sql
    PERCENTILE_DISC (expr) WITHIN GROUP (ORDER BY expr [DESC | ASC])
    [OVER (query_partition clause) ]
    ```
    
- MEDIAN 함수
    
    MBDIAN 함수는 연속 분포 모형을 가정한 중앙값을 반환한다. PERCENTILE_CONT (0.5) 표현식과 결과가 동일하다.
    
    ```sql
    MEDIAN (expr) [OVER(query_partition_clause)]
    ```
    

### 14.2.5 순차 함수

순차 함수는 순차에 해당하는 값을 가져오는 함수다.

- FIRST_VALUE 함수
    
    FIRST_VALUE 함수는 윈도우 첫 행의 expr을 반환한다. IGNORE NULLS 키워드를 기술하면 널이 무시된다.
    
    ```sql
    FIRST_VALUE (expr) [IGNORE NULLS) OVER (analytic_clause)
    ```
    
- LAST_VALUE 함수
    
    LAST_VALUE 함수는 윈도우 끝 행의 expr을 반환한다.
    
    ```sql
    LAST_VALUE (expr) [IGNORE NULLS] OVER (analytic_clause)
    ```
    
- NTH_VALUE 함수
    
    NTH_VALUE 함수는 윈도우 n번째 행의 measure_expr을 반환한다. FIRST는 윈도우의 첫 한, LAST는 윈도우의 끝 행부터 검색을 시작한다. 기본값은 FIRST다. NTH_VALUE 함수는 11 버전부터 사용할 수 있다.
    
    ```sql
    NTH_ VALUE (measure_expr, n) [FROM {FIRST | LAST)] [IGNORE NULLS]
     OVER (analytic_clause)
    ```
    

### 14.2.6 기타 함수

- LAG 함수
    
    LAG 함수는 현재 행에서 offset 이전 행의 value_expr을 반환한다. offset은 행 기준이며 기본 값은 1이다. default에 이전 행이 없을 경우 반환할 값을 지정할 수 있다. default의 기본값은 널이다.
    
    ```sql
    LAG (value_expr [, offset [, default]]) [IGNORE NULLS) 
    OVER ([query_partition_clause] order_by_clause)
    ```
    
- LISTAGG 함수
    
    LISTAGG 함수는 measure_expr를 order_by_clause로 정렬한 후 delimiter로 구분하여 연결 한 값을 반환한다. delimiter의 기본값은 널이다.
    
    ```sql
    LISTAGG (measure_expr l, 'delimiter'] (listagg_overflow_clause]) WITHIN GROUP (order_by_clause) [OVER query_partition_clause]
    ```