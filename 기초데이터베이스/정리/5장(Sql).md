* \# 어려운 예제
	* ![[Pasted image 20231010214248.png]]
	* ![[Pasted image 20231010214308.png]]
	* ![[Pasted image 20231010214314.png]]
	* 적색 혹은 녹색이면 단순히 OR하면 되지만
	* 이를 AND로 바꾸면 배의 색이 녹색이면서 빨강임을 의미하게 된다.
	* 둘 다 예약한 적이 있는 지 확인 하려면 Boats를 B1, B2로 나누어서 and해야한다.
	* ![[Pasted image 20231010214713.png]]
	* 다만 밑에서는 이름만 같고 다른 사람도 나오게 된다. 이를 해결하기 위해서는 name과 id를 둘다 남겨서 교집합하고, 거기서 name을 뺴오는 것이 낫다.
* \# 만약 두 테이블을 조인하려한다면 반드시 where 절에 두 키가 같다는 조건을 써줘야한다. 왜냐하면 단순히 FROM에 두 테이블을 적으면 cross product가 되기에 우리가 생각하는 테이블이 아니다.
* \# 선원들중 가장 높은 등급을 가진 뱃사람을 구하여라
```sql
	select S.sid
		from Sailors S
		where S.rating > ALL(select S2.rating from Sailor S2)
```
* 모든 배를 예약한 적이 있는 뱃사람의 이름을 구하시오
	* 생각1: sailors로 행을 돌린다 했을때, 각 sailor마다 예약한 보트가 있을 거고, 전체 보트중에서 이를빼면, 그 sailor가 예약 안한 보트가 나온다. 이게 없으면 된다.
	```sql
		select S.name
			from Sailors S
			where NOT EXISTS(select B.bid
								from boats B
							EXCEPT
							select R.bid
								from reserves R
								where R.sid = S.bid)
	```
	* 생각2: EXCEPT을 제외하려면 각 사람당 예약하지 않은 보트가 없어야하는데, 예약하지 않은 보트는 그 사람의 reservation리스트중에 그 보트가 없으면 된다.
	```sql
		select S.name
			from Sailors S
			where NOT EXISTS(select B.bid
								from boats B
								where NOT EXIST(select R.bid
													from reserves R
													where R.sid = S.bid AND R.bid = B.bid)
	```
* /# having 절에는 절대 R.color등의, 값이 하나로 나오지 않는 수식을 적으면 안된다. 그룹을 대표하는 값만 사용!
* /# ![[Pasted image 20231022161447.png]]
	* 여기서 두명 이상의 뱃사람을 가지는 등급이므로 나이로 자르고 이에 대해 having을 하면 안된다.

* \# ![[Pasted image 20231022170314.png]] 
	* 이는 Reserves에서 등록을 하려 할 때, Reserves가 예약하려는 배(bid)의 이름이 Interlake면 안된다는 것
	* 즉 다른 테이블을 참조가능하다.



* dba(database administration)
	* 데이테베이스에 관한 모든 관리
* sql
	* DDL(data definition languege, 데이터 정의어):
		* 테이블과 뷰에 대해 생성, 삭제, 수정을 지원하며,
		* 무결성 제약 조건을 테이블 생성 초기에 테이블에 대해 정의할 수 있으며, 나중에도 가능하다.
	* DML(data management languege, 데이터 조작어):
		* 행을 삽입, 삭제, 수정
