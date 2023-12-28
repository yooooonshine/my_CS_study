## jpql
기존 sql은 테이블을 대상으로 쿼리를 하지만, jpql은 객체를 대상으로 쿼리를 한다.
```java
	em.createQuery("select m from Member m", Member.class);
```

## 사용방법
* 상단에 @Repository 적어서 bean으로 등록되게 하자
* @PersistenceContext private EntityManager em; 을 통해 영속성 컨텍스트를 주입해주자
* 만약에 직접 엔티티 팩토리를 구성하고 싶다면 @PersistenceUnit private EntityManagerFactory emf; 를 통해 직접 주입가능


## em.persist()
persist를 한다고 해서 insert문이 되는 것은 아니다. 데이터베이스 트랜잭션이 커밋 될 떄 나가기 때문이다. 데이터베이스에 반영하고 싶다면 em.flush() 를 하면 된다.


# 동적 쿼리
사용자의 입력에 따라, 쿼리문이 다르게 작동해야 한다면 어떡해야 할까
예로 `orderStatus` 와 사용자의 입력 두가지의 있고 없음에 따라 총 4가지의 상황에 맞게 다른 쿼리문이 필요하다.

## 1. 생으로 쿼리문을 만들어준다.
```java
public List<Order> findAllByString(OrderSearch orderSearch) {  
    //language=JPAQL  
    String jpql = "select o From Order o join o.member m";  
    boolean isFirstCondition = true;  
    //주문 상태 검색  
    if (orderSearch.getOrderStatus() != null) {  
        if (isFirstCondition) {  
            jpql  
                    += " where";  
            isFirstCondition = false;  
        } else {  
            jpql += " and";  
            jpql += " o.status = :status";  
        }  
    }  
    //회원 이름 검색  
    if (StringUtils.hasText(orderSearch.getMemberName())) {  
        if (isFirstCondition) {  
            jpql += " where";  
            isFirstCondition = false;  
        } else {  
            jpql += " and";  
            jpql += " m.name like :name";  
        }  
    }  
    TypedQuery<Order> query = em.createQuery(jpql, Order.class)  
            .setMaxResults(1000); //최대 1000건  
    if (orderSearch.getOrderStatus() != null) {  
        query = query.setParameter("status", orderSearch.getOrderStatus());  
    }  
    if (StringUtils.hasText(orderSearch.getMemberName())) {  
        query = query.setParameter("name", orderSearch.getMemberName());  
    }  
    return query.getResultList();  
}
```
위와 같이, 조건에 따라 동적으로 쿼리문을 추가해주는 것이다.
매우 로직이 복잡해진다.

## 2. jpa에서 표준으로 제공해주는 criteria를 이용한다.
```java
public List<Order> findAllByCriteria(OrderSearch orderSearch) {  
    CriteriaBuilder cb = em.getCriteriaBuilder();  
    CriteriaQuery<Order> cq = cb.createQuery(Order.class);  
    Root<Order> o = cq.from(Order.class);  
    Join<Order, Member> m = o.join("member", JoinType.INNER); //회원과 조인  
    List<Predicate> criteria = new ArrayList<>();  
    //주문 상태 검색  
    if (orderSearch.getOrderStatus() != null) {  
        Predicate status = cb.equal(o.get("status"),  
                orderSearch.getOrderStatus());  
        criteria.add(status);  
    }  
    //회원 이름 검색  
    if (StringUtils.hasText(orderSearch.getMemberName())) {  
        Predicate name =  
                cb.like(m.<String>get("name"), "%" +  
                        orderSearch.getMemberName() + "%");  
        criteria.add(name);  
    }  
    cq.where(cb.and(criteria.toArray(new Predicate[criteria.size()])));  
    TypedQuery<Order> query = em.createQuery(cq).setMaxResults(1000); //최대 1000건  
    return query.getResultList();  
}
```
이거는 1의 방법에서 `and`, `where`등의 쿼리문을 알아서 criteria가 처리해준다는 장점이 있다.
다만, 이를 보고 무슨 쿼리문이 생성될 지 아예 감이 안오기 때문에, 실무에 적합하지 않고, 유지보수가 힘들다.

## 쿼리 dsl을 이용한다.
