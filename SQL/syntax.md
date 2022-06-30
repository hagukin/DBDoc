1. 세미콜론은 안붙여도 되지만 가급적 붙일 것.  
2. NULL은 없는 것을 표현하는 값이다. Allow null을 해제함으로써 null값을 채우지 못하게 할 수도 있다.  
3. .sql 파일 첫줄에 USE 키워드를 사용해 사용할 데이터베이스 지정 가능.

---
### SELECT <항목명> FROM <table명>;
```sql
-- 항목명에 *을 입력하면 모든 항목을 다 불러온다
SELECT * FROM <table 명>;


-- 불러올 항목의 이름을 변경해서(DB를 수정하는 건 아님, 가져올 때만 다른 이름으로 가져오는 것임) 가져올 수 있다
-- 이렇게 하면 nameFirst 항목명은 name이라는 항목명으로 변경되어 출력됨.
SELECT nameFirst AS name, birthYear FROM players;


-- 특정 조건을 지정할 수 있음.
-- 같을 조건 (==이 아니라 =이다)
SELECT birthYear, nameFirst FROM players WHERE birthYear = 1922;

-- 다를 조건
SELECT birthYear, nameFirst FROM players WHERE birthYear != 1922;

-- null은 IS/IS NOT 사용
SELECT * FROM players WHERE deathYear IS NULL;
SELECT * FROM players WHERE deathYear IS NOT NULL;

-- AND, OR
SELECT birthCountry, nameFirst FROM players WHERE birthYear = 1922 AND birthCountry = 'USA';
SELECT birthCountry, nameFirst FROM players WHERE birthYear = 1922 OR birthCountry = 'USA';
-- AND문의 우선순위가 OR보다 높다! 괄호로 감싸서 명시적으로 우선순위를 지정할 수 있다.
SELECT birthCountry, nameFirst FROM players WHERE (birthYear = 1922 OR birthCountry = 'USA') AND nameFirst = 'Mike';

-- 패턴 매칭
-- %: 임의의 문자열, _:임의의 문자
-- e.g. 나라 이름이 U로 시작하는 국적의 플레이어 찾기
SELECT * FROM players WHERE birthCity LIKE 'U%'
-- => USA, UNITED KINGDOM, ...
SELECT * FROM players WHERE birthCity LIKE 'U__'
-- => USA
```

---  
### ORDER BY, TOP
```sql
-- 년도순으로 정렬 (기본값 ASC 오름차)
SELECT *
FROM players
ORDER BY birthYear;

-- null 값들을 제외하고 정렬
-- 참고: null값은 SQL 표준에 어떻게 처리할 지 정의되어있지 않다. -> SQL마다 약간씩 다르게 동작할 수 있다.
SELECT *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear;

-- 내림차순
SELECT *
FROM players
ORDER BY birthYear DESC;

-- 여러 항목들을 기준으로 정렬
SELECT *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear DESC, birthMonth DESC, birthDay DESC;

-- TOP (SQL 표준은 아니지만 TSQL에서는 사용 가능한 문법. MYSQL에서는 다르다. 비슷한 기능은 존재함.)
-- 상위 10개만 표시
SELECT TOP 10 *
FROM players
ORDER BY birthYear DESC;

-- 상위 1퍼센트만 표시
SELECT TOP 1 PERCENT *
FROM players
ORDER BY birthYear DESC;

-- TOP의 단점: 100~200위를 보여주고 싶을 경우 TOP만을 이용해서는 불가능하다.
-- 해결법: OFFSET 사용
-- 쿼리 의미: 100 줄을 뛰어넘고 그다음 100개를 추출하시오.
SELECT 10 *
FROM players
ORDER BY birthYear DESC
OFFSET 100 ROWS FETCH NEXT 100 ROWS ONLY;
```

---  

### 연산자들
```sql
-- 간단한 사칙연산 및 모듈로 연산을 지원한다.
-- 예제:
SELECT 50 - 30;
-- 출력: 
-- 20이 들어있는 테이블


-- 테이블에서 연산한 값을 특정 열에 가져오기
-- 예시: 한국 나이 80세 이하의 players들의 한국나이를 오름차순으로 정렬해 추출
-- 잘못된 풀이
SELECT 2022 - birthYear AS koreanAge
FROM players
WHERE birthYear IS NOT NULL AND koreanAge <= 80
ORDER BY koreanAge;
-- 틀린 이유: SQL은 영어 문법과 마찬가지의 순서로 실행된다.
-- 즉 FROM, WHERE, SELECT, ORDER BY 순으로 실행되는데, koreanAge는 SELECT에서 define되었으므로 WHERE에서 에러가 발생한다.

-- 올바른 풀이
SELECT 2022 - birthYear AS koreanAge
FROM players
WHERE birthYear IS NOT NULL AND 2022 - birthYear <= 80
ORDER BY koreanAge;
-- 그냥 koreanAge를 한 번 더 하드코딩해서 바꿔주면 된다.


-- 기타 유용한 함수들
SELECT ROUND(3.1415926535, 3);
SELECT POW(2,3);
SELECT COS(0);


-- 주의: NULL 연산
SELECT 20 - NULL;
-- 결과: NULL
-- 이유: NULL은 존재하지 않는 값이므로 NULL과 연산하면 다 NULL만 나온다.

-- 주의2: 부동소수점 연산
SELECT 3 / 2;
-- 결과: 1

SELECT 3.0 / 2;
-- 결과: 1.5
```

---  

### 문자열
```sql
-- 문자열은 작은따옴표로 표현한다.
SELECT 'Hello world';


-- 한국어의 경우 유니코드를 사용해야 하므로 앞에 N을 붙이자.
SELECT N'헬로 월드';


-- 문자열 합치기
-- 표준
SELECT CONCAT('Hello', ' Wor', 'ld');
-- TSQL
SELECT 'Hello' + ' World';


-- SUBSTR
SELECT SUBSTRING('20200425', 1, 4);
-- 결과: 2020
-- 주의! 시작이 0이 아니라 1이다.


-- TRIM
SELECT TRIM('           HELLO');
-- 결과: HELLO


-- 응용: 성과 이름 합쳐서 fullName이란 값으로 출력하기
SELECT nameFirst + ' ' + nameLast AS fullName
FROM players;
```