* ## 5.2 기본sql 질의 형태
	*  질의의 기본형
		* ![[Pasted image 20231010201531.png]]
		* select 절
			* 프로젝션에 관련된다
			* 실제로 추출하기 위해서 사용된다.
			* select-list는 S.name, S.age 등으로 작성되며 한 테이블이 리턴되므로 이 떄는 name열 age열을 잘라 붙여 만들어서 리턴한다.
			* Distinct
				* distinct는 생략 가능하며, distinct를 넣어주면 중복을 제거해준다.
				* 위의 경우 name과 age가 같은 행이 있다면 하나 제거해준다.
				* distinct는 sort를 한 다음 중복을 제거하기에 많은 오버헤드를 가져온다.
			* distinct를 뺴면 결과는 행들의 다중집합이 된다.
				* 다중집합
					* 집합안에 같은 원소를 갖는 것을 허용한다 {a, a, b}
					* {a,a,b} 와 {a,b,a}는 같은 다중집합이다.
			* 모든 열을 가져온다면, \*를 써도 된다.
				* 다만 \*는 가독성이 좋지 않기에 나중 유지 보수에는 좋지 못하다. 따라서 자제하는 것이 좋다.
			* select-list에는 S.name도 올 수 있지만, from-list에서 범위 변수를 제공 안했다면 그냥 name도 가능하다.
		* from절
			* 크로스 프로덕트에 관련된다.
			* from-list는 테이블 이름들의 리스트
			* 만약 from-list에 테이블이 2개이상온다면 이는 크로스 프로덕트를 통해 테이블을 하나로 만든다.
			* AS
				* Students AS S 등으로 범위 변수(여기선 S)를 나타내는 키워드 AS가 있다.
				* 이는 생략 가능하다. 즉 Students S 로도 가능하다.
				* 만약 그냥 Students 만 있다면 where 절에선 Students.name 등으로 사용하면된다.
			* 웬만하면 범위변수를 사용하는 것이 좋다.
			* from도 중첩질의를 통한 인스턴스를 사용할 수 있다.
		* where절
			* 셀렉트, 조인에 관련된다.
			* 선택하기 위해서 사용된다.
			* where절은 생략 가능하다.
			* where 절에는
				* 수식 op 수식 조건들이 Boolean(AND OR NOT) 조합으로 구성된다
				* op는 <, <=,>,>=,<>,  이 존재하고 <>는 not equal 이다.
				* 수식은 열 이름, 상수, 수, 문자열이다.
			* \# NOT을 가진 수식은 주어진 비교 연산자들로 항상 같은 의미의 NOT 없는 수식으로 바꿀 수 있다.
		* 개념적 평가 전략
			* from-list의 테이블에 대한 크로스 프로덕트 계산
			* 나온 결과에서 자격조건을 만족하지 않는 행 삭제
			* select-list에 나타나지 않는 열삭제
			* distinct가 있으면 중복제거
	* ### selelct 명령어에서 수식과 문자열
		* #### select-list는 수식도 가능하다
			* select-list의 각 항은 수식 AS 열이름 형식이 될 수 있다.
			* 열이름은 질의의 출력에 나타나는 열의 새로운 이름
			* 예로 S.rating + 1 AS rating 이라고 한다면, 기존 rating에 +1한 값을 rating으로 저장한다.
		* 알파벳으로 구성된 문자열은 비교연산자(> , < , =)등을 사용할 수있다.
		* #### collation
			* 만약 january, february 등 달을 비교하고 싶다면 sql은 순서(collation)을 지원하므로 이를 이용
		* #### like
			* 형태부합을 찾을 수 있다.
			* _ 은 임의의 한문자
			* %는 0개 이상의 임의의 한 문자.
			* 또한 다른 비교 연산자는 띄어쓰기는 무시하는데, like에서는 'jet' 와 'jet '를 다르게 본다.
			* 사용은
				* S.name LIKE 'B_%B'  이건 S.name이 오른쪽 형태를 만족하는 것만
	* ### SQL의 여러 연산
		* UNION, INTERSECT, EXCEPT
			* 이 세 연산은 union-compatible할 때에 사용할 수 있다.
			* 기본적으로 중복이 제거된다.
			* UNION 
				* 중복을 유지하려면 UNION ALL
				* 합집합
				* ![[Pasted image 20231010214909.png]]
			* INTERSECT
				* 중복을 유지하려면 INTERSECT ALL
				* 교집합
				* ![[Pasted image 20231010214919.png]]
			* EXCEPT
				* 중복을 유지하려면 EXCEPT ALL
				* 차집합
				* A -B는 A집합에서 B집합과 겹치는 것만 제외한다.
				* ![[Pasted image 20231010215001.png]]
		* IN
			* IN(서브쿼리 or  값)
			* IN구문에 포함된 하나의 값이라도 같은 게 있으면 true
			```sql
			select name
				from sallayMan
				where sal IN (300, 400, 500)
			```
			* 즉 월급이 300, 400, 500중 하나라도 포함되면 true
			* not을 붙일 수 있다.
			* /# 참고로 IN()은 =ANY() 와 동일하다.
			* /# 참고로 NOT IN()은 <>ALL()와 동일하다. any가 아닌 all인 이유는 해당하는 전체가 없어야 하므로
		* op ANY
			* ANY(서브쿼리 or 값)
			* 하나라도 만족하는 값이 있으면 true를 반납한다.
			 ```sql
			 select age
				 from salaryMan
				 where name = ANY("김떙떙", "나훈아", "배지터")
			```
			* 즉 이름이 김떙떙, 나훈아, 배지터 셋 중 하나라도 포함되면 그 사람의 나이를 출력
			* ANY안에 쿼리 문을 넣어도 된다. select에서 도메인이 string이기만 하면 된다. 
		* op ALL
			* ALL(서브쿼리 or 값)
			* 전체를 만족해야 true를 반납한다.
			```sql
			select name
				from salaryMan
				where age >= ALL(300, 200, 100)
			```
			* 즉 나이가 300이상이어야 한다.
		* EXISTS
			* EXISTS(서브쿼리)
			* 서브쿼리문에서 반환된 행이 하나라도 존재하면 true를 반납한다.
			```sql
				SELECT * 
				FROM orders o 
				WHERE EXISTS ( SELECT 1 FROM customers c WHERE o.customer_id = c.customer_id );
			```
			* 여기서 1이라고 적은 것은 반환되는 릴레이션이 어떤 값이든 상관 없어서이다.
			* 부질의문에서는 o가 선언된 적 없는데 어떻게 사용하는 걸까?
				* 주질의문에서 o에 대해 행별로 where문을 판단할 때, 부질의문을 사용한다.
				* 이에 해당하는 o를 부질의문 가져올 수 있다
			* not을 붙일 수 있다.
		* UNIQUE
			* UNIQUE(서브쿼리)
				* 서브쿼리에서 어떠한 행도 중복되지 않는다면 true를 반환

	* ### 중첩 질의(nested query)
		* ![[Pasted image 20231010220224.png]]
		* 중첩 질의는 그 안에 내장된 다른 질의를 가지는 질의
		* 내장된 질의가 부질의(subquery)이다.
		* 물론 내장된 질의도 중첩질의 일 수 있다.
		* #### 상호 관련된 중첩 질의
			* 외부질의에서 사용된 릴레이션을 내부 질의에서 사용하는 것.
			* 부질의에 나타난 외부질의의 릴레이션을 상관관계(correlation)이라고 한다.
	* ### 집단 연산자
		* 집단 연산자에는 COUNT, AVG, MIN, MAX, SUM이 존재하고 select문에서 사용가능하다.
		* 집단 연산자에서는 DISTINCT가 필요하지 않지만  사용하는 것을 제한하고 있지는 않다.
		* 집단 연산자는 중첩하면 안된다.
		* COUNT
			* COUNT (\[DISTINCT] A)
			* 속성 A의 행의 수를 리턴한다.
			* 주로 행을 셀려고 할 때에는 COUNT (\*)를 사용한다.
		* AVG
			* 속성의 평균 값을 리턴
		* MAX
			* 속성의 최대 값을 리턴
		* MIN
			* 속성의 최소 값을 리턴
		* SUM
			* 속성 값의  합을 리턴
		* 주의: 집단 연산자를 사용할 경우, GROUP BY를 사용하지 않는 한 select문은 오직 집단 연산만 사용해야 한다.
		```sql
		select S.name, Max(S.age)
			from sailors S
		```
		* 위 쿼리문은 따라서 불법이다. 따라서 아래처럼 작성해야 한다.
		```sql
		select S.name, S.age
			from Sailors S
			where S.age = (select MAX(S2.age)
							from Sailors S2)
		```
	* ## GROUP BY와 HAVING 절
		* 이는 특정 열을 그룹 별로 나누어 질의를 하고 싶을 때 사용한다.
		* 일단 select 절을 사용한 다음, 이를 group으로 묶는다.
		* GROUP BY절은 각 그룹의 하나만을 리턴한다.
		* ![[Pasted image 20231022151401.png]]
			* SELECT select-list
				* 여기에는 1. 열이름 리스트, 2. aggop AS 새이름 의 형태를 가진 항들의 리스트로 구성된다. 여기서 aggop는 바로 위에서 배운 집단 연산자.
			* GROUP BY grouping-list
				* select-list에 나온 모든 열은 grouping-list에도 나타나야 한다.
				  ```sql
				  SELECT department, AVG(salary) as avg_salary 
				  FROM employees 
				  GROUP BY department;
				  ```
				* 이를 보면 department가 group-by에도 나타난 것을 볼 수있다.
					* 여기서 salary도 열이고 이 또한 필요한 열이다. 다만 이는 Max등으로 모호성을 처리하기에 GROUP BY에 나타날 필요는 없다.
				* 이에 대한 이유는 GROUP BY절은 각 그룸의 하나만을 리턴하는데, 만약 select에는 존재하는 열이, group by에는 존재하지 않다면, 서로 다른 값들 중 어느 한 값을 대표로 리턴해야하는지 불 분명해지기 때문이다.
			* HAVING group-qualification
				* 이는 그룹을 결과에 포함시킬지 말지를 결정한다.
				* 여기서 group-qaulification에서 나타나는 수식들은 그룹에서 단 한개만 가질 수 있는 값이어야 한다.
					* 예를 들어서 `COUNT(*) > 1`에서 이는 true라는 정확히 한 값만 갖는다.
				* 
		* 이러한 쿼리문의 순서는
			* 1. from을 통해 크로스 프로덕트하여 가져온다.
			* 2.where절을 적용하여 필터링한다.
			* 3.select를 통해 필요한 열만 남기고 자른다.
			* 4.group by를 통해 그룹별로 나타낸다
			* 5.having을 통해 group을 결과에 넣을지 결정한다.
			* 남아있는 그룹마다 하나의 답 행을 select를 이용하여 만든다.
				* EVERY
		* EVERY(수식)
			* having절에서 사용되며
			* `HAVING COUNT(*) AND EVERY (S.age <= 60)`
			* 등으로 
			* GROUP안에서 모든 행들이 만족해야 한다.
		* ![[Pasted image 20231022160325.png]]
		* ![[Pasted image 20231022160346.png]]
			* 위를 아래처럼 바꾼다하였을 때 결과는 다를 수 있다.
			* 아래는 그룹의 개수에도 영향을 받고, 위의 같은 경우 한그룹내의 나이가 25,25, 70 인경우 먼저 60이상인 70이 짤리고 25,25가 그룹을 형성하는데
			* 아래의 경우 그룹이 25,25,70이라 EVERY조건을 만족 못시켜 그룹자체가 결과에 포함이 안된다
