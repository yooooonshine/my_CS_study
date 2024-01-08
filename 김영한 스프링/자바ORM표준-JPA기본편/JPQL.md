jpa는 다양한 쿼리 방법을 지원한다.
* jpql
* jpa criteria
* queryDSL
* 네이티브 sql

## JPQL
단순히 검색말고 18살이상과 같은 쿼리문을 날리려면 어떻게 해야할까?

이럴 때 JPQL을 사용한다.

쿼리문을 날리고 싶다. 근데 테이블 중심이 아니라, 나는 지금 자바 객체를 다루므로 자바 객체를 대상으로 쿼리문을 날리고 싶다.

이럴 때 JPQL을 사용한다.

JPQL이란, JPA에서 sql을 추상화한 객체 지향 쿼리 언어이다.
SQL 문법과 유사하게 SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN을 지원한다.

## QueryDSL
* 문자가 아닌 자바 코드로 JPQL을 작성할 수 있다.
* 컴파일 시점에 문법오류를 찾을 수 있다.
* 동적쿼리 작성하기가 편한다.
* 단순하고 쉽다.
* 실무에 사용을 권장한다.

## 네이티브 SQL
* JPA가 제공하는 SQL을 직접 사용하는 기능
* JPQL로 해결할 수 없는 특정db에 의존적인 기능을 사용할 수 있다.
## JDBC 직접 사용, SpringJdbcTemplate 
* JPA를 사용하면서 JDBC 커넥션을 직접 사용하거나, 스프링 JDBCTemplate를 함께 사용하는 것




# JPQL
---
## JPQL 문법
* select m from Member as m where m.age > 18
* 엔티티와 속성은 대소문자를 구분한다
* JPQL 키워드는 대소문자 구분안한다(select SELECT)
* 위에서 Member는 엔티티 이름이지, 테이블 이름이 아니다. 또한 Member 자체도 엔티티이다.
* 별칭(m 등)은 필수이다. (as는 생략 가능하다.)
## 집합과 정렬
```java
select
	count(m),
	sum(m.age),
	avg(m.age),
	max(m.age),
	min(m.age)
from Member m
```
위와 같은 function들 모두 제공 된다.
## GROUP BY, HAVING, ORDER BY
모두 제공 된다.

## TypeQuery, Query
* TypeQuery는 반환 타입이 명확할 때 사용한다.
* Query: 반환 타입이 명확하지 않을 때 사용한다.

```java
TypeQuery<Member> query = em.createQuery("select m from Member m" ,Member.class)
```
위와 같이 반환 타입이 Member.class로 명확할때 TypeQuery로 바로 받아올 수 있다.

```java
Query query = em.createQuery("select m.username, m.age from Member m")
```
위와 같이 반환 타입이 명확하지 않을 때는(username은 String이고 age는 Int라 명확하지 않다) Query


## 결과 조회
### 1.결과가 리스트일때
```java
List<Member> resultList = query.getResultList();
```
결과가 리스트일때, 결과가 없으면 빈리스트를 반환하기에 NPE를 걱정할 필요 없다,
### 2.결과가 하나일때
```java
Member result = query.getSingleResult();
```
결과가 정확히 하나 일때, 결과가 없으면 NPE가 발생한다.
만약 결과가 하나이어야 하는데, 두 개 이상일 경우에도 에러가 터진다.

참고 SpringJPA DATA를 사용하면, NPE를 반환하지 않고, null을 반환하거나 Optinal을 반환한다.


## 자바 변수는 어떻게 사용?
```java
em.createQuery("select m from Member m where m.userName = :userName" , Member.class)
	.setParameter("userName", "이게 이름이야");
```
위와 같이 :와 함께 명시하고
그에 대하여 setParamenter 해준다.

