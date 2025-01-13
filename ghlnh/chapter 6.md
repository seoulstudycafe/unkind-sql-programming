# 6장 단일 행 함수

## 6.1 문자 함수

	•	CHR 함수: CHR 함수는 n에 해당하는 데이터베이스 캐릭터 셋의 문자 값을 반환한다. 문자로 입력할 수 없는 특수 문자를 입력할 때 사용할 수 있다 
`CHR (n)`

```sql
SELECT 'AB' || CHR(10) || ' C' AS c1 FROM DUAL;

C1
--
AB
 C
```

	•	LOWER 함수: LOWER 함수는 char를 소문자로 변경한다.
`LOWER (char)`

```sql
SELECT LOWER ('abc') FROM DUAL;

C1
--
abc
```

	•	UPPER 함수: UPPER 함수는 char를 대문자로 변경한다.
`UPPER (char)`

```sql
SELECT UPPER('abc') AS c1 FROM DUAL;

C1
---
ABC
```

	•	INITCAP 함수: INITCAP 함수는 char에 포함된 단어의 첫 글자는 대문자, 나머지는 소문자로 변경한다. 단어 는 공백이나 숫자와 알파벳이 아닌 문자를 기준으로 구분된다.
`INITCAP (char)`

```sql
SELECT INITCAP ('abC de') AS c1, INITCAP('abC,de') AS c2 FROM DUAL;

C3     C4
-----  -----
Abc De Abc,De
```

	•	LPAD 함수: LPAD 함수는 expr1의 길이를 좌측으로 만큼 늘린 후, 늘어난 공간을 expr 2로 반복해서 채운다. expr2의 기본값은 공백이다.
`LPAD (expr1, n [, expr2)`

```sql
SELECT LPAD('AB',3) AS c1, LPAD ('AB',1) AS c2, LPAD('AB',5,'12')AS c3 FROM DUAL;

C1   C2   C3
---  ---  ------
AB   A    121AB 
```

	•	RPAD 함수: RPAD 함수는 expr1의 길이를 우측으로 n만큼 늘린 후, 늘어난 공간을 expr2로 반복해서 채운 다. expr2의 기본값은 공백이다.
`RPAD (expr1, n [expr2])`

```sql
SELECT RPAD('AB',3) AS c1, RPAD('AB',1) AS c2, RPAD ('AB',5,'12')AS c3 FROM DUAL;

C1   C2   C3
---  ---  ------
AB   A    AB121
```

RPAD 함수는 expr1이 널이면 널을 반환한다. LPAD 함수도 마찬가지다.

	•	LTRIM 함수: LTRIM 함수는 char의 좌측부터 set에 포함되지 않은 문자를 만날 때까지 set에 포함된 문자를 제거한다. char를 한 문자씩 set과 비교한다. set의 기본값은 공백( )이다.
`LTRIM (char [, set])`

```sql
SELECT LTRIM(' A ')AS c1, LTRIM('ABC','BA')AS c2, LTRIM('ABC','BC')AS c3 FROM DUAL;

C1  C2  C3
--  --  ---
A   C   ABC
```

	•	RTRIM 함수는 char의 우측부터 set에 포함되지 않은 문자를 만날 때까지 set에 포함된 문자를 제거한다. set의 기본값은 공백이다.
`RTRIM (char [, set])`

```sql
SELECT RTRIM(' A ')AS c1, RTRIM('ABC','BA')AS c2, RTRIM('ABC','BC')AS c3 FROM DUAL;

C1  C2   C3
--  ---  ---
A   ABC  A
```

단일 행 함수는 중첩하여 사용할 수 있다. 아래 쿼리는 RTRIM 함수와 LTRIM 함수를 중첩하여 문자열 양측의 숫자 값을 제거한다.

```sql
SELECT RTRIM(LTRIM('12A34','0123456789'). '0123456789')AS c1 FROM DUAL;

C1
--
A
```

	•	TRIM 함수:TRIM 함수는 trim_source의 좌측이나 우측이나 양측에서 trim_character가 아닌 문자를 만날 때까지 trim_character를 제거한다. 위치의 기본값은 BOTH다. trim_character은 한 문자만 지정할 수 있으며 기본값은 공백이다.
`TRIM ([{{LEADING | TRAILING | BOTH} [trim_character] | trim_character} FROM] trim_source)`

```sql
SELECT TRIM ( LEADING 'A' FROM 'AAB ') AS c1, TRIM( LEADING FROM 'AAB ') AS c2
			,TRIM (TRAILING 'B' FROM 'AAB ') AS c3, TRIM(TRAILING FROM 'AAB ') AS c4
			,TRIM (    BOTH 'A' FROM 'AAB ') AS c5, TRIM(    BOTH FROM 'AAB ') AS c6
			,TRIM (         'A' FROM 'AAB ') AS c7, TRIM(              'AAB ') AS c8
FROM DUAL;

C1  C2   C3   C4   C5  C6   C7  C8
--  ---  ---  ---  --  ---  --  ---
B   AAB  AAB  AAB  B   AAB  B   AAB 
```

	•	SUBSTR 함수: SUBSTR 함수는 char를 position 위치에서 우측으로 substring_length 만큼 자른다. substring_length를 생략하면 끝까지 잘린다. position이 음수인 경우 뒤쪽에서 좌측으로 음수 만큼 이동한 위치에서 우측으로 자른다. position이 char의 길이보다 크면 널을 반환한다.
`SUBSTR (char, position [, substring_length])`

```sql
SELECT SUBSTR('123456', 4) AS c1, SUBSTR('123456', 4, 2) AS c2
			,SUBSTR('123456',-4) AS c3, SUBSTR('123456',-4, 2) AS c4
			,SUBSTR('123456', 7) AS c5
FROM DUAL;

C1   C2  C3    C4  C5
---  --  ----  --  ---
456  45  3456  34
```

	•	REPLACE 함수: REPLACE함수는 char에 포함된 search_string를 replacement_string으로 변경한다.replacement_string의 기본값은 널이다.
`REPLACE (char, search_string [, replacement_ string])`

```sql
SELECT REPLACE('ABCABC', 'AB') AS c1
			,REPLACE('ABCABC', 'AB','X') AS c2
			,REPLACE('ABCABC', 'AB','XYZ') AS c3
FROM DUAL;

C1  C2    C3
--  ---   -----
CC  XCXC  XYZCXYZC
```

```sql
WITH w1 AS (SELECT 'A B C' AS c1 FROM DUAL)
SELECT c1
     , REPLACE(c1, ' ', '!') AS c2
     , REPLACE(REPLACE(c1, ' ', '!'), '!', '|') AS c3
     , REPLACE(REPLACE(REPLACE(c1, ' ', '!'), '!', '|'), '|', '!') AS c4
     , REGEXP_REPLACE(c1, ' +', ' ') AS c5
FROM w1;

C1      C2      C3      C4      C5
------- ------- ------- ------- -----
A B C   A!B!C   A|B|C   A!B!C   A B C

```

	•	TRANSLATE 함수: TRANSLATE 함수는 expr의 문자를 from_string 문자와 대응되는 to_string 문자로 변환한다.from_string 문자와 일치하지 않는 문자는 변환하지 않는다.
`TRANSLATE (expr, from_string, to_string)`

