# SELECT

---

# DATE_FORMAT

`MEMBER_PROFILE`테이블에서 생일이 3월인 여성 회원의 ID, 이름, 성별, 생년월일을 조회하는 SQL문을 작성해주세요. 이때 전화번호가 NULL인 경우는 출력대상에서 제외시켜 주시고, 결과는 회원ID를 기준으로 오름차순 정렬해주세요.

```sql
SELECT MEMBER_ID, MEMBER_NAME, GENDER, DATE_FORMAT(DATE_OF_BIRTH,'%Y-%m-%d')
    FROM MEMBER_PROFILE
    WHERE MONTH(DATE_OF_BIRTH) = 3
        AND GENDER = 'W'
        AND TLNO IS NOT NULL 
    ORDER BY MEMBER_ID ASC;

# DATE_FORMAT(변수,'형식')
-- 근데 이건 DATE 형식으로 저장돼있어야 됨 

```

# ORDER BY

`FIRST_HALF`테이블은 아이스크림 가게의 상반기 주문 정보를 담은 테이블입니다.`FIRST_HALF` 테이블 구조는 다음과 같으며, `SHIPMENT_ID`, `FLAVOR`, `TOTAL_ORDER`는 각각 아이스크림 공장에서 아이스크림 가게까지의 출하 번호, 아이스크림 맛, 상반기 아이스크림 총주문량을 나타냅니다.

```sql
SELECT FLAVOR
    FROM FIRST_HALF
    ORDER BY total_order desc, shipment_id asc;

# ORDER BY 1순위, 2순위; 
```

# JOIN - ON

상반기 아이스크림 총주문량이 3,000보다 높으면서 아이스크림의 주 성분이 과일인 아이스크림의 맛을 총주문량이 큰 순서대로 조회하는 SQL 문을 작성해주세요.

```sql
SELECT F.FLAVOR
    FROM first_half as F
  JOIN icecream_info as I 
    ON F.flavor = I.flavor
    WHERE F.total_order > 3000
       AND I.ingredient_type = 'fruit_based'
    ORDER BY F.total_order desc;

-- JOIN ON 을 빼먹지 말 것 ... 
```

# ROUND

`CAR_RENTAL_COMPANY_CAR`
 테이블에서 자동차 종류가 'SUV'인 자동차들의 평균 일일 대여 요금을 출력하는 SQL문을 작성해주세요. 이때 평균 일일 대여 요금은 소수 첫 번째 자리에서 반올림하고, 컬럼명은 `AVERAGE_FEE`
 로 지정해주세요.

```sql
SELECT ROUND(AVG(daily_fee)) AS AVERAGE_FEE
    FROM car_rental_company_car
    WHERE car_type = 'SUV' ;

-- ROUND(값,반올림할 위치)
-- 1.01234 (위치) 
```

# LIKE

`FOOD_FACTORY`
 테이블에서 강원도에 위치한 식품공장의 공장 ID, 공장 이름, 주소를 조회하는 SQL문을 작성해주세요. 이때 결과는 공장 ID를 기준으로 오름차순 정렬해주세요.

```sql
-- SELECT factory_id factory_name address
-- ORDER BY factory_id asc

-- 강원도에 위치한!!!!!!!!!!!!!

SELECT factory_id , factory_name ,address
    FROM food_factory
    WHERE address LIKE '강원도%'
    ORDER BY factory_id asc;

-- LIKE / NOT LIKE
# '_a__'
# '%a%'
```

# UNION ALL

`ONLINE_SALE`테이블과 `OFFLINE_SALE` 테이블에서 2022년 3월의 오프라인/온라인 상품 판매 데이터의 판매 날짜, 상품ID, 유저ID, 판매량을 출력하는 SQL문을 작성해주세요. `OFFLINE_SALE`
 테이블의 판매 데이터의 `USER_ID` 값은 NULL 로 표시해주세요. 결과는 판매일을 기준으로 오름차순 정렬해주시고 판매일이 같다면 상품 ID를 기준으로 오름차순, 상품ID까지 같다면 유저 ID를 기준으로 오름차순 정렬해주세요.

