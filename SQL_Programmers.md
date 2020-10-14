```mysql
DESC EMP; 테이블의 구조, 타입, 칼럼명 등 확인
SHOW TABLES IN [DB명] LIKE '%coin%'
INSERT INTO sandbox VALUES (1, 'abc', 10, 'first');
INSERT INTO sandbox (name, value, note) VALUES ('def', 20, 'second');
DELETE FROM sandbox WHERE id=5;
TRUNCATE sandbox; 		-- 테이블 전체의 데이터를 삭제
UPDATE sandbox SET note = 'test' WHERE id = 2;
UPDATE sandbox SET value = value*10; -- VALUE 필드의 값을 모두 x 10 
SELECT * FROM sandbox ORDER BY FIELD(id, 2, 1, 3); -- FIELD(필드명, 순서)

Select EMP_ID, 'COL' from EMP; 표현식을 사용하여 출력
Select NAME as "성명", SALARY as "급여" from EMP; 칼럼 별칭 사용하여 출력
Select DISTINCT SALARY from EMP; 중복된 값을 제거하고 출력
Select LST_NAME||BIRTH from EMP; 칼럼울 합쳐서 출력 예은1213 

Select * From EMP1 UNION Select * From EMP2;  	 -- 중복된 값을 제거
Select * From EMP1 UNION ALL Select * From EMP2; -- 중복 값을 제거하지 않음
Select * From EMP1 INTERSECT Select * From EMP2; -- 교집합 결과를 출력
Select * From EMP1 MINUS Select * From EMP2;   	 -- 차집합 결과를 출력

SELECT 
sum(case when note='cat1' then value end) as 'cat1', 
sum(case when note='cat2' then value end) as 'cat2', 
sum(case when note='cat3' then value end) as 'cat3' 
FROM sandbox;

date_format(datetime, '%Y-%m-%d') 2020-10-10
date_format(now(), '%Y %y')
date_format(now(), '%M %m') October 10, January 01
date_format(now(), '%D %d') 10th 10, 1st 01
date_foramt(now(), '%W %w %a') Saturday 7 Sat, Sunday 0 Sun
date_foramt(now(), '%H %h') 시 16(24H) 04
date_foramt(now(), '%i') 분
date_foramt(now(), '%S %s') 초
date_foramt(now(), '%p') PMAM

now() 2020-10-14 00:52:18
curdate() 2020-10-14 00:00:00
adddate(curdate(), 1) 1일 후 --now 도 가능
subdate(curdate(), 1) 1일 전
```

```mysql
WHERE Clause
Select NAME "성명", SALARY "급여", (SALARY*0.1) "보너스", (SALARY*1.1) "총액" From EMP Where NAME='예은';

Where MAX_SALARY>=100 and MAX_SALARY<=200;
Where MAX_SALARY Between 100 and 200;

Where SALARY=100 Or SALARY=200;
Where SALARY In (100, 200);

Where PHONE like '%8267';
Where PHONE not like '%99%' -- 특정문잘르 제외한 검색

Where PHONE Is Null; NULL 값을 찾을 때
Where PHONE IS Not Null; NULL이 아닌 값을 찾을 때
```

```mysql
GROUP BY Clause
- 2005년 부서별 사원 수 출력
Select '2005년' year, DEPTNO 부서번호, count(*) 사원수 From EMP
Group By DEPTNO
Order By count9(*) DESC;

YEAR     부서번호     사원 수
------ ---------- ----------
2005년         30          6
2005년         20          5
2005년         10          3

- 부서별 인원수와 급여 평균 출력
Select DEPTNO 부서번호, count(*) 사원 수, AVG(SALARY) "급여평균" FROM EMP
Group By DEPTNO;

 부서번호   사원 수    급여평균    
-------- ---------- ---------- 
      30          6       1567
      20          5       2175 
      10          3       2917

GROUP BY + HAVING 절
- 월급 5000을 초과하는 Job별 급여 합계 출력 (이 때 판매원은 제외)
Select job, Sum(SALARY) "급여합계" From emp  
Where job != 'SALES'  				 -- 판매원은 제외
Group By job       					 -- 업무별로 Group By 
Having Sum(SALARY) > 5000    		 -- 전체 월급이 5000을 초과하는
Order By Sum(SALARY) DESC;  		 -- 월급여 합계로 내림차순 정렬
  
JOB                  급여합계
------------------ ----------
MANAGER                  8275
ANALYST                  6000
SALESMAN                 5600

- 사원 수 5명 이상 부서명과 사원 수 출력
Select B.DEPT_NAME, count(A.EMP_ID) "사원 수" From EMP A, DEPT B
Where A.DEPT_NO=B.DEPT_NO
Group By DEPT_NAME
Having count(A.EMP_ID)>=5;

DEPT_NAME      사원수
------------ -------
SALES              6
```



### Programmers

```mysql
- 젊은 동물의 아이디와 이름을 조회 (아이디 순)
SELECT Animal_id, name from animal_ins 
where intake_condition not in('aged')
order by animal_id;

- 이름이 같은 동물 중에서는 보호를 나중에 시작한 동물을 먼저 보여줘야 합니다.
SELECT animal_id, name, datetime from animal_ins
order by name, datetime desc;

- 보호소에 가장 먼저 들어온 동물
SELECT name from animal_ins
where datetime=(select min(datetime) from animal_ins);

- (Having) 09:00부터 19:59까지, 시간대별로 입양이 몇 건이나 발생했는지 조회 (시간 순)
SELECT hour(datetime) HOUR, count(*) from animal_outs
group by hour(datetime)
having HOUR>=9 and HOUR<20	-- hour(datetime) 은 Unknown...
order by hour(datetime);

- (JOIN) 입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름 (ID 순)
select b.animal_id, b.name 
from animal_ins a 
right join animal_outs b
on a.animal_id=b.animal_id
where a.animal_id is null
order by b.animal_id;

- Null 값인 경우 No Name 으로 표시
SELECT animal_type, ifnull(name, 'No name') as name, sex_upon_intake from animal_ins

- 중성화 여부 파악하기
SELECT animal_id, name, if(sex_upon_intake like 'Neutered%','O','X') 
from animal_ins;

select animal_id, name,
case 
when sex_upon_intake like 'Neutered%' then 'O'
wher sex... like 'Int%' then 'X'
else 'X' end
from animal_ins;

- 보호 기간이 가장 길었던 동물 두 마리의 아이디와 이름을 조회하는 SQL문을 작성해주세요. 이때 결과는 보호 기간이 긴 순으로 조회해야 합니다.
SELECT a.animal_id, a.name from animal_ins a, animal_outs b
where a.animal_id = b.animal_id
order by datediff(b.datetime, a.datetime) desc 
limit 2;

- (사용자 정의 변수) 보호소에서는 몇 시에 입양이 가장 활발하게 일어나는지 알아보려 합니다. 0시부터 23시까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회 (시간 순)
set @hour = -1;
select (@hour := @hour+1), (select count(*) from animal_outs
                            where @hour=hour(datetime))
from animal_outs
order by @hour;

- 번호 매기기
select (@i :=@i+1), name from (select @i:=1) as A, animal_ins;
```

