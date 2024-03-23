## JPA란
Java Persistence API이며, 자바 ORM의 표준

ORM(Object relational mapping)은 관계형 데이터베이스랑 객체를 매핑(변환)해주는 기술

즉 JPA가 entity를 분석하여 sql문을 생성하고, JDBC api를 사용하여 쿼리문을 날려준다. 

JPA 이전에는 하이버네이트라는 ORM이 존재했지만, 이에 대해 JAVA가 새롭게 만든게 JPA이다.
## JPA 구동방식
![[Pasted image 20231227173424.png]]
persistence가 설정 정보를 조회하여, EntityManagerFactory를 만든다.
EntityManagerFactory가 EntityManager를 생성한다.

## application.properties하고, persistence.xml차이
application.properties는 스프링부트에서 사용하는 설정파일이며
persistence.xml은 jpa에서 사용하는 설정파일이다.

application.properties에서 persistence.xml을 사용하지 않고도 동작할 수 있도록 설정할 수 있다.

## jpql을 사용한 sql
```java
public class JpaMain {  
  
    public static void main(String[] args) {  
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");  
        EntityManager em = emf.createEntityManager();  
  
        Member member = new Member();  
        member.setId(1L);  
        member.setName("이름");  
  
        EntityTransaction tx = em.getTransaction();  
  
        tx.begin();  
        em.persist(member);  
        tx.commit();  
  
        em.close();  
        emf.close();  
    }  
}
```
우선적으로 entityManagerFactory를 만들고, 이걸로 entitymanager를 만든다.
db에 넣기 위하여, entitymanager로 트랜잭션을 만들고 커밋한다.

커밋을 안하면 db에 반영되지 않는다. 즉 쿼리문이 날라가지 않는다.

* 등록: em.persist(객체) 
* 삭제: em.remove()
* 수정: 맴버를 find로 찾아내고, 그 맴버를 변경하면 영속성 컨테이너가 알아서 적용해준다.

## 엔티티 매니져 팩토리
엔티티 매니저 팩토리는 하나만 만들 수 있고, 이걸 사용하여 엔티티 매니저를 생성한다.
엔티티 매니저는 필요할 때마다 만들고, 버리고, 다시 만드는 식으로 사용한다.

즉 엔티티 매니저는 스레드상 공유하면 안된다. 

## JPA의 모든 변경은 transaction 내에서 실행해야 한다.

## JPQL
특정 나이 이상의 멤버들을 모두 알고 싶다면? 이런 쿼리는 단순히 존재하는 것으로는 불가능하다. 이를 JPQL을 통해 sqla문을 만들 수 있다.
### em.createQuery()
```java
em.createQuery("select m from Member as m", Member.class)
		.setFirstResult(5)  
		.setMaxResults(10)
		.getResultList();
```
일반적인 쿼리문과 다르다.
 왜냐하면 기본적인 쿼리문은 테이블을 대상으로 쿼리를 날리는데. 지금은 객체를 대상으로 쿼리를 날리는 것이기 때문이다.


여기서 `.setFirstResult(5)`  ,`.setMaxResults(10)`은 5번부터 10개 가져오라는 뜻이다.


# 프로젝션
---
select 절에 조회할 대상을 지정하는 것
프로젝션 대상: 엔티티, 임베디드 타입, 스칼라타입(숫자, 문자등)

distinct로 중복제거 가능하다.

예시
`select m from Member m`
`select m.team from Member m`

여기서 m, m.team이 프로젝션 부분이다.
참고로 JPQL문은 최대한 쿼리문이랑 비슷하게 작성해야 한다
따라서
`select m.team from Member m`보다는
`select m.team from Member m join m.team t`로 쓰자

## 여러 값 조회
`select m.username, m.age from Member m`등일 경우 username과 age를 어떻게 받을까
여러 방법이 가능하다
* Query 타입으로 조회
* Object 타입으로 조회
* new 명령어로 조회

### Query 타입으로 조회
```java
Query resultList = em.createQuery("select m.username, m.age from Member m").getResultList();
Object[] result = (Obeject[]) resultList.get(0);

```
쿼리에는 타입이 없으므로 Object로 들어가 있다.
### Object 타입으로 조회
```java
List resultList = em.createQuery("select m.username, m.age from Member m").getResultList();

Object o = resultList.get(0);
Object[] result = (Obeject[]) o;
```
위와 같이 username과 age가 Object 배열로 들어가 있으므로, 형변환으로 바꿔 가져온다.

이를 간편하게 타입 명시를 이용한다.
```java
List<Object[]> resultList = em.createQuery("select m.username, m.age from Member m").getResultList();

```

### new 명령어로 조회
이거는 DTO로 가져오는 방법이다.
```java
List<MemberDTO> resultList = em.createQuery("select new jpal.MemberDTO(m.username, m.age) from Member m", MemberDTO.class).getResultList();

public class MemberDTO {
	private String username;
	private String age;
}

```
그니까 생성자로 MemberDTO를 만들고 이 안에 username과 age를 넣는다.