```sql
SELECT  date_format(S.sales_date,  '%Y-%m-%d') AS sales_date, product_id, user_id, sales_amount 
    FROM
        (SELECT sales_date, product_id, user_id, sales_amount
            FROM online_sale
                UNION ALL
        SELECT sales_date, product_id, NULL AS user_id, sales_amount
            FROM offline_sale) S 
    WHERE DATE_FORMAT(S.sales_date, '%Y-%m') = '2022-03'
    ORDER BY sales_date ASC, product_id ASC, user_id ASC;
```

`REST_INFO`와 `REST_REVIEW` 테이블에서 서울에 위치한 식당들의 식당 ID, 식당 이름, 음식 종류, 즐겨찾기수, 주소, 리뷰 평균 점수를 조회하는 SQL문을 작성해주세요. 이때 리뷰 평균점수는 소수점 세 번째 자리에서 반올림 해주시고 결과는 평균점수를 기준으로 내림차순 정렬해주시고, 평균점수가 같다면 즐겨찾기수를 기준으로 내림차순 정렬해주세요.

```sql
# 서울 위치
# SELECT  식당 ID, 식당 이름, 음식 종류, 즐겨찾기수, 주소, ROUND( 리뷰 평균 점수, 2)
# ORDER BY AVG(리뷰 평균 점수) DESC, 즐겨찾기수 ASC; 

SELECT I.rest_id , I.rest_name , I.food_type , I.favorites , I.address , ROUND(AVG(R.review_score),2 ) AS SCORE
    FROM REST_INFO I
    JOIN REST_REVIEW R
    ON I.rest_id = R.rest_id 
    WHERE address LIKE '서울%'
    GROUP BY rest_id
    ORDER BY score DESC , I.favorites DESC;
```

# JOIN

## RANK

- RANK()  OVER(ORDER BY salary DESC)
    - 1 2 2 4 4 6
- DENSE_RANK() OVER()
    - 1  2 3 4 4 4 5
- ROW_NUMBER() OVER()
    - 1 2 3 4 5 6 7 8

```sql
# 리뷰를 가장 많이 작성한 회원의 리뷰 조회
# 회원 이름, 리뷰 텍스트, 리뷰 작성일
# ORDER BY 리뷰작성일 ASC, 리뷰텍스트 ASC 

SELECT I.member_name , R.review_text, DATE_FORMAT(R.review_date,'%Y-%m-%d') AS review_date
    FROM member_profile I
    JOIN rest_review R
    ON I.member_id = R.member_id
    WHERE I.member_id = 
            (SELECT member_id
                FROM (
                SELECT member_id , count(member_id) AS cnt
                FROM rest_review
                GROUP BY member_id
                ORDER BY cnt DESC
                LIMIT 1
                ) AS C)
    ORDER BY R.review_date ASC , R.review_text ASC
```

7월 아이스크림 총 주문량과 상반기의 아이스크림 총 주문량을 더한 값이 큰 순서대로 상위 3개의 맛을 조회하는 SQL 문을 작성해주세요.

```sql
-- first_half  => flavor
-- 같은 맛, 다른 출하 번호
-- 7월 총 주문량 + 상반기 총 주문량 DESC 
-- SELECT flavor 
-- 상위 3개의 맛

SELECT H.flavor 
        # , SUM ( H.total_order + J.total_order) AS total_order
    FROM first_half H
    JOIN (
# 같은 맛 합친 서브쿼리 
        SELECT flavor, SUM(total_order) AS total_order
            FROM july 
            GROUP BY flavor
         ) J
    ON H.flavor = J.flavor
    GROUP BY H.flavor
    ORDER BY SUM ( H.total_order + J.total_order) DESC
    LIMIT 3;
    
  -- 다른 답   
SELECT JU.FLAVOR
  FROM JULY JU, FIRST_HALF FI
  WHERE JU.FLAVOR = FI.FLAVOR
  GROUP BY JU.FLAVOR
  ORDER BY SUM(JU.TOTAL_ORDER) + SUM(FI.TOTAL_ORDER) DESC
  LIMIT 3

--union 
SELECT FLAVOR
	FROM (
	    SELECT FLAVOR, TOTAL_ORDER FROM FIRST_HALF
	    UNION
	    SELECT FLAVOR, TOTAL_ORDER FROM JULY
	) A
	GROUP BY FLAVOR
	ORDER BY SUM(TOTAL_ORDER) DESC
	LIMIT 3
```

