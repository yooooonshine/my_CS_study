* \# 어려운 예제
	* ![[Pasted image 20231010214248.png]]
	* ![[Pasted image 20231010214308.png]]
	* ![[Pasted image 20231010214314.png]]
	* 적색 혹은 녹색이면 단순히 OR하면 되지만
	* 이를 AND로 바꾸면 배의 색이 녹색이면서 빨강임을 의미하게 된다.
	* 둘 다 예약한 적이 있는 지 확인 하려면 Boats를 B1, B2로 나누어서 and해야한다.
	* ![[Pasted image 20231010214713.png]]
	* 다만 밑에서는 이름만 같고 다른 사람도 나오게 된다. 이를 해결하기 위해서는 name과 id를 둘다 남겨서 교집합하고, 거기서 name을 뺴오는 것이 낫다.
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
			* 모든 열을 가져온다면, *를 써도 된다.
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
			* not을 붙일 수 있다.
		* op ANY
		* op ALL
		* EXISTS
			* not을 붙일 수 있다.
	* ### 중첩 질의(nested query)
		* ![[Pasted image 20231010220224.png]]
		* 중첩 질의는 그 안에 내장된 다른 질의를 가지는 질의
		* 내장된 질의가 부질의(subquery)이다.
		* 물론 내장된 질의도 중첩질의 일 수 있다.
		* #### IN
			* a IN bInstances
			* bInstances에 존재하는 a만 선택한다.
			* NOT IN 은 존재하지 않는 것을 선택한다.