# 페이징
---
JPA는 페이징을 다음 두 API로 추상화 한다.
* setFirstResult(int startPosition): 조회 시작 위치
* setMaxResult(int maxResult): 조회할 데이터 수
즉 페이징은 결국 몇번째에서 몇개 가져와가 전부이기 때문이다.

```java
em.createQuery("select m from Member m order by m.age desc" , Member.class)
	.setFirstResult(0)
	.setMaxResults(10)
	.getResultList();

```
다음과 같이 `order by`를 이용한다. 왜냐하면 sorting을 해야지 어디서부터 몇 개 가져와가 의미가 있기 때문이다.

desc는 역순이라는 의미


# 조인
---
조인에는 여러 조인이 있다.
* 내부 조인
* 외부 조인
* 세타 조인

## 내부 조인
우리가 아는 일반적인 조인
`select m from Member m [INNER] JOIN m.team t`
inner는 생략 가능
## 외부 조인
`select m from Member m LEFT [outer] join m.team t`
외부 조인에는 LEFT, RIGHT 조인이 존재하다.
LEFT 조인: LEFT는 전부 가져오고, RIGHT 테이블은 해당하는 게 없으면 null로
outer는 생략가능

## 세타 조인
연관관계가 없는 테이블을 조인할  수 있다.
`select count(m) from Member m, Team t where m.username = t.name`
별다른 join이 필요없다. 바로 where 명

## 조인 -ON절(JPA 2.1부터 지원)
* 조인 대상 필터링 가능
* 연관관계 없는 엔티티 외부 조인 가능
### 조인 대상 필터링
이거는 Member와 Team을 조인하면서, Team 이름이 A인 팀만 조회하고 싶을 때 사용한다.
`select m, t from Member m LEFT JOIN m.team t on t.name = 'A'`
### 연관관계 없는 엔티티 외부 조인가능
회원의 이름 과 팀의 이름이 같은 대상 외부조인 하고 싶을 때 사용
`select m, t from Member m LEFT JOIN Team t on m.username = t.name`




# 서브 쿼리
---
sql에서는 쿼리안에 쿼리인 서브쿼리가 가능하다.
jpa에서는 어떻게 처리할까?
다음과 같다.
```java
//나이가 평균보다 많은 회원
select m from Member m
	where m.age > (select avg(m2.age) from Member m2)
```
메인쿼리와, 서브쿼리가 관련이 없으므로 Member이름을 m과 m2로 다르게 했다.

다른 예시
```java
//한 건이라도 주문한 고객
select from Member m
	where (select count(o) from Order o where m = o.member) > 0
```
여기서는 m을 서브쿼리로 가져왔다 당연히 성능은 안좋아진다.

## 서브쿼리 지원함수
* `[NOT] EXISTS (subquery)`: 서브 쿼리에 결과가 하나라도 존재하면 참
* `{ALL| ANY | SOME} (subquery)`
* ALL은 모두 만족하면 참
* ANY, SOME: 둘은 같다 조건을 하나라도 만족하면 참
* `[NOT] IN (subquery)` : subquery 결과중 하나라도 같은 것이 있으면 참
## 서브 쿼리 한계
* jpa는 select, where, having 절에서만 서브 쿼리가 가능하며 from절에서는 불가능하다.
from절의 subquery가 필요하다면, 네이티브쿼리를 사용하거나, 쿼리문을 두번으로 나눠서 해결한다.




# JPQL 타입 표현
---
* 문자: 'HELLO'처럼 \'를 사용하며 \'가 필요한 경우 두번쓴다. 'she\''s'
* 숫자: 10L(Long), 10D(Double), 10F(Float)
* Boolean: True, False
* Enum: `jpabook.MemberType.Admin`등으로 패키지 명도 포함하여 명시
* 엔티티 타입: Type(m) = Book, 이건 m이 ITEM이고 이를 상속 받은 Book 객체등이 존재할 때 사용

Enum의 경우 지저분할 수 있고 이럴 경우 :를 사용할 수 있다.
```java
em.createQuery("select m from Member m where m.type = :memberType")
	.setParameter("memberType", MemberType.ADMIN)
	.getResultList();
```

# 기타
---
sql과 문법이 같은 식
* EXISTS, IN
* AND, OR, NOT
* =, >, >=
* BETWEEN, LIKE, IS NULL




# JPQL 기본 함수
---
JPQL에는 다음과 같이 기본으로 제공되는 표준 함수가 존재한다.
* CONCAT
* SUBSTRING
* TRIM
* LOWER, UPPER
* LENGTH
* LOCATE
* ABS, SQRT, MOD
* SIZE, INDEX

## 사용자 정의 함수 호출