* ## NULL
	* 아직 값을 알 수 없을 때 사용
	* 이에 대한 AND, OR,NOT 적용
		* A AND B
			* A나, B가 UNKNOWN -> UNKNOW
		* A OR B
			* A가 TRUE면 B가 FALSE든 UNKNOWN이든 TRUE
		* 이에 대해 생각해보면 알 지 못하는 값이 TRUE 냐 FALSE냐 상관없이 결과가 어떻게 될 지 생각해보면된다.
	* SQL에서 where의 결과가 false나 unknown이면 제거 된다.
	* NULL == NULL은 NULL 일까 true일까
		* true로 규정한다
		* 즉 두 행이 null로 동일하다면 이건 중복이다.
	* 산술연산에서 NULL이 존재하면 결과도 NULL이다,
	* count(/.*)에는 NULL도 포함해서 계산한다.
	* 그 외의 COUNT, MAX,MIN,SUM,AVG는 NULL만 무시한다.
* ## 외부조인(outer join)
	* 좌측 외부조인
		* 좌측 테이블의 레코드는 모두 가져오고, 우측 테이블과 조건이 일치할 경우에만 연관 정보를 가져온다.
			* ![[Pasted image 20231022164923.png]]
			* ![[Pasted image 20231022164937.png]]
			* 이를 보면 왼쪽 테이블은 1,2,3 그대로 가져왔고, 조건인 id가 같은 지왼쪽 테이블 레코드마다 확인하면, 
				* 1은 101로 동일한 게 있으므로 Alice 가져옴
				* 2는 일치하는 게 없으니 NULL
		* 즉 일치하는 게 없으면 NULL을 가져온다는 것이 핵심이다.
	* 우측 외부조인
	* 전체 외부조인
		* 전체 외부조인은 양쪽 레코드를 모두 가져온다.  이는 좌측 외부조인과 우측 외부조인을 합친 것과 같다.
		* 이를 보면 id를 기준으로 101,102,103,104모두 레코드를 만들고 빈 곳은 null로 채운다.
		* ![[Pasted image 20231022165235.png]]
		* ![[Pasted image 20231022165244.png]]
