
* ## sql in application code
	* 자바나 c언어 안에서 sql문을 사용할 수있다.
		* 두 가지 조건이 필요한다.
			* sql문은 host variables(언어 안의 변수)를  접근가능해야 한다.
			* 데이터 베이스와 연결 되어야 한다.
	* 이에 두가지 방법이 존재한다.
		* embed sql(host languege안에 존재하는 기능)
		* sql문을 호출하기 위한 특별 api를 사용한다(JDBC등)
## Embed sql(내장형 sql)
전처리가 sql문을 api call(해당하는 언어에 존재하는 함)로 바꿔준다.
일반 컴파일러는 코드를 컴파일한다. 즉 sql문은 전처리가 다 처리해줘야 한다.
### 언어 구성
* EXEC SQL CONNECT
이 명령어로 sql 사용함을 알려주다.
* EXEC SQL BEGIN DECLARE SECTION과 EXEC SQL END DECLARE SECTION
사용방법
```sql
EXEC SQL BEGIN DECLARE SECTION
char a[20];
short c;
EXEC SQL END DECLARE SECTION
```
이렇게 선언된 변수들은 sql문 안에서 :a 등 콜론(:)을 붙여서 사용가능하다.
* EXEC SQL Statement
조작어 실행
```sql
EXEC SQL
INSERT INTO sailors
	VALUES(:a,:b,:c);
	```
## 내장형 sql의 ERROR  변수
* SQLCODE
	* 옛날에는 특정 에러를 가리키는 음수값들이 있었다.
	* 예를 들어 -1 이면 io ERROR등 지정되어있다.
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
		* 커서가 열려 있는 상태에서 데이터베이스의 변경 사항이 반영되지 않습니다.
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
			* 1은 첫번째

## Read-Only Cursor
이는 커서로 쓰기는 못하고 오직 읽기만 가능하게 하는 것.
```C
EXEC SQL DECLARE sinfo CURSOR FOR
	SELECT S.Sname
	From Sailors S
	FOR READ ONLY
```

## Dynamic SQL
때때로는 SQL 쿼리문을 compile time에 알 수 없는 경우가 있다.
예를 들어 특정 어플리케이션은 user로 부터 명령을 받아 이를 기반으로 알맞는 SQL문을 날려야 하는 경우가 있다.
이럴 경우에 Dynamic SQL을 사용한다.

참고로 기존의 :등으로 가져오는 것은 static한 방법으로 compile시 가져오는 방법이다.
Dynamic sql은 dynamic한 방법으로 on-the-fly 즉 runtime시 가져오는 방법이다.
```C
char sqlString[] = {"DELETE FROM Sailors WHERE rating > 5"};
EXEC SQL PREPARE readytogo FROM :sqlString;//sql문을 받아 준비한다.
EXEC SQL EXECUTE readytogo;//sql문을 실행한다.
```
즉 sqlString으로 부터 sql문을 받아서 sql문을 실행한다.

# 임베디드 sql의 한계
host언어에서 각 DBMS에 맞는 preprocessor는 임베디드 sql문을 알맞는 함수호출로 변환한다.
따라서 DBMS에 따라 해석이 달라져 호출되는 함수도 달라진다.
즉 소스코드가 여러 DBMS와 연동될 수 있도록 컴파일 될 수 있더라도, 마지막에는 실행 가능한 특수한 DBMS에 대해서만 작동한다.
따라서 한 프로그램이 여러 DBMS를 사용해야 하는 데, 임베디드 sql은 불편하여, 실행 수준에서도 독립이 필요하다.

# Database API: JDBC와 ODBC
JDBC(java database connectivity)와 ODBC(Open Database Connectivity) 모두 sql과 프로그래밍 언어를 연결할 수 있다.
이 둘은 응용 프로그래밍 인터페이스를 통하여 데이터베이스의 특성을 프로그래머에게 보여준다.
즉 jdbc는 java에 맞게 인터페이스와 클래스를 구현해 뒀고, 이를 통해 데이터 베이스 접근을 가능하게 하는 것이다.

기존의 임베디드SQL은 소스 코드 수준에서만 DBMS-독립이지만, 
JDBC와 ODBC는 모두 소스코드와 실행 수준에서도 DBMS-독립이다.
JDBC나 ODBC를 사용하면 단 하나의 DBMS가 아니라 동시에 여러 개의 다른 DBMS에 접근 가능하다.
## JDBC가 제공하는 기능들
* 원격의 database와 연결하기
* sql문을 실행하기
* sql결과를 받기
* 트랜잭션 관리 하기
* 예외 처리리

