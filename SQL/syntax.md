1. 세미콜론은 안붙여도 되지만 가급적 붙일 것.  
2. NULL은 없는 것을 표현하는 값이다. Allow null을 해제함으로써 null값을 채우지 못하게 할 수도 있다.  

---

```sql
SELECT <항목명> FROM <table명>;




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


