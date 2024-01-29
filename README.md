# DB-2-
/* 집계함수 대상 데이터를 특정 그룹으로 묶은 다음 그룹에 대한
총합, 평균, 최댓값, 최솟값 등을 구하는 함수.
*/
-- COUNT 로우 수를 반환하는 집계함수
SELECT COUNT(*)                         -- null 포함
     , COUNT(department_id)             -- default ALL
     , COUNT(ALL department_id)         -- 중복 포함, null X
     , COUNT(DISTINCT department_id)   -- 중복제거
     , COUNT(employee_id)
FROM employees;
SELECT COUNT(mem_id)
     , COUNT(*)
FROM member;
SELECT SUM(salary)              as 합계
     , ROUND (AVG(salary),2)    as 평균
     , MAX(salary)              as 최대
     , MIN(salary)              as 최소
     , COUNT(employee_id)       as 직원수
FROM employees;
-- 부서별 집계 <-- 그룹의 대상 부서
SELECT department_id       
     , SUM(salary)              as 합계
     , ROUND(AVG(salary),2)     as 평균
     , MAX(salary)              as 최대
     , MIN(salary)              as 최소
     , COUNT(employee_id)       as 직원수
FROM employees
GROUP BY department_id
ORDER BY 1 ;
-- 30, 60, 90 부서의 집계
SELECT department_id
     , SUM(salary)              as 합계
     , ROUND(AVG(salary),2)     as 평균
     , MAX(salary)              as 최대
     , MIN(salary)              as 최소
     , COUNT(employee_id)       as 직원수
FROM employees
WHERE department_id IN(30, 60, 90)
GROUP BY department_id
ORDER BY 1 ;
-- member 화원의 직업별 마일리지의 함계, 평균, 최대, 최소 값과 회원수를 출력하시오
-- 평균은 소수점 2째자리까지 표현 (정렬은 마일리지 평균  내림차순)
SELECT mem_job
     , COUNT(mem_job)                as 회원수
     , SUM(mem_mileage)              as 마일리지합계
     , ROUND(AVG(mem_mileage),2)     as 마일리지평균
     , MIN(mem_mileage)              as 마일리지최소
     , MAX(mem_mileage)              as 마일리지최대
FROM member
GROUP BY mem_job
ORDER BY 4 DESC;

-- kor_loan_status 테이블에 loan_jan_amt 컬럼을 활용하여
-- 2013년도 기간별 총 대출잔액을 출려하시오
SELECT *
FROM kor_loan_status;


SELECT PERIOD, loan_jan_amt
FROM kor_loan_status;

SELECT PERIOD
     , SUM(loan_jan_amt) as 총합계
FROM kor_loan_status
--WHERE PERIOD LIKE '2013%';
WHERE SUBSTR(PERIOD,1,4) ='2013'
GROUP BY PERIOD
ORDER BY 1;
DESC kor_loan_status; -- VARCHAR 확인

-- 기간별, 지역별, 대출총합계를 출려하시오

SELECT PERIOD
     , region
     , SUM(loan_jan_amt) as 총합계
FROM kor_loan_status
GROUP BY PERIOD
         , region
ORDER BY 1;
-- 년도별, 지역별 대출합계
SELECT SUBSTR(PERIOD,1,4) as 년도
     , region
     , SUM(loan_jan_amt) as 합계
FROM kor_loan_status   -- 검색조건!!
WHERE region ='대전'  -- 찾고싶은 지역!!
GROUP BY SUBSTR(PERIOD,1,4)
         , region
ORDER BY 1;

--employees 직원들의 입사년도별 직원수를 출력하시오 (정렬 : 년도 오름차순)

SELECT TO_CHAR(hire_date,'YYYY') as 년도 -- 년도를 뽑기 위한 방법
     , COUNT(*) as 직원수
FROM employees
GROUP BY TO_CHAR(hire_date,'YYYY')
ORDER BY 년도;


-- 집계 데이터에 대해서 검색조건을 사용하려면 HAVING 사용
-- 입사직원이 10명 이상인 년도에 직원수 출력
-- SELECT문 실행 순서
-- from -> where -> group by -> having -> select -> order by !!! 외우기 중요 !!!
SELECT TO_CHAR(hire_date,'YYYY') as 년도 -- 년도를 뽑기 위한 방법
     , COUNT(*) as 직원수
FROM employees
GROUP BY TO_CHAR(hire_date,'YYYY')
HAVING COUNT(*) >= 10
ORDER BY 년도;
     
-- member 테이블을 활용하여
-- 직업별 마일리지 평균금액을 구하시오
-- (소수점 2째 자리까지 반올림하여 출력)
-- (1)정렬 평균마일리지 내림차순
-- (2)평균 마일리지가 3000이상인 데이터만 출력

SELECT mem_job
     , ROUND(AVG(mem_mileage),2) as 평균
FROM member
GROUP BY mem_job
HAVING ROUND(AVG(mem_mileage),2) >=3000
ORDER BY 평균 DESC;

    -- customers 회원의 전체 회원수, 남자 회원수, 여자 회원수를 출력하시오
SELECT 
  COUNT(CUST_GENDER) as 전체 -- CUST_GENDER 컬럼의 값이 있는 모든 행의 수를 세어 전체 회원 수를 나타냅니다.
  , SUM (DECODE(CUST_GENDER, 'M', 1, 0)) as 남자 -- CUST_GENDER 컬럼의 값이 'M'인 경우 1을, 아닌 경우 0을 반환하여 모두 더해 남자 회원 수를 나타냅니다.
  , SUM (DECODE(CUST_GENDER, 'F', 1, 0)) as 여자 -- CUST_GENDER 컬럼의 값이 'F'인 경우 1을, 아닌 경우 0을 반환하여 모두 더해 여자 회원 수를 나타냅니다.
FROM customers; -- customers 테이블에서 위의 조건에 맞는 데이터를 가져옵니다.
