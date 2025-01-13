# ORDER BY 절
## 기본 문법
```oracle
ORDER BY { expr | position | column alias } [ ASC | DESC ] [ NULL FIRST | NULL LAST ] & ...
```
- NULL 값의 위치를 조정할 수 있다. 기본적으로 오름차순은 NULL LAST.
- position은 SELECT 된 순서를 지정하는 것. 1부터 시작.

## 조건 정렬
### DECODE
```oracle
SELECT job, sal FROM emp
ORDER BY DECODE (job, 'A', 1, 'B', 2), sal;
```

```oracle
SELECT no, sal, comm FROM emp
ORDER BY no, DECODE(no, 10, sal) DESC, DECODE(no, 30, comm) sal DESC
```

- no 가 10이 아닌 경우 DECODE 함수는 NULL을 리턴.   
  ORDER BY 절에 null이 들어오면 무시되므로 해당 기준으로 정렬이 이루어지지 않는다.

# 집계 함수
## COUNT
- COUNT (DISTINCT expr) 이면 고유한 값만 셀 수 있다.
- 만약 다중 행의 특정 컬럼이 모드 NULL인 경우, 그 컬럼의 수를 세려면? COUNT(*)
- COUNT(컬럼명)이 들어가면 NULL인 로우는 세지 않음.

## SUM
- nullable 한 컬럼의 경우 NVL 함수 잘 사용하기

## AVERAGE

## 통계 함수들
- STDDEV: 표준 편차
- VARIANCE: 분산
- STATS_MODE: 최빈값
- MEDIAN: 중앙값

## RANK
```oracle
RANK( expr, expr, ...) WITH IN GROUP (ORDER BY expr, expr, ...)
```
- ORDER BY를 적용한 컬럼 중 각 컬럼과 일치하는 값의 순위를 리턴.
- `DENSE_RANK()`: 동순위를 증가시키지 않고 하나의 집단으로 생각하여 순위를 매김
- `RANK`의 경우 1, 2, 2, 4, 4, 4, 7 이라면 `DENSE_RANK`의 경우 1, 2, 2, 3, 3, 3, 4 로 순위가 매겨짐.

## CUME_DIST
- 누적 분포 값을 리턴.

## PERCENT_RANK
- 백분위 순위 값을 리턴.

## LISTAGG
- 정렬된 값을 delimiter 로 구분한 채로 연결하여 리턴.

# GROUP BY & HAVING
- GROUP BY는 "행 그룹"울 만들고 HAVING은 행 그룹에 조건을 적용하는 것.

## ROLLUP
- 중간 기준점 마다 누적 연산

## CUBE
- `ROLLUP`과 기준점 별 누적 연산은 똑같은데,   
   ROLLUP은 위계적인 반면 CUBE는 컬럼의 모든 조합을 계산해서 출력 

## GROUPING SETS
- TBU

## 열 조합
```oracle
GROUP BY ROLLUP ((colA, colB))
```

## HAVING 