```sql
SELECT TRANSLATE('AAABBC','AB','X') AS c1
			,TRANSLATE('AAABBC','AB','X ') AS c2
			,TRANSLATE('AAABBC','AB','XYZ') AS c3
FROM DUAL;

C1    C2     C3    
----  -----  ------
XXXC  XXX C  XXXYYC
```

TRANSLATE 함수는 1자리의 코드 값을 다른 값으로 변환할 때 사용할 수 있다. 아래 쿼리에서 c1열의 A,B는 각각 C,D로 변환된다

```sql
WITH w1 AS (SELECT 'A' AS c1 FROM DUAL UNION ALL SELECT 'B' AS c1 FROM DUAL)
SELECT c1, TRANSLATE(c1, 'AB', 'CD') AS c2
FROM w1;

C1  C2
--- ---
A   C
B   D
```

TRANSLATE 함수를 사용하면 특정값을 제거하거나 추출 할 수 있다

```sql
WITH w1 AS (SELECT 'A1B2C3' AS c1 FROM DUAL)
SELECT c1
     , TRANSLATE(c1, '1234567890', '') AS c2
     , TRANSLATE(c1, '1234567890', '1234567890') AS c3
FROM w1;

C1      C2     C3
------- ------ ---
A1B2C3  ABC    123
```

	•	ASCIl 함수: ASCII 함수는 char 첫 문자의 아스키 값을 십진수로 반환한다.
`ASCII (char)`

```sql
SELECT ASCII('A') AS c1, ASCII('BC') AS c2, CHR (ASCII('A') +1) AS c3 FROM DUAL;

C1 C2 C3
-- -- --
65 66 B
```

	•	INSTR 함수: INSTR 함수는 string의 position에서 우측으로 occurrence번째 substring의 시작 위치를 반환 한다. position과 occurrence의 기본값은 1이다.
`INSTR (string, substring [, position [, occurrence]])`
아래는 INSTR 함수를 사용한 쿼리다. 강조한 부분이 시작 위치다. c2 열처럼 Substring을 찾 을 수 없거나 c3 열처럼 position이 string의 길이보다 긴 경우 0을 반환한다. c6, c7 열처럼 position이 음수인 경우 position에서 좌측으로 substring을 검색한다

```sql
SELECT INSTR('ABABABAB', 'AB') AS c1
     , INSTR('ABABABAB', 'AC') AS c2
     , INSTR('ABABABAB', 'AB', 9) AS c3
     , INSTR('ABABABAB', 'AB', 1, 2) AS c4
     , INSTR('ABABABAB', 'AB', 3, 2) AS c5
     , INSTR('ABABABAB', 'AB', -1, 2) AS c6
     , INSTR('ABABABAB', 'AB', -3, 2) AS c7
FROM DUAL;

C1  C2  C3  C4  C5  C6  C7
--- --- --- --- --- --- ---
1   0   0   3   5   5   3
```

- LENGTH 함수: LENGTH함수는 char 함의 길이를 반환한다

`LENGTH (char)`

```sql
SELECT LENGTH ('AB') AS c1, LENGTH('AB  ')AS c2 FROM DUAL;

C1  C2
--  --
2    4
```

## 6.2 숫자함수

- ABS함수: ABS함수는 n의 절대값을 반환한다

`ABS (n)`

```sql
SELECT ABS (0)AS c1, ABS(10)AS c2, ABS(-10)AS c3 FROM DUAL;

c1  c2  c3
--  --  --
0   10  10
```

- SIGN함수: SIGN함수는 n의 부호를 반환한다. n이 양수면 1, 음수면 -1, 0이면 0을 반환한다

`SIGN (n)`

```sql
SELECT SIGN(0) AS c1, SIGN (10) AS c2, SIGN (-10) AS c3 FROM DUAL;

C1  C2  C3
--  --  --
0   1   -1
```

- ROUND(number)함수: n1을 n2자리로 반올림한다. n2가 양수면 소수부, 음수면 정수부를 반올림한다. n2의 기본값은 0이다.

`ROUND (n1[, n2])`

```sql
SELECT ROUND (15.99) AS c1, ROUND (15.59, 1) AS c2, ROUND (15.59, -1) AS c3 FROM DUAL;

C1  C2   C3
--  ---  --
16  15.6 20
```

- TRUNC(nimber)함수: n1을 n2자리로 버린다. 양수면 소수부, 음수면 정수부를 버린다. n2의 기본값은 0이다.

`TRUNC (n1 [, n2])` 

```sql
SELECT TRUNC (15.99) AS c1, TRUNC (15.59, 1) AS c2, TRUNC (15.59, -1) AS c3 FROM DUAL;

C1  C2   C3
--  ---  --
15  15.5 10
```

- CEIL: n보다 크거나 같은 정수의 최소값을 반환한다.

`CEIL (n)`

```sql
SELECT CEIL(15.5) AS c1, CEIL(16) AS c2 FROM DUAL;

C1 C2
-- --
16 16
```

- FLOOR:n보다 작거나 같은 정수의 최대값을 반환한다.

`FLOOR (n)`

```sql
SELECT FLOOR(15.5) AS c1, FLOOR(16) AS c2 FROM DUAL;

C1 C2
-- --
15 16
```

- MOD: n1을 n2로 나눈 나머지를 반환한다. n2가 0이면 n1을 반환한다. n1이나 n2가 음수면 절대값으로 계산한 값에 n1부호를 적용한다.

`MOD (n1, n2)`

```sql
SELECT MOD (11,4) AS c2, MOD (11,-4) AS c2
			,MOD (-11,4) AS c3, MOD (-11,-4) AS c4, MOD (11,0) AS c5 FROM DUAL;

C1 C2 C3 C4 C5
-- -- -- -- --
3  3  -3 -3 11
```

- REMAINDER: n1을 n2로 나눈 나머지를 반환한다. n2가 0이면 에러가 발생한다.

`REMAINDER (n1, n2)`

```sql
SELECT REMAINDER (11,4) AS c2, REMAINDER (11,-4) AS c2
			,REMAINDER (-11,4) AS c3, REMAINDER (-11,-4) AS c4 FROM DUAL;

C1 C2 C3 C4
-- -- -- --
-1 -1 1  1 
```

- POWER: n1을 n2로 거듭제곱한 값을 반환한다.

`POWER (n1, n2)`

```sql
SELECT POWER (2,10) AS c1, 107374182400 / POWER (1024,3) AS c2 FROM DUAL;

C1   C2
---  ---
1024 100
```

- BITAND: exp1, exp2의 비트 AND 연간 값을 반환한다. 비트 AND 연산은 비교되는 비트가 모두 1 이면 1이고, 그렇지 않으면 0이다.

`BITAND (expr1, expr2)`

```sql
SELECT BITAND(6,3) AS c1 FROM DUAL;

C1
--
2
```

- WIDTH_BUCKET: min_value ~ max_value의 범위에 대해 num_buckets 개의 버킷을 생성한 후, expr이 속한 버킷을 반환한다. 버킷은 반개구간으로 생성된다.

`WIDTH_BUCKET(expr, min_value, max_value, num_buckets)`