- 보호소에 들어올 당시에는 중성화되지 않았지만, 보호소를 나갈 당시에는 중성화된 동물의 아이디와 생물 종, 이름을 조회하는 아이디 순으로 조회하는 SQL 문을 작성해주세요.

```sql
SELECT I.animal_id, I.animal_type, I.name
    FROM animal_ins I
    JOIN animal_outs O
    ON I.animal_id = O.animal_id 
    WHERE I.sex_upon_intake LIKE 'Intact%'
        AND O.sex_upon_outcome NOT LIKE 'Intact%'
```

- 천재지변으로 인해 일부 데이터가 유실되었습니다. 입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름을 ID 순으로 조회하는 SQL문을 작성해주세요.

```sql
SELECT animal_id , name
    FROM animal_outs 
    WHERE animal_id NOT IN (
        SELECT O.animal_id 
            FROM animal_outs O
            JOIN animal_ins I 
            ON O.animal_id = I.animal_id 
        )
    ;

-- NOT IN 도 가능하다 ...
```

- 관리자의 실수로 일부 동물의 입양일이 잘못 입력되었습니다. 보호 시작일보다 입양일이 더 빠른 동물의 아이디와 이름을 조회하는 SQL문을 작성해주세요. 이때 결과는 보호 시작일이 빠른 순으로 조회해야합니다.

```sql
# 보호 시작일 > 입양일
# SELECT animal_id, name
# ORDER BY I.datetime ASC;

SELECT I.animal_id, I.name 
    FROM animal_ins I
    JOIN animal_outs O
    ON I.animal_id = O.animal_id 
    WHERE I.datetime > O.datetime
    ORDER BY I.datetime ASC;

-- date 비교 : 최근이 더 크다 ! 
```

```sql
SELECT YEAR, MONTH, COUNT(*) AS PUCHASED_USERS,
	ROUND((COUNT(*)/ (SELECT COUNT(*)
											FROM USER_INFO 
											WHERE YEAR(JOINED) = 2021)), 1) 
		AS PUCHASED_RATIO
	FROM (
		-- year , month , user_id (2021 가입) 
	    SELECT DISTINCT YEAR(S.SALES_DATE) AS YEAR, 
							MONTH(S.SALES_DATE) AS MONTH, U.USER_ID
		    FROM ONLINE_SALE S
		    JOIN USER_INFO U 
				ON S.USER_ID = U.USER_ID 
						AND YEAR(JOINED) = 2021
				) 
	GROUP BY YEAR, MONTH
	ORDER BY YEAR, MONTH

#풀이할 때 아래 논리적 오류 큰 2가지 함정을 조심해야 한다.
#- 한 사람이 같은 년, 월에 2번 이상 구매할 수 있으므로 분자에서 중복을 제거해야 한다.
#- 2021년에 가입한 회원 중에 물건을 아예 구매 안한 사람도 분모에 포함되어야 한다.
#구현 방법은 DISTINCT로 중복을 제거하고, SELECT절에서의 서브쿼리로 분모를 제대로 명시해야 한다.
```

# DATE_FORMAT

# TIMESTAMPDIFF(DAY, 1, 2)

![Untitled](SELECT%20c29f7b471dda4d238480defbb4ceef7b/Untitled.png)

```sql
SELECT history_id ,
    car_id,
    DATE_FORMAT(start_date, '%Y-%m-%d') AS start_date,
     DATE_FORMAT(end_date, '%Y-%m-%d') AS end_date,
    (
     CASE
        WHEN (TIMESTAMPDIFF(DAY,start_date,end_date)+1) >= 30 THEN '장기 대여'
        ELSE '단기 대여'
        END 
        )
     AS rent_type
        FROM car_rental_company_rental_history
        WHERE DATE_FORMAT(start_date,'%Y-%m') = '2022-09'
        ORDER BY history_id DESC;
```