* ## NULL 값 불허
	* 필드 정의에서 NOT NULL을 표시함으로서  널 값을 불허할 수 있다.
	* 즉 primary key 필드를 위해 묵시적으로 NOT NULL 제약 조건이 있다.
	  
  * ## 한 테이블 위의 제약 조건
	  * CHECK (조건식)
	  * ![[Pasted image 20231022165807.png]]
  * ## 둘 이상의 테이블을 위한 제약 조건
	  * ASSERTION(단정)
		  * `CREATE ASSERTION assertion_name CHECK (condition);`
		  * 이는 DBMS가 ASSERTION을 활성화하여 데이터 작업 중에 알아서 확인 하는 것이다.
		  * 테이블 자체에 두 테이블에 관한 CHECK를 거는 것은 상당히 번거롭고 예를 들어 두테이블의 행의 값이 100이하라고 하면, 한 테이블이 아예 비었는데도 참이 되는 문제가 발생할 수 도있다.
		  * 이로 인해
		  * ![[Pasted image 20231022171423.png]]
		  * 이렇게 제약조건을 따로 명시하는 것
* ## 트리거
	* 참고로 DBA는 인간, 즉 데이터 베이스를 직접 관리하는 쪽이며, 이를 DBMS가 도와주는 역할 이다.
	* 트리거는 DBMS에 의하여 자동적으로 실행되는 프로시저로, DBA(인간)에 의해 표시된다.
	* 능동 데이터 베이스
		* 트리거 집합을 가진 데이터 베이스
	* 트리거의 명세
		* 이벤트
			* 트리거를 구동시키는 DB변경사항
		* 조건
			* 트리거가 구동될 때 수행되는 질의
		* 동작
			* 트리거가 구동되고 조건이 참일 때 수행되는 프로시저