```sql
WITH w1 AS (SELECT LEVEL AS c1 FROM DUAL CONNECT BY LEVEL <=6)
SELECT c1, WIDTH_BUCKET (c1,2,6,2) AS c2 FROM w1;

C1 C2
-- --
1  0
2  1
3  1
4  2
5  2
6  3
```

위 쿼리는 아래와 같은 버킷을 생성한다. [m,n)은 m이상, n미만의 구간을 의미

| C1 | C2 | 버킷 |
| --- | --- | --- |
| 1 | 0 | 언더플로우 버킷: 0 |
| 2 | 1 | 1번 버킷: [2,4) |
| 3 | 1 | 1번 버킷 |
| 4 | 2 | 2번 버킷: [4,6) |
| 5 | 2 | 2번 버킷 |
| 6 | 3 | 오버플로우 버킷: num_buckets +1 |

## 6.3 날짜 함수

- SYSDATE: 데이터베이스 서버의 날짜 값을 반환한다. 초가 포함된 DATE 타입이 반환된다.

`SYSDATE`

```sql
SELECT SYSDATE AS c1 FROM DUAL;

C1
-------------------
2050-01-01 00:00:00
```

- SYSTIMESTAMP: 시간대가 포함된 데이터베이스 서버의 날짜 값을 반환한다. 소수점 이하 초가 포함된 TIMESTAMP WITH TIME ZONE 타입이 반환된다.

`SYSTIMESTAMP`

```sql
SELECT SYSTIMESTAMP AS c1 FROM DUAL

C1
---------------------------------------
2050-01-01 00:00:00:00.000000000 +09:00
```

- NEXT_DAY: date 이후 char에 지정된 요일에 해당하는 가장 가까운 날짜 값을 반환한다.

NEXT_DAY(date, char)

| 일 | 월 | 화 | 수 | 목 | 금 | 토 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 2 | 3 | 4 | 5 | 6 | 7 |

```sql
SELECT NEXT_DAY (DATE '2050-01-01' '2') AS c1 FROM DUAL;

C1
---------------------
2050-01-03 00:00:00
```

char는 요일 형식으로도 지정할 수 있다. 

```sql
SELECT NEXT_DAY (DATE '2050-01-01' '월') AS c1 FROM DUAL;

C1
---------------------
2050-01-03 00:00:00
```

- LAST_DAY: date가 속한 월의 월말일을 반환

`LAST_DAY(date)`

```sql
SELECT LAST_DAY (DATE '2050-02-15') AS c1 FROM DUAL;

C1
---------------------
2050-02-28-00:00:00
```

- ADD_MONTHS: date에서 integer로 지정한 월을 가감한 날짜 값을 반환한다. date가 월말일이면 결과도 월말일로 계산 된다.

`ADD_MONTHS(date, integer)`

```sql
SELECT ADD_MONTHS (DATE '2050-01-15' , -1) AS c1
			,ADD_MONTHS (DATE '2050-01-31' , 1) AS c2
			,ADD_MONTHS (DATE '2050-02-28' , 1) AS c3
FROM DUAL;

C1                    C2                   C3
-------------------   -------------------  -------------------
2049-12-15 00:00:00   2050-02-28 00:00:00  2050-03-31 00:00:00
```

월말일 지정: 월말일을 고려하지 않고 월을 가감해야 한다면 사용자 정의 함수를 사용해야 한다. 

```sql
CREATE OR REPLACE FUNCTION fnc_add_months (i_date IN DATE, i_integer IN NUMBER)
RETURN DATE
IS
    l_date DATE := ADD_MONTHS(i_date, i_integer);
BEGIN
    RETURN CASE
        WHEN TO_CHAR(i_date, 'DD') <> TO_CHAR(l_date, 'DD')
        THEN l_date + NUMTOYMINTERVAL(i_integer, 'MONTH')
        ELSE l_date
    END;
END fnc_add_months;
```

아래는 fnc_add_months함수를 사용한 쿼리다. 

```sql
SELECT fnc_add_months(DATE '2050-01-15', 1) AS c1
     , fnc_add_months(DATE '2050-01-31', 1) AS c2
     , fnc_add_months(DATE '2050-02-28', 1) AS c3
FROM DUAL;

C1                C2                C3
----------------- ----------------- -----------------
2050-02-15 00:00:00 2050-02-28 00:00:00 2050-03-28 00:00:00
```

- MONTHS_BETWEEN 함수: date1과 date2 사이의 개월수를 반환한다. date1, date2으 일자가 같거나 모두 월말일이면 정수, 그렇지 ㅇ낳으면 날짜의 차이를 31로 나눈 값을 반환한다. date2 > date1이면 음수를 반환한다.

`MONTHS_BEWEEN (date1, date2)` 

```sql
SELECT MONTHS_BETWEEN (DATE '2050-04-15', DATE '2050-01-15') AS c1
			,MONTHS_BETWEEN (DATE '2050-04-30', DATE '2050-01-31') AS c2
			,MONTHS_BETWEEN (DATE '2050-04-30', DATE '2050-01-15') AS c3
			,MONTHS_BETWEEN (DATE '2050-01-15', DATE '2050-04-30') AS c4
FROM DUAL;

C1 C2  C3  C4
-- -- --- ----
3  3  3.48 -3.48
```

- EXTRACT: expr에서 날짜 정보를 추출한다

`EXTRACT ({YEAR | MONTH| DAY |HOUR | MINUTE| SECOND} FROM expr)`

```sql
WITH w1 AS (SELECT TIMESTAMP ‘2050-01-02 12:34:56.789’ AS dt FROM DUAL)
SELECT EXTRACT (YEAR FROM dt) AS c1, EXTRACT (MONTH FROM dT) AS c2
, EXTRACT(DAY FROM dt) AS c3, EXTRACT(HOUR FROM dt) AS c4
, EXTRACT(MINUTE FROM dt) AS c5, EXTRACT(SECOND FROM dt) AS c6 FROM w1;

C1   C2 C3 C4 C5  C6
---- -- -- -- -- -------
2050 1  2  12 34  56.789
```

- ROUND: fmt를 기준으로 date를 반올림한다. fmt의 기본값은 DD다.

`ROUND (date [, fmt])` 

```sql
WITH w1 AS (SELECT TO_DATE ('20510816123131', 'YYYYMMDDHH24MISS') AS dt FROM DUAL)
SELECT ROUND (dt) AS c1, ROUND (dt, 'DD') AS c2 FROM w1;

C1                   C2
-------------------  -------------------
2051-08-17 00:00:00  2051-08-17 00:00:00
```

| 포맷요소 | 설명 | ROUND기준 |
| --- | --- | --- |
| CC | 세기 | 연도의 끝 두자리 51년 |
| YY | 년 | 7월 1일 |
| Q | 분기 | 분기의 둘째 달 16일 |
| MM | 월 | 16일 |
| DD | 일 | 12시 |
| HH | 시 | 31분 |
| MI | 분 | 31초 |

| 포맷 요소 | 일자 | ROUND 결과 |
| --- | --- | --- |
| CC | 2051-08-16 12:31:31 | 2101-01-01 00:00:00 |
| YY | 2051-08-16 12:31:31 | 2052-01-01 00:00:00 |
| Q | 2051-08-16 12:31:31 | 2051-10-01 00:00:00 |
| MM | 2051-08-16 12:31:31 | 2051-09-01 00:00:00 |
| DD | 2051-08-16 12:31:31 | 2051-08-17 00:00:00 |
| HH | 2051-08-16 12:31:31 | 2051-08-17 13:00:00 |
| MI | 2051-08-16 12:31:31 | 2051-08-17 12:32:00 |

