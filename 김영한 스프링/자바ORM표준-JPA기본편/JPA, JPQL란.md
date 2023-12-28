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