# 단일 행 함수
## 문자 함수
### CHR (n)
- n에 해당하는 데이터베이스 캐릭터 셋의 문자 값 반환
- 개행 문자는 n = 10

> 실험. 개행 문자 `\n`
- C, javascript 와 달리 `\n` 처럼 이스케이프 문자를 인식하지 못함
    ```oracle 
    SELECT 'AB' || '\n' || 'C' FROM dual; --'AB\nC'
    ``` 
- 따라서 개행 문자 사용하려면 반드시 `CHR(10)` 써야 함.

### LOWER / UPPER / INITCAP 
### LPAD, RPAD
- L, R: 방향
- PAD: Padding (채우기)
### LTRIM, RTRIM, TRIM
- `TRIM` 의 경우 left 는 `LEADING`, right 는 `TRAILING` 키워드로 공백 또는 타겟 문자를 제거한다.
  ```oracle
  SELECT TRIM( LEADING 'A' FROM 'AAB ') as c1, TRIM( LEADING FROM 'AAB ') as c2
  FROM dual;
  -- c1 = 'B ', c2 = 'AAB'
  ```
- 기본값은 `BOTH`이다. 
  
### SUBSTR
`SUBSTR(char, position [, substring_length])` 
- position 은 index 와 달리 1부터 시작한다.
- 만약 음수인 경우 javascript 에서 처럼 문자열 끝에서부터 시작한다.
- substring_length 는 자르는 길이.

### REPLACE
- 정규식 활용하는 `REGEXP_REPLACE` 도 있다.

### TRANSLATE
`TRANSLATE(expr, from_string, to_string`
- from_string 과 to_string 문자열의 각 자리에 위치한 문자를 1:1 매칭하여 번역.
- from_string 의 문자 중 to_string 과 매치되지 않으면 NULL 로 취급되어 사라진다.
- to_string 중 from_string 과 매치되지 않으면 무시된다. 변환이 이루어지지 않는다.

### ASCII
- 문자의 아스키 값을 십진수로 반환

### INSTR
- 동작이 복잡하므로 필요할 때 다시 참고하자
- 기본적으로 문자열에서 반복되는 부분 문자열의 위치를 찾는 함수

## 숫자 함수
### ABS
- 절대 값
### SIGN
- n의 부호. 양수면 1, 음수면 -1, 0은 0
### ROUND, TRUNC
- `operator (n1 [,n2])`
- `ROUND`: 반올림, `TRUNC`: 버림, 
- n1의 소수점으로부터 n2만큼 이동한 위치에서 반올림 또는 버림. 즉 n2가 1이면 소수점 첫번째 자리부터가 아니라, 소수점이 오른쪽으로 한 칸 이동한 뒤 그 뒤의 소수점부터 operand 라고 생각

### CEIL, FLOOR
- `CEIL`: n보다 크거나 같은 최소 정수
- `FLOOR`: n보다 작거나 같은 최대 정수

### MOD, REMAINDER
- `operator (n1, n2)`
- 둘 다 나머지 반환이나 내부 동작이 매우 다르다.
  - `MOD`: $$n1 - (n2 * FLOOR (n1 / n2))$$
  - `REMAINDER`: $$n1 - (n2 * ROUND (n1 / n2))$$
- 직관적으로 이해하자면
  - 일반적은 모듈러 연산은 `MOD`. 
  - `REMAINDER`는 몫을 round 처리하기 때문에 더 가까운 정수 몫을 가정하였을 때의 나머지라고 생각하기.
    - 예를 들어, `REMAINDER(11, 4)`는, 2.xx가 2.5보다 크므로 3이랑 가깝다 처리해서 11 - 12에 해당하는 값 -1 이 나온다.

### POWER
- 거듭제곱

### BITAND
- 비트 AND 연산 값