위 포맷요소는 아래의 결과를 반환한다.

- TRUNC(date): fmt 기준으로 date를 버린다. fmt는 ROUND(date) 함수와 동일하다

`TRUNC (date [, fmt])` 

```sql
WITH w1 AS (SELECT TO_DATE ('20510816123131', 'YYYYMMDDHH24MISS') AS dt FROM DUAL)
SELECT TRUNC (dt) AS c1, TRUNC (dt, 'DD') AS c2 FROM w1;

C1                   C2
-------------------  -------------------
2051-08-16 00:00:00  2051-08-16 00:00:00
```

포맷 요소 별로 아래의 결과를 반환한다. 밑줄 친 위치에서 버려진다. MM 포맷 요소를 사용하면 월초일을 계산할 수 있다

| 포맷 요소 | 일자 | ROUND 결과 |
| --- | --- | --- |
| CC | 2051-08-16 12:31:31 | 2001-01-01 00:00:00 |
| YY | 2051-08-16 12:31:31 | 2051-01-01 00:00:00 |
| Q | 2051-08-16 12:31:31 | 2051-07-01 00:00:00 |
| MM | 2051-08-16 12:31:31 | 2051-08-01 00:00:00 |
| DD | 2051-08-16 12:31:31 | 2051-08-16 00:00:00 |
| HH | 2051-08-16 12:31:31 | 2051-08-16 12:00:00 |
| MI | 2051-08-16 12:31:31 | 2051-08-16 12:31:00 |

소수점 이하 초의 유실: DATE타입을 반환하는 날짜 수에 ‘TIMESTAMP’값을 사용하면 소수점 이하 초가 유실된다. 아래 쿼리의 C1열은 NEXT_DAT함수로 인해 소수점 이하 초가 유실된다. 소수점 이하 초를 유지하려면 C2열처럼 별도의 쵸현식을 사용해야 한다.

```sql
WITH w1 AS (SELECT TIMESTAMP '2050-01-01 23:59:59.123456789' AS dt FROM DUAL)
SELECT NEXT_DAY (dt, 2) AS c1,
       CAST (NEXT_DAY (dt, 2) AS TIMESTAMP)
       + NUMTODSINTERVAL (TO_CHAR (dt, 'FF9') * 1E-9, 'SECOND') AS c2
FROM w1;

C1                  C2
------------------- -----------------------------
2050-01-03 23:59:59 2050-01-03 23:59:59.123456789
```

## 6.4 변환 함수

데이터 변환: 사용자가 변환 함수를 통해 값의 데이터 타입을 변경하는 것을 명시적 데이터 변환이라고 한다. 값의 데이터 타입은 오라클 데이터베이스에 의해 자동으로 변환되기도 하는데 이런 변환을 암시적 데이터 변환이라고 한다. 

아래 표는 데이터 변환이 가능한 조합이다.

|  | CHAR | VARCHAR2 | DATE |  INTERVAL | NUMBER | CLOB |
| --- | --- | --- | --- | --- | --- | --- |
| CHAR |  | O | O | O | O | O |
| VARCHAR2 | O |  | O | O | O | O |
| DATE | O | O |  |  |  |  |
| INTERVAL | O | O |  |  |  |  |
| NUMBER | O | O |  |  |  |  |
| CLOB | O | O |  |  |  |  |

아래는 데이터 타입이 변환되는 우선순위다.

| 우선순위 | 데이터 타입 |
| --- | --- |
| 1 | DATE, INTERVAL |
| 2 | NUMBER |
| 3 | CHAR, VARCHAR2, CLOB |
| 4 | 기타 |
- TO_CHAR(number): expr을 fmt 형식의 문자 값으로 변환한다

`TO_CHAR (n [, fmt [, ’nlsparam’]])`

아래는 자주 사용되는 포맷 요소다.

| 포맷 요소 | 설명 |
| --- | --- |
| 0 | 앞쪽이나 뒤쪽에 0을 출력 |
| 9 | 한자리 숫자 |
| , | 구분자 |
| . | 소수점 |
| S | 부호 (양수면 +, 음수면 -) |

c1, c4열은 값이 fmt보다 커 결과가 #으로 표시된다.

```sql
SELECT TO_CHAR(12, '00') AS c1, 
       TO_CHAR(12, '000') AS c2, 
       TO_CHAR(12, '9') AS c3, 
       TO_CHAR(12, '99') AS c4, 
       TO_CHAR(12, '999') AS c5, 
       TO_CHAR(12, '9999') AS c6
FROM DUAL;

C1  C2   C3  C4  C5  C6
--  --  --  --  --  --
##  12  012  ##  12  12

```

아래는 S포맷 요소를 사용한 쿼리다. 명시적으로 부호를 표시할 때 사용할 수 있다

```sql
SELECT TO_CHAR(1, '9') AS c1, 
       TO_CHAR(-1, '9') AS c2, 
       TO_CHAR(1, 'S9') AS c3, 
       TO_CHAR(-1, 'S9') AS c4
FROM DUAL;

C1  C2  C3  C4
--  --- -- ----
1   +1  -1  -1

```

- TO_CHAR(datetime)함수: datetime값과 interval값을 fmt 형식의 문자 값으로 변환한다.

`TO_CHAR({datetime | interval} [fmt[, ‘nlsparam’]])`

아래는 자주 사용되는 포맷 요소다. TO_* 날짜 함수가 Y로 표시된 포맷 요소는 TO_DATE, TO_TIMESTAMP, TO_TIMESTAMP_TZ함수에도 사용할 수 있다.

| 포맷요소 | 설명 | 범위 | TO_*날짜함수 |
| --- | --- | --- | --- |
| -/,.;: | 문장부호 |  |  Y |
| “text” | 텍스트 |  |  Y |
| YYYY | 년 |  |  Y |
| MM | 월 | 0~12 |  Y |
| DD | 일 | 01~31 |  Y |
| HH | 시(12시간) | 01~12 |  Y |
| HH24 | 시(24시간) | 00~23 |  Y |
| MI | 분 | 00~59 |  Y |
| SS | 초 | 00~59 |  Y |
| FF[1..9] | 소수점 이하 초 |  |  Y |
| AM, PM | 오전, 오후 |  |  Y |

아래는 TO_CHAR(datetime)함수를 사용한 쿼리다.

```sql
WITH w1 AS (SELECT TO_DATE('20500102123456', 'YYYYMMDDHH24MISS') AS dt FROM DUAL)
SELECT TO_CHAR(dt, 'YYYY') AS c1
     , TO_CHAR(dt, 'YYYYMM') AS c2
     , TO_CHAR(dt, 'YYYYMMDD') AS c3
     , TO_CHAR(dt, 'YYYYMMDDHH24MISS') AS c4
     , TO_CHAR(dt, 'YYYY-MM-DD HH24:MI:SS') AS c5
     , TO_CHAR(dt, 'HH24"H" MI"M" SS"S"') AS c6
FROM w1;

C1     C2       C3        C4                C5                      C6
2050   205001   20500102  20500102123456    2050-01-02 12:34:56     12H 34M 56S
```