## 드라이버
JDBC나 ODBC에는 기존과 다르게 DBMS-전용 드라이버라는 것이 존재한다.
이는 ODBC나 JDBC에 대한 호출 명령어들을 DBMS 전용 호출 명령어로 변환해주는 소프트 프로그램이다.
DBMS는 실행 시간에 알려지므로, 각각의 DBMS를 각각의 드라이버들은 실행 시간에 동적으로 드라이버 관리기(driver manager)에 등록된다. 
![[Pasted image 20231207174112.png]]
즉 그림과 같이 java진영에서 jdbc api가 sql문을 jdbc 호출 함수로 바꿔주고
JDBC Driver는 이러한 jdbc호출 함수를 특정 DBMS에 맞는 함수 호출로 바꿔준다.
![[Pasted image 20231207174443.png]]
위와 같이  driver를 바로 호출 하지 않고, 실행 중 동적으로 사용되는 드라이버를 Driver Manager에 적재하고, 매니저를 통해 알맞는 드라이버가 함수 호출을 변형한다.
## JDBC의 구조
* Application: Db와의 연결을 시작하고, sql문을 주고, 연결을 끊는다.
* Driver Manager:사용되는 driver를 적재하고, 함수호출을 넘긴다.
* Driver:DB에 요청을 전송하고 응답을 받아, 알맞게 변형하다.
* Data Source
# JDBC에서 driver의 네가지 타입
---
## 타입 1.Bridge
이는 jdbc와 odbc간에도 변환 가능하게 하는 것
jdbc 함수 call을 odbc 함수 call로 바꿔주는 드라이버
다만 이는 새로운 드라이버는 필요없지만, 새로운 layer가 생긴다.

## 타입 2. 비 java 드라이버를 통한 고유 API로 직접 변환
즉 jdbc를 native API로 바꿔주는 드라이버
성능은 좋지만 native API가 설치되어야 한다.
## 타입 3. 네트워크 브리지
middleware server를 두어, jdbc 함수 콜을 middleware server에서 db call로 바꿔준다.
## 타입 4. java 드라이버를 통한 고유 API로 직접 변환
java driver로 바로 native api로 변환


# JDBC 연결
---
jdbc는 세션을 통해 데이터를 주고 받는다.
세션은 connection object를 만드는 것으로 부터 시작된다.
```java
String url = "www.naver.com:3306";
Connection con;
try{
	con = DriverManager.getConnection(url,id, password)
} catch(SQLException e)
```

세가지 종류의 sql문이 있다.
* statement: static, dynamic SQL
* preparedStatement: semi-static SQL, 즉 형식은 하는데 실행중 값을 알때
* callableStatement:stored procedures

## preparedStatement
```java
String sql = "INSERT INTO Sailors VALUES(?,?)";
PreparedStatement pstmt = con.prepareStatment(sql);
pstmt.set(1, sid);
pstmt.set(2, sname);

int a = pstmt.executeUpdate();//이 리턴 값은 영향을 준 record 개수
ResultSet a = pstmt.executeQuery();//이 리턴 값은 reserltSet 객체
```
여기서 ResultSet은 cursor와 유사하다.
즉 a.next()로 가져온다. 없으면 false 리턴한다.

## java의 에러

모든 에러의 최상위 class는 Throwable 이며
이를 상속받는 Error, RuntimeException, Exception이 존재한다.

에러를 캐치 했을 때
* getMessage()
* getSQLState()
* getErrorCode(): 이는 제조사
* getNextException(): 예외가 여러개 터졌을 때 다음 예외를 가져온다.
## Database Metadata
데이터베이스 meta data 객체는 db 시스템에 대한 정보를 제공한다.
```java
DatabaseMetaData md = con.getMetaData();
md.getDriverName()
md.getDriverVersion()
```

# SQLJ, SQL_java
이건 JDBC의 semi static query 모델이다.
즉 모든 형식은 정해졌으나 값만 모를때
```java
#sql iterator Sailors(Int sid, String name, Int rating);
Sailors sailors
#sql sailors = {
SELECT name, rating INTO :name, :rating
FROM Sailors WHERE sid = :sid;
}
```
이건 precompiler가 sqlj코드를 jdbc driver call로 바꿔주고, 이후 자바 컴파일러가 값을 넣어준다.