### WIDTH_BUCKET
- 데이터의 폭을 bucket 수 만큼 나누어 넣고, 특정 값이 속해있는 bucket 넘버를 리턴한다.
- `WIDTH_BUCKET(target, min, max, the_number_of_buckets)`
```oracle
SELECT WIDTH_BUCKET(10, 1, 10, 3) from dual; -- 4
```

## 날짜 함수
### SYSDATE, SYSTIMESTAMP
### NEXT_DAY(date, char)
- date 이후 char 요일에 해당하는 가장 가까운 date
### LAST_DAY(date)
- date가 속한 월의 말일
### ADD_MONTHS(date, integer)
- date 가 말일인 경우 더해진 date 로 말일로 계산된다.
### MONTHS_BETWEEN(date1, date2)
- date 간 간격 월의 수 리턴. 일 수의 차이는 31로 나누어 계산.
- 말일 간의 일수 차이는 0.
### EXTRACT
- 추출할 단위 키워드 ( `YEAR` ) + FROM  ---
### ROUND & TRUNC

## 변환 함수
### TO_CHAR(number)
- formatter
  - 0: 앞쪽이나 뒷쪽에 0 출력
  - 9: 한 자리 숫자
  - , | G: 구분자 
  - . | D: 소수점
  - S: 부호 ( '+' | '-' )
  ```oracle
  SELECT TO_CHAR(1234.5, '999G999G999D00') from dual -- 1,234.50
  ```
- 이 외에도 통화 관련 포맷 요소도 있음.
  
### TO_CHAR(datetime)
- formatter 엄청 많으니 필요할 때 찾아보자

### TO_NUMBER(formatted, formatter) / TO_DATE / TO_TIMESTAMP
- 위의 formatter 로 format 된 형태의 문자열을 원본 숫자로 되돌림

### TO_YMINTERVAL / TO_DSINTERVAL
- `YM`: YEAR TO MONTH, `DS`: DAY TO SECOND
- 위의 formatted -> origin 처럼 formatted 된 문자열을 interval 값으로 돌리는 함수

### NUMTOYMINTERVAL / NUMTODSINTERVAL
- num to YEAR TO MONTH interval / DAY TO SECOND interval

### CAST
- `AS` 키워드와 함께 타입 변형

### VALIDATE_CONVERSION
- `AS` 키워드와 함께 타입 변형이 가능한지 1, 0 으로 리턴

## NULL 관련 함수
### NVL(a, b), NVL2(a, b, c)
- a가 널이 아니면 a, 널이면 b 리턴
- a가 널이 아니면 b, 널이면 c 리턴

### COALESCE(a, b, c, ...)
- `NVL`을 확장. 순서대로 null 값이 아니면 해당 값 리턴
- `NVL`도 이와 같이 동작하는 것처럼 보이나, `COALESCE`는 뒤의 인수를 평가하지 않는 반면 `NVL`은 평가한다. 따라서 에러나 부하가 발생할 수 있다.

### NULLIF(a, b)
- a와 b가 다르면 a, 같으면 null

## 비교 함수
### LEAST, GREATEST
- 동일한 데이터 타입을 가진 변수들 중 함수 명에 어울리는 최소 최대 값 리턴
```oracle
SELECT GREATEST('A', 'AB', 'ABC') FROM dual; -- 'ABC'
```

## 인코딩, 디코딩 함수
### DECODE
- `DECODE(target, search, result [, search, result]... [, default]`
- target 과 search 가 일치하면 result 를 반환. 
- search, result 쌍이 쭉 이어질 수 있음.
- 일치하는 게 없다면 default 를 반환
- 따라서 result 와 default 의 타입은 첫번째 result 타입과 일치해야 한다.
- DECODE 함수의 중첩은 성능 저하 이슈.

### DUMP
- 인수 값의 데이터 타입과 바이트 길이를 리턴한다.
- 동작이 생소해 필요할 때 다시 보자

### VSIZE
- 바이트 크기 반환

### ORA_HASH / STANDARD_HASH
- 해시 함수. `STAANDARD_HASH`는 알고리즘을 지정할 수 있다. 기본은 SHA1.