아래는 특정 시점 이후 기간을 표시하는 포맷 요소다.

| 포맷요소 | 설명 |  범위 | TO_* 날짜함수 |
| --- | --- | --- | --- |
| Q | 연중분기 | 1~4 |  |
| WW | 연중 주 | 1~53 |  |
| DDD | 연중 일자 | 1~365 | Y |
| W | 월중 주 | 1~5 |  |
| SSSSS | 자정 이후 초 | 0~86399 | Y |

```sql
WITH w1 AS (SELECT TO_DATE('2050041512', 'YYYYMMDDHH24') AS dt FROM DUAL)
SELECT TO_CHAR(dt, 'YYYY-Q') AS c1,
       TO_CHAR(dt, 'YYYY-W') AS c2,
       TO_CHAR(dt, 'YYYY"년"DDD"일"') AS c3,
       TO_CHAR(dt, 'YYYYMM') AS c4,
       TO_CHAR(dt, 'SSSSS') AS c5
FROM w1;

	C1      C2     C3        C4        C5
------  -------  --------  --------  -----
2050-2  2050-15  2050-105  205004-3  43200
```

아래는 월과 요일의 이름을 표시하는 포맷 요소다.

| MONTH | 설명 | TO_* 날짜 함수 |
| --- | --- | --- |
| MONTH | 월 이름 | Y |
| MON | 월 약명 | Y |
| DAY | 요일 이름 | Y |
| DY | 요일 약명 | Y |
| D | 요일 숫자 | Y |

아래는 쿼리에서 c5열을 제외한 나머지 열은 NLS파라미터 값에 따라 결과가 달라질 수 있다.

```sql
WITH w1 AS (SELECT DATE '2050-01-01' AS dt FROM DUAL)
SELECT TO_CHAR(dt, 'MON') AS c1,
       TO_CHAR(dt, 'MONTH') AS c2,
       TO_CHAR(dt, 'DY') AS c3,
       TO_CHAR(dt, 'DAY') AS c4,
       TO_CHAR(dt, 'D') AS c5
FROM w1;

C1   C2   C3     C4  C5
---  ---  -----  --  --
1월  1월  토요일  토  7
```

TO_CHAR(datetime) 함수는 반드시 포맷을 지정해야 한다. 아래 쿼리는 포맷을 지정하지 않았지만 YYYY-MM-DD HH24:MI:SS 형식으로 값이 변환된다.

```sql
SELECT TO_CHAR (date '2050-02-02') AS c1 FROM DUAL;

C1
---------------------
2050-01-01 00:00:00
```

- TO_NUMBER: fmt형식의 expr을 숫자 값으로 변환한다

`TO_NUMBER (expr [, fmt [, ‘nlsparam’]])`

아래 쿼리는 문자값에 구분자 (,)가 포함되어 있어 에러가 발생한다

```sql
SELECT TO_NUMBER ('+1,234.50') AS c1 FROM DUAL;
```

아래와 같이 포맷을 지정하면 에러가 발생하지 않는다.

```sql
SELECT TO_NUMBER ('+1,234.50', 'S999,990.00') AS c1 FROM DUAL;

C1
-------
1234.5
```

- TO_DATE: fmt형식의 char를 DATE값으로 변환한다. TO_DATE 함수도 TO_CHAR함수처럼 반드시 포맷을 지정해야 한다. 포맷을 지정하지 않으면 NLS_DATE_FORMAT 파라미터에 따라 에러가 발생할 수 있다.

`TO_DATE (char [, fmt [, ‘nlsparam’]])`

```sql
SELECT TO_DATE('2005', 'YYYY') AS c1,
       TO_DATE('200501', 'YYYYMM') AS c2,
       TO_DATE('20050102', 'YYYYMMDD') AS c3,
       TO_DATE('20050102123456', 'YYYYMMDDHH24MISS') AS c4
FROM DUAL;

C1	                C2	                 C3	                C4
------------------- ------------------- ------------------- -------------------
2005-05-01 00:00:00	2005-01-01 00:00:00	2005-01-02 00:00:00	2005-01-02 12:34:56
```

TO_DATE함수는 fmt이 정확히 일치하지 않아도 데이터가 변환된다. 

```sql
SELECT TO_DATE ('2050-1-2 3:4:5', 'YYYY-MM-DD HH24:MI:SS') AS c1 FROM DUAL;

C1
------------------
2050-01-02 03:04:05
```

FX포맷 한정자를 사용하면 fmt이 정확히 일치하는 경우에만 데이터가 변환된다. 

- FX: char, fmt이 정확히 일치해야 함 (Format eXact)
- TO_TIMESTAMP: fmt형식의 char를 TIMESTAMP값으로 변환한다.

`TO_TIMESTAMP (char [, fmt [, ‘nlsparam’]])`

```sql
SELECT TO_TIMESTAMP ('2050-01-02 12:34:56.789', 'YYYY-MM-DD HH24:MI:SS.FF3')AS c1
FROM DUAL;

C1
-----------------------------
2050-01-02 12:34:56.789000000
```

- TO_YMINTERVAL: 문자값을 YEAR TO MONTH 인터벌 값으로 변환한다.

`TO_YMINTERVAL (’{sql_format | ym_iso_format}’)`

