* ## 속성
---

* simple
* composite(복합): 즉 속성안에 또 속성이 존재
* ![[Pasted image 20230926150757.png]]
* multivalued 속성(다중값 속성): 원 두개  겹침 
* 
* derived 속성(유도 속성)
* 정규화된 속성은 모두 단일 속성이어야 한다.
* 위의 속성들은 객체지향 모델일 경우 좋을 수 도 있다.



* ## Relational algebra
---
* sql의 기반이 된다. relational algebra를 high level로 포장한 게 sql 이다. 즉 relational algebra가 먼저다.
* ![[Pasted image 20230926151948.png]]
* 모든 dbms는 querylanguege가 있고 relational은 sql이며, 다른 것들은 sql이 아니다.
* query languege: 데이터베이스로부터 데이터를 고안하고 정제
* sql은 특정 로직에 기반을 두는 데 그게 relational algebra 이다
* 또한 sql은 optimization이 가능해서 power full하다
* db에는 메타데이터, 인덱스, 데이터 파일이 존재한다.
	* 메타데이터는 데이터베이스를 만들 때 필요한 모든 정보, 즉 테이블 자체에 관한 정보, attribute는 몇개, 몇개있나, index는? 이런 모든 것.
* query languege는 programming languege가 아니다.
	* ![[Pasted image 20230926152709.png]]
	* turing 머신의 모든 기능 필요없다. 그냥 가져오는 게 중요
	* 튜링 완전(turing complete)이란 어떤 프로그래밍 언어나 추상 머신이 튜링 머신과 동일한 계산 능력을 가진다는 의미이며 튜링 머신으로 풀 수 있는 문제, 즉 계산적인 문제를 그 프로그래밍 언어나 추상 머신으로 풀 수 있다는 의미입니다.
	* 선언만 하면 된다. 나머지는 dbms가 해준다.
* Formal relational query languages
	* 두 수학적인 쿼리언어가 실언어의 기본을 형성한다.
	* ![[Pasted image 20230926153226.png]]
* ![[Pasted image 20230926153250.png]]
* relational에는 스키마(구조), 인스턴스가 존재. 당연히 쿼리문은 데이터 있는 테이블에 질의
* instance의 질의에서 나온 결과도 intance
* 즉 테이블구조에서 가져온 데이터도 테이블 구조다.
* 즉 결과의 스키마도 fix되어있다.
* 즉 결과테이블에도 질의를 던질 수 있다는 것이다.
* 즉 관계대수 연산자를 조합을 할 수 있다.
* 연산 순서를 바꿔도 된다.
* 이름, 번호를 가지고 속성을 가져올 수 있다.
* ## relationaly complete, query is fixed
	* 즉 스키마 유지.



* ![[Pasted image 20230926154605.png]]
* join(두 테이블을 연결)은 위 다섯가지 연산으로 유도가능
	* join은 여러 테이블에 있는 데이터를 조회할 때 사용
	* - 일반적인 경우 PRIMARY KEY(PK) 나 FOREIGN KEY(FK) 값의 연관에 의해 JOIN이 성립
	* 이게 기본 연산에 포함되지 않는 이유는 cross-product와 selection으로 만들 수 있기에.
	* 종류
		* natural
		* equi
		* theta
		* 
* cross-product
	* **Cartesian Product** (카티시안 곱)은 **발생가능한 모든 경우의 수**의 행이 출력되는 것을 의미합니다.  즉 n행과 m행을 곱하면 m* n 행
	* 쓰레기값이 많이 나오므로 잘 사용 x
	* 즉 s1 X r1 한 테이블에서 s1.sid = r1.sid가 같은 것만 select해서 가져오기도 함. 이 연산이 사실 join

N 개의 행을 가진 테이블과 M 개의 행을 가진 테이블의 카티시안 곱은 N*M 이 되는거죠!
* 교집합도 유도가능
* complete = closed =fixed
* 레코드는 특정 row
* 한테이블에 대한 연산은 selection, projection밖에 없다.
* selection은 where 절에 있는 것.

* select s.name , s.gpa from students s where s.age >= 20
	*  students 를 s로 받겠다
* 다대다는 table로 , 1대 다는 테이블 x
* S1 X R1 에서 S1이 outer,R1이 inner, s1 r1위치 달라지면 cost가 달라진다.
* 
* union-compatible
* ![[Pasted image 20230927121938.png]]
* ![[Pasted image 20231004140029.png]]
* ![[Pasted image 20231004135808.png]]
* 
* 첫 컬럼을 sid1으로, 5번째 column을 sid2로 , 이건 product했을떄 컬럽이 같을경우
* ![[Pasted image 20231004135705.png]]
* ![[Pasted image 20230927122143.png]]
* 이건 키와 foreing키를 의미
* ![[Pasted image 20230927122231.png]] 
* natural join에서는 key임을 아니까 sid 빼도 된다.
* ![[Pasted image 20230927122309.png]]
* /는 실제로 없지만 divide 연산자는 있다.
* x,y가 A에 있을떄 이에 해당하는 모든 y는 b에 있다. 이중 x만
* 즉 b에 포함하는 y에 해당하는게 a에 있을떄 x만
* B에 두개 이상있을경우 x가 B에 두개이상을 모두 가져야함
* 즉 B가 y1, y2, y3일경우 A에 (x, y1),(x, y2),(x, y3) 가 있어야지 x가 포함된다.
* ![[Pasted image 20230927123014.png]]
* 1. 103ㅣ라는 배를 예약한 선원
* 2. 103을 예약한 사람음 temp1에 temp1과 sailor 조인을 temp2에 ,  이게 가장 안좋음 reservees는 disk에 있으니 가져와야한다. 이를 이용해 임시 테이블을 만들어 또 disk에 저장(메모리는 휘발되기에) 이를 두번하니까 제일 안좋다
* 3. 
* 3보다 1번이 좋음. 1은 reserves103은 테이블이 좁아
* 3은 좁아지기전에 다 가져옴 
* 즉 1이 io 코스트가 더 쌈

* 즉 최대한 줄인 상태에서 join하는게 가장 싸다.
* ![[Pasted image 20230927123726.png]]
* 1은 빨간색 배를 예약한 선원
* 2는 빨간배의 id와 reserved와 조인 즉 테이블 크기를 최소한으로 줄여서.
* 테이블크기를 최소한으로 줄여서 사용하는게 io 코스트가 작다.
* ![[Pasted image 20230927124359.png]]
* v는 or이다. ,
* ^는 and 근데 red and green 이라면 동시에 빨강이면서 green이라는 의미 즉 말이 안돼
* ![[Pasted image 20230927124744.png]]
* 이처럼 교집합을 이용
* 단 교집합은 compatible에서만 사용가
* 