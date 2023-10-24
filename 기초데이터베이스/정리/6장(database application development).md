
* ## sql in application cod
	* 자바나 c언어 안에서 sql문을 사용할 수있다.
		* 두 가지 조건이 필요한다.
			* sql문은 host variables(언어 안의 변수)를  접근가능해야 한다.
			* 데이터 베이스와 연결 되어야 한다.
	* 이에 두가지 방법이 존재한다.
		* embed sql(host languege안에 존재하는 기능)
		* sql문을 호출하기 위한 특별 api를 사용한다(JDBC등)
* ## Embed sql(내장형 sql)
	* 전처리가 sql문을 api call(해당하는 언어에 존재하는 함)로 바꿔준다.
	* 일반 컴파일러는 코드를 컴파일한다. 즉 sql문은 전처리가 다 처리해줘야 한다.
	* 언어 구성
		* EXEC SQL CONNECT
			* 이 명령어로 sql 사용함을 알려주다.
		* EXEC SQL BEGIN DECLARE SECTION과 EXEC SQL END DECLARE SECTION
			* 사용방법
			```sql
			EXEC SQL BEGIN DECLARE SECTION
			char a[20];
			short c;
			EXEC SQL END DECLARE SECTION
			```
			* 이렇게 선언된 변수들은 sql문 안에서 :a 등 콜론(:)을 붙여서 사용가능하다.
		* EXEC SQL Statement
			* 조작어 실행
			```sql
			EXEC SQL
			INSERT INTO sailors
				VALUES(:a,:b,:c);
			```
	* 내장형 sql의 ERROR  변수
		* SQLCODE
			* 옛날에는 특정 에러를 가리키는 음수값들이 있었다.
			* 예를 들어 -1 이면 io ERROR등 지정되어있다
		* SQLSTATE
			* 현재 주로 이 에러변수를 사용하고
			* CHAR\[6]로 에러를 표현한다.
				* 참고로 마지막 5번째 값은 null Character라, 실질적으로는 5개의 값으로 판단한다.
			* SQLSTATE는 에러가 나면 여기에 저장되는 단순 변수이지 이 변수가 프로그램을 종료하거나 하지는 않는다. 따라서 SQLSTATE값을 예상해 예외처리를 해주어야 한다.
		* Impedance Mismatch
			* 질의 결과 테이블의 사이즈를 몰라 생기는 문제
		* CURSOR
			* impedance mismatch문제를 해결하기 위해 생겼다.
			* 사용법
				* 커서를 쿼리문에서 선언한다.
				* 커서를 open하고 반복적으로 행을 fetch하고 move한다.(fetch하면 자동으로 move한다.)
					* 참고로 레코드의 끝은 SQLSTATE에러로 알 수 있다. 레코드의 끝 다음값은 null값은 아니나,기존 테이블과 도메인이 맞지 않고 이로 인해 SQLSTATE에 오류값을 넣게 된다. 우린 이를 확인해 끝을 판별한다.
				* 커서가 가리키는 행을 modify/delete할 수 있다.
			* 커서 사용 예시
			```SQL
			EXEC SQL DECLARE sinfo CURSOR FOR
				select S.name
					from Sailors S
					ORDER BY S.name;
					FOR READ ONLY //이는 읽기 전용 커서에만 붙인다.
 			EXEC SQL OPEN sinfor;
			while(SQLSTATE != '02-000'){
				EXEC SQL FETCH sinfo INTO :c_name;
			}
			EXEC SQL CLOSE sinfo;
			```
			* 수정
			```sql
			UPDATE sailor S
			SET S.rating = S.rating + 1
			WHERE CURRENT of sinfo;
			```
			* 삭제
			```sql
			DELETE sailors S
				FROM CURRENT of sinfo;
			```
			* INSENSITIVE
				* 커서가 열려 있는 상태에서 데이터베이스의 변경 사 항이 반영되지 않습니다.
				* INSENSITIVE 커서는 결과 집합을 복사하여 메모리에 유지하므로, 데이터베이스로의 추가 요청 없이 결과 집합을 반복해서 조회할 수 있습니다.
				```sql
				EXEC SQL DECLARE sinfo INSENSITIVE CURSOR FOR
					select S.name
						from Sailors S
						ORDER BY S.name;
				```
			* 커서 SCROLLING
				* 커서를 차례대로가 아니라 점프등을 하고 싶다면 SCROLL 키워드를 이용하면 된다.
				```sql
				EXEC SQL DECLARE sinfo SCROLL CURSOR FOR
					select S.name
						from Sailors S
						ORDER BY S.name;
				EXEC SQL OPEN sinfo;
				EXEC SQL FETCH PRIOR sinfo INTO :c_name;
				```
				* FETCH NEXT/PRIOR
					* 원래는 현재를 가져오고 MOVE하지만 다음을 가져오거나 전을 가져온다.
					* 만약 prior를 하면 커서 이동방향이 바뀐다.
						* 예로 n번째에서 prior를 하면 n-1을 가져오고 커서는 n-2가 된다.
				* FETCH FIRST/LAST
					* 마지막으로 이동하면 커서는 더 이상 움직이지 않는다
				* FETCH RELATIVE 3(-3)
					* 현재 위치 기준으로 이동
				* FETCH ABSOLUTE 3(-3)
					* -1은 LAST
					* 1은 첫번째째