| 포맷 | 형식 |
| --- | --- |
| sql_format | [+ | -] years-months |
| ym_iso_format | [-[P[years Y][months M] |

아래는 TO_YMINTERVAL 함수를 사용한 쿼리다. c1열을 sql_format, c2열은 ym_iso_format을 사용했다.

```sql
SELECT TO_YMINTERVAL ('1-11') AS c1, TO_YMINTERVAL ('P1Y11M') AS c2 FROM DUAL;

C1            C2
------------- -------------
+00000001-11  +000000001-11
```

- TO_DSINTERVAL: 문자값을 DAY TO SECOND 인터벌 값으로 변환한다.

`TO_DSINTERVAL (’{sql_format | ds_iso_format}’)`

| 포맷 | 형식 |
| --- | --- |
| sql_format | [+ | -]days hours:minutes:seconds[.frac_secs] |
| ds_iso_format | [-]P[days D][T[hours H][minutes M][seconds[.frac_secs]S]] |

아래는 TO_DSINTERVAL함수를 사용한 쿼리다. c1열을 sql_format, c2열은 ds_iso_format을 사용했다.

```sql
SELECT TO_DSINTERVAL('1 23:59:59.999999999') AS c1,
       TO_DSINTERVAL('P1DT23H59M59.999999999S') AS c2
FROM DUAL;

C1	                          C2
----------------------------- -----------------------------
+000000001 23:59:59.999999999	+000000001 23:59:59.999999999
```

- NUMTOYMINTERVAL: n을 YEAR TO MONTH 인터벌 값으로 변환한다

`NUMTOYMINTERVAL(n, ‘interval_unit’)`

```sql
SELECT NUMTOINTERVAL (2, 'YEAR') AS c1, NUMTOYMINTERVAL (23, 'MONTH') AS c2 FROM DUAL;

C1     C2
----   ----
+02-00 +01-11
```

- NUMTODSINTERVAL: n을 DAY TO SECOND 인터벌 값으로 변환한다

`NUMTODSINTERVAL (n, ‘interval_unit’)`

```sql
SELECT NUMTODSINTERVAL(10, 'DAY') AS c1,
       NUMTODSINTERVAL(24, 'HOUR') AS c2,
       NUMTODSINTERVAL(60, 'MINUTE') AS c3,
       NUMTODSINTERVAL(60, 'SECOND') AS c4,
       NUMTODSINTERVAL(0.1, 'SECOND') AS c5
FROM DUAL;

C1	              C2	            C3	                C4	         C5
----------------- --------------- --------------- --------------- ---------------
+10 00:0000:00.00 +01 00:00:00.00 +00 01:00:00.00 +00 00:01:00.00 +00 00:00:00.10
```

아래 쿼리 c2열은  NUMTODSINTERVAL함수를 사용하여 날짜 값의 연산 결과를 인터벌 값으로 변환했다. NUMTODSINTERVAL 함수를 사용하지 않으면 c3열 처럼 복잡한 표현식을 작성해야 한다. 

```sql
WITH w1 AS (SELECT TO_DATE('2050-01-02 12:34:56', 'YYYY-MM-DD HH24:MI:SS')
                   - TO_DATE('2050-01-01 00:00:00', 'YYYY-MM-DD HH24:MI:SS') AS c1
            FROM DUAL)
SELECT c1,
       SUBSTR(NUMTODSINTERVAL(c1, 'DAY'), 9, 11) AS c2
       , TO_CHAR(TRUNC(c1)                      ,        'FM00') || ''
       || TO_CHAR(TRUNC(MOD(c1 * 24        , 24)),        'FM00') || ':'
       || TO_CHAR(TRUNC(MOD(c1 * 24 * 60   , 60)),        'FM00') || ':'
       || TO_CHAR(TRUNC(MOD(c1 * 24 * 60 * 60, 60)),      'FM00') AS c3
FROM w1;

C1	       C2	         C3
---------- ----------- -----------
1.52425926 01 12:34:56 01 12:34:56
```

- CAST: expr을 type_name에 지정한 데이터 타입으로 변환한다. type_name은 NUMBER, DATE, TIMESTAMP, TIMESTAMP WITH TIME ZONE, TIMESTAMP WITH LOCAL TIME ZONE을 지정할 수 있다.

`CAST (expr AS type_name [, fmt [, ‘nlsparam’]])`

```sql
SELECT CAST('123' AS VARCHAR2(5)) AS c1,
       CAST('123.456' AS NUMBER(6,3)) AS c2,
       CAST('2050-01-02' AS DATE) AS c3,
       CAST('2050-01-02 12:34:56' AS TIMESTAMP(2)) AS c4,
       CAST('2050-01-02 12:34:56 +08:00' AS TIMESTAMP(2) WITH TIME ZONE) AS c5
FROM DUAL;

C1	C2	    C3	       C4	                     C5
--- ------- ---------- ----------------------- ------------------------------
123	123.456	2050-01-02	2050-01-02 12:34:56.00	2050-01-02 12:34:56.00 +08:00
```

- VALIDATE_CONVERSION: expr이 type_name에 지정한 데이터 타입으로 변환 될 수 있으면 1, 변환 될 수 없으면 0을 반환한다. expr이 NULL이면 1을 반환한다. type_name은 NUMBER, DATE, TIMESTAMP, TIMESTAMP WITH TIME ZONE, TIMESTAMP WITH LOCAL TIME ZONE, INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND를 지정할 수 있다.

`VALIDATE_CONVERSION (expr AS type_name [, fmt [, ‘nlsparam’]])`

```sql
SELECT VALIDATE_CONVERSION ('20500131' AS DATE, 'YYYYMMDD') AS c1
			,VALIDATE_CONVERSION ('20500132' AS DATE, 'YYYYMMDD') AS c2
FROM DUAL;

C1 C2
-- --
1  0
```

**변환 함수의 신규 기능**

12.2 버전부터 변환 함수에 에러가 발생할 경우 반환할 return_value을 지정할 수 있다. TO_NUMBER, TO_DATE, TO_TIMESTAMP, TO_TIMESTAMP_TZ, TO_DSINTERVAL, TO_YMINTERVAL, CAST 함수에 사용할 수 있다.

`TO_* (char [DEFAULT return_value ON CONVERSION ERROR] [, fmt [, ‘nlsparam’]])`

```sql
SELECT TO_DATE ('205012' DEFAULT '999912' ON CONVERSION ERROR, 'YYYYMM') AS c1
      ,TO_DATE (205013' DEFAULT '999912' ON CONVERSION ERROR, 'YYYYMM') AS c2
FROM DUAL;

C1                  C2
------------------- -------------------
2050-12-01 00:00:00 9999-12-01 00:00:00
```

## 6.5 널 관련 함수

- NVL: expr1이 널이 아니면 expr,  널이면 expr2를 반환

`NVL (expr1, expr2)`

```sql
SELECT NVL (1,2) AS c1, NVL (NULL, 2) AS c2 FROM DUAL;

C1 C2
-- --
1  2
```

- NVL2: expr1이 널이 아니면 expr2, 널이면 expr3 를 반환

`NVL2 (expr1, expr2, expr3)`

```sql
SELECT NVL2(1,2,3) AS c1, NVL2 (NULL, 2, 3) AS c2 FROM DUAL;

C1 C2
-- --
2  3
```

- COALESCE: 널이 아닌 첫번째 expr을 반환한다. NVL 함수의 기능을 확장한 함수

`COALESCE (expr [, expr] …)`

```sql
SELECT COALESCE (1,2,3) AS c1, COALESCE (NULL, 2,3) AS c2
      ,COALESCE (NULL, NULL, 3) AS c3
FROM DUAL;

C1  C2  C3
--  --  --
1   2   3
```

- NULLIF: expr1, expr2가 다르면 expr1, 같으면 NULL 반환

`NULLIF(expr1, expr2)`

```sql
SELECT NULLIF(1,1) AS c1, NULLIF(1,2) c2 FROM DUAL;

C1 C2
-- --
   1
```

아래 쿼리는 결합 연산자에 NULLIF를 사용했다. c2열은 comm이 널일때 널을 반환한다.

```sql
SELECT ename,comm , '$' || comm AS C1, NULLIF('$' || comm, "$") AS C2
FROM emp
WHERE empno IN (7844, 7900)

ENAME COMM C1 C2
----- ---- -- --
TURNER   0 $0 $0
JAMES      $
```

## 6.6 비교함수

- LEAST:  expr중 최소값을 반환한다. expr에 널이 입력되면 널을 반환한다

`LEAST(expr, [, expr] …)`

```sql
SELECT LEAST (1,2,3) AS c1, LEST('A', 'AB', 'ABC') AS c2, LEAST (1, NULL)M AS c3
FROM DUAL;

C1 C2 C3
-- -- --
1  A  
```

- GREATEST: expr중 최대값을 반환한다. expr에 널이 입력되면 널을 반환한다

`GREATEST(expr, [, expr] …)`

```sql
SELECT GREATEST (1,2,3) AS c1, GREATEST ('A', 'AV', 'ABC') AS c2
      ,GREATEST (1, NULL) AS c3
FROM DUAL;

C1 C2  C3
-- --- --
3  ABC
```

*LEAST함수와 GREATEST 함수는 expr의 데이터 타입이 동일해야 한다. 데이터 타입이 다른 
expr은 첫번째 expr의 데이터 타입으로 변환된다.

## 6.7 엔코딩 디코딩 함수

- DECODE: expr과 search가 일치 하면 result, 모두 일치 하지 않으면 default를 반환한다. default를 지정하지 않으면 널을 반환한다.

`DECODE (expr, search, result [, search, result] … [,default])`

```sql
SELECT DECODE (1,1,'A',2,'B','C') AS c1
      ,DECODE (2,1,'A',2,'B','C') AS c2
      ,DECODE (3,1,'A',2,'B','C') AS c3
FROM DUAL;
C1 C2 C3
-- -- --
A  B  C
```

 DECODE함수는 널을 평가 할 수 있지만 NVL2를 사용하는 편이 바람직 하다. 

첫번째 result가 널이면 VARCHAR2 타입으로 평가된다. 아래 쿼리에서 c1열은 VARCHAR2타입, c2열은 NUMBER 타입을 반환한다. 결과가 문자값은 앞쪽, 숫자값은 뒤쪽에 표시된다. 

```sql
SELECT DECODE (2, 1, NULL            ,9) AS c1
			,DECODE (2, 1, TO_NUMBER(NULL), 9) AS c2
FROM DUAL;

C1 C2
-- --
9  9
```

DECODE함수가 중첩되면 쿼리의 가독성이 나빠지고 성능이 저하된다. 

아래와 같이 결합 연산자를 사용하면 DECODE 합수의 중첩을 제거할 수 있다. 

```sql
SELECT DECODE (deptno || dname || loc, '30SALESCHICAGO', 'Y')AS c1 FROM dept
```

숫자 값이나 문자 값에 결합 연산자를 사용하면 암시적 데이터 변환이 발생할 수 있다. 결합 연산자 보다는 CASE표현식을 사용하는 편이 바람직하다.

```sql
SELECT CASE
					WHEN deptno = 30 AND dname = 'SALES' AND loc = 'CHICAGO' THEN 'Y'
				END AS c1
FROM dept;
```

- DUMP: expr의 데이터 타입과 바이트 길이를 return_fmt 형태로 반환한다.

`DUMP (expr [, return_fmt [, start_position [, length]]])`

return_fmt 은 아래와 같이 지정 가능하다. return_fmt에 1000을 더하면 캐릭터 셋이 포함된 결과를 반환한다. 

| return_fmt | 의미 | return_fmt | 의미 |
| --- | --- | --- | --- |
| 8 | 8진수 | 16 | 16진수 |
| 9 | 10진수 | 17 | 문자의 각 바이트 |

```sql
SELECT DUMP ('ABC', 16) AS c1, DUMP ('ABC', 1016, 2,2)AS c2 FROM DUAL;

C1                       C2
-----------------------  ----------------------------------------------
TypE=96, Len=3:41,42,43  Type=96 Len=3 CharacterSet=K016MSWIN949: 42,43
```

데이터 타입의 코드는 아래와 같다

| 코드 | 데이터 타입 | 코드 | 데이터 타입 |
| --- | --- | --- | --- |
| 1 | VARCHAR2 | 112 | CLOB |
| 2 | NUMBER | 113 | BLOB |
| 8 | LONG | 114 | BFILE |
| 12 | DATE | 180 | TIMESTAMP |
| 23 | RAW | 181 | TIMESTAMP WITH TIME ZONE |
| 24 | LONG RAW | 182 | INTERVAL YEAR TO MONTH |
| 69 | ROWID | 183 | INTERVAL DAY TO SECOND |
| 96 | CHAR | 208 | UROWID |
| 100 | BINARY_FLOAT | 231 | TIMESTAMP WITH LOCAL TIME ZONE |
| 101 | BINARY_DOUBLE |  |  |
- VSIZE: expr의 바이트 크기를 반환한다

`VSIZE (expr)`

```sql
SELECT VSIZE (12345) AS c1, VSIZE ('ABC') AS c2
			,VSIZE ('가나다') AS c3, VSIZE (SYSDATE) AS c4
			,VSIZE (SYSTIMESTAMP) AS c5, VSIZE (TRUNC (SYSTIMESTAMP) AS c6
FROM DUAL;

C1 C2 C3 C4 C5 C6
-- -- -- -- -- --
4  3  9  7  13  7
```

- ORA_HASH: expr에 대한 해시 값을 생성한다. max_bucket은 0~4294967295의 범위를 지정할 수 있으며 기본 값은 4294967295다. seed_value도 동일한 범위를 가지며 기본값은 0이다.

`ORA_HASH (expr [, max_bucket [, seed_value]])`

```sql
SELECT ORA_HASH (123) AS c1, ORA_HASH (123, 100) AS c2
			,ORA_HASH(123, 100, 1000) AS c3
FROM DUAL;

C1         C2 C3
---------- -- --
1865268886 78 73
```

- STANDARD_HASH: method에 지정한 해시 알고리즘으로 expr 해시 값을 생성한다. method는 SHA1, SHA256, SHA384, SHA512, MD5 알고리즘을 지정할 수 있으며 기본값은 SHA1이다.

`STANDARD_HASH (expr [, ‘mehod’])`

```sql
SELECT STANDARD_HASH (123, 'SHA256')AS c1 FROM DUAL;

C1
-----------------------------------------------------------------
A07040C0829EC3314E5318E1F060266479AA31F8BBBC1868DA42B9E608F52A09F
```

## 6.8 환경 식별자 함수

- USER: 로그인한 사용자의 이름을 반환

```sql
SEELCT USER AS c1 FROM DUAL;

C1
-----
SCOTT
```

- UID: 로그인한 사용자의 ID를 반환 (USER 뷰에서 동일한 결과 조회 가능)

```sql
SELECT UID AS c1 FROM DUAL;

C1
----
110
```

- SYS_GUID: 전역 고유 식별자를 16바이트 RAW값으로 반환한다.  (실행할 때마다 다른 값 반환)

```sql
SELECT SYS_GUID() AS c1 FROM DUAL;

C1
---------------------------
EC05C3F31C084F039E3909E13446C25E
```

- USERNV: 현재 세션에 대한 정보 반환

USERNV (’parameter’)

| 파라미터 | 설명 |
| --- | --- |
| ISDBA | DBA 권한 로그인 여부 |
| CLIENT_INFO | 클라이언트 정보 |
| TERMINAL | 클라이언트 OS식별자 |
| LANG | 언어 약명 |
| LANGUAGE | 언어 지역 및 데이터베이스 문자 집합     |

```sql
SELECT USERNV ('ISDBA') AS c1, USERNV ('CLIENT_INFO') AS c2
			,USERNV ('TERMINAL') AS c3, USERNV ('LANG') AS c4[
			,USERNV ('LANGUAGE') AS c5
FROM DUAL;

C1      C2       C3  C5
------- -------  -- -------------------------------------------
FALSE   HRJEONG  KO  KOREAN_KOREA.K016MSWIN949
```

- SYS_CONTEXT : namespace에 속한 parameter값을 반환한다. 미리 정의 되어 있는 USERENV네임 스페이스에 속한 파라미터를 살펴보자

`SYS_CONTEXT (’namespace’, ‘parameter’ [length])`

- SERVER_HOST: 클라이언트가 접속되어 있는 서버의 IP주소
- IP_ADDRESS: 클라이언트가 접속되어 있는 기기의 IP주소

```sql
SELECT SYS_CONTEXT ('USERNV', 'SERVER_HOST') AS c1
			,SYS_CONTEXT ('USERNV', 'IP_ADDRESS') AS c2
FROM DUAL;

C1         C2
---------- ---------- 
hrjeong-pc 대충 IP주소
```

아래는 데이터 베이스, 인스턴스와 관련된 파라미터다.

| 파라미터 | 설명 |
| --- | --- |
| DB_UNIQUE_NAME | 데이터 베이스 이름 (DB_UNIQUE_NAME 초기화 파라미터) |
| DB_NAME | 데이터 베이스 이름 (DB_NAME 초기화 파라미터) |
| DB_DOMAIN | 데이터베이스 도메인 (DB_DOMAIN 초기화 파라미터) |
| INSTANCE | 인스턴스 번호 |
| INSTANCE_NAME | 인스턴스 이름 |

```sql
SELECT SYS_CONTEXT ('USERNV', 'DB_UNIQUE_NAME') AS c1
			,SYS_CONTEXT ('USERNV', 'DB_NAME') AS c2
			,SYS_CONTEXT ('USERNV', 'DB_DOMAIN') AS c3
			,SYS_CONTEXT ('USERNV', 'INSTANCE') AS c4
			,SYS_CONTEXT ('USERNV', 'INSTANCE_NAME') AS c5
FROM DUAL;

C1       C2       C3 C4 C5
-------  -------- -- -- --------
ora12cr  ora12cr2    1  ora12cr2
```

아래는 클라이언트와 관련된 파라미터다.

| 파라미터 | 설명 |
| --- | --- |
| HOST | 클라이언트 호스트 |
| OS_USER | 클라이언트  OS 사용자 |
| TERMINAL | 클라이언트 OS 식별자 |
| CLIENT_PROGRAM_NAME | 클라이언트 프로그램 |

```sql
SELECT SYS_CONTEXT ('USERNV', 'HOST') AS c1
			,SYS_CONTEXT ('USERNV', 'OS_USER') AS c2
			,SYS_CONTEXT ('USERNV', 'TERMINAL') AS c3
			,SYS_CONTEXT ('USERNV', 'CLIENT_PROGRAM_NAME') AS c4
FROM DUAL;

C1                    C2                  C3          C4
-------------------   ------------------  ----------  -----------
WORKGROUP\HRJEONG-PC  HRJEONG-PC\HRJEONG  HRJEONG-PC  sqlplus.exe
```

아래는 세션과 관련된 파라미터다.

| 파라미터 | 설명 |
| --- | --- |
| SID | 세션 ID |
| SESSION_USER | 세션 사용자 (로그인 사용자) |
| CURRENT_USER | 현재 권한이 활성화된 사용자 |
| CURRENT_SCHEMA | 현재 권한이 활성화 된 스키마 |

```sql
SELECT SYS_CONTEXT ('USERNV', 'SID') AS c1
			,SYS_CONTEXT ('USERNV', 'SESSION_USER') AS c2
			,SYS_CONTEXT ('USERNV', 'CURRENT_USER') AS c3
			,SYS_CONTEXT ('USERNV', 'CURRENT_SCHEMA') AS c4
FROM DUAL;

C1   C2    C3    C4
---- ----- ----- -----
110  SCOTT SCOTT SCOTT
```

아래는 언어, NLS와 관련된 파라미터다.

| 파라미터 | 설명 |
| --- | --- |
| LANG | 언어 약명 |
| LANGUAGE | 언어와 지역, 데이터베이스 캐릭터 셋 |
| NLS_CALAENDAR | 달력 |
| NLS_CURRENCY | 통화 |
| NLS_DATE_FORMAT | 날짜 형식 |
| NLS_DATE_LANGUAGE | 날짜 언어 |
| NLS_SORT | 언어 정렬 기준 |
| NLS_TERRITORY | 지역 |

```sql
SELECT SYS_CONTEXT ('USERNV', 'LANG') AS c1
			,SYS_CONTEXT ('USERNV', 'LANGUAGE') AS c2
			,SYS_CONTEXT ('USERNV', 'NLS_CALAENDAR') AS c3
			,SYS_CONTEXT ('USERNV', 'NLS_CURRENCY') AS c4
			,SYS_CONTEXT ('USERNV', 'NLS_DATE_FORMAT') AS c5
			,SYS_CONTEXT ('USERNV', 'NLS_DATE_LANGUAGE') AS c6
			,SYS_CONTEXT ('USERNV', 'NLS_SORT') AS c7
			,SYS_CONTEXT ('USERNV', 'NLS_TERRITORY') AS c8
FROM DUAL;

C1   C2                       C3       C4 C5                    C6     C7     C8
-- -------------------------- --------- - --------------------- ------ ------ -----
KO  KOREAN_KOREA.K016MSWIN949 GREGORIAN ₩ YYYY-MM-DD HH24:MI:SS KOREAN BINARY KOREA
```

아래는 서비스 ,프로그램, 클라이언트와 관련된 파라미터다.

| 파라미터 | 설명 | 설정 |
| --- | --- | --- |
| SERVICE_NAME | 서비스 이름 | 리스너 |
| MODULE | 프로그램 모듈 | DBMS_APPLICATION_INFO |
| ACTION | 프로그램 모듈의 액션 | DBMS_APPLICATION_INFO |
| CLIENT_INFO | 클라이언트 정보 | DBMS_APPLICATION_INFO |
| CLIENT_IDENTIFIER | 클라이언트 식별자 | DBMS_SESSION |

```sql
SELECT SYS_CONTEXT ('USERNV', 'SERVICE_NAME') AS c1
			,SYS_CONTEXT ('USERNV', 'MODULE') AS c2
			,SYS_CONTEXT ('USERNV', 'ACTION') AS c3
			,SYS_CONTEXT ('USERNV', 'CLIENT_INFO') AS c4
			,SYS_CONTEXT ('USERNV', 'CLIENT_IDENTIFIER') AS c5
FROM DUAL;

C1       C3        C3 C4 C5
-------- --------- -- -- --
ora12cr2  SQL*PLUS
```

- V$SQLFN_*뷰
    - **V$SQLFN_METADATA** 뷰에서는 SQL 함수에 대한 정보를 조회할 수 있다.
    
    ```sql
    SELECT func_id, name, minargs, maxargs, datatype, version
    FROM v$sqlfn_metadata;
    
    FUNC_ID  NAME  MINARGS  MAXARGS  DATATYPE  VERSION
    -------  ----  -------  -------  --------  -------
    35       CHR   1        1        NUMERIC   V6 Oracle
    ```
    
    - **V$SQLFN_ARG_METADATA** 뷰에서는 SQL 함수의 인수에 대한 정보를 조회할 수 있다.
    
    ```sql
    SELECT func_id, argnum, datatype 
    FROM v$sqlfn_arg_metadata;
    
    FUNC_ID  ARGNUM  DATATYPE
    -------  ------  --------
    35       1       NUMERIC
    ```