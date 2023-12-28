## 연관 관계의 주인
* 두 객체 중 하나를 정해서 테이블의 외래키를 가지고 있어야 하며, 외래키를 가진 테이블을 연관 관계의 주인이라고 한다.
* **주인이 아니면 `mappedBy 속성`을 사용해서 속성의 값으로 연관관계의 주인을 지정**
- **주인은 `mappedBy 속성`을 사용하지 않는다.**
- ![[Pasted image 20231119004534.png]]
- 여기서 orders의 주인은 member라는 것이다.
- \# 일대다 관계에서는 무조건 다에 외래키가 존재한다.
- 연관 관계에 주인 쪽에서 값을 변경해야 다른 테이블에 반영된다. 주인이 아닌 쪽에서 변경하려하면 안된다.(즉 단순 조회용으로만 사용된다.)
* 기본 키 할당 전략
	* 직접 할당 방식
		* @Id만 있고 값을 직접 넣어줘야 한다.
	* 자동 생성 방식
		* @Id와 @GeneratedValue를 사용하면 자동으로 할당해준다.
			* 다만 이는 Transaction을 지원하는 쓰기 지연 방식이 동작하지 않는다.
			* DB를 확인해서 존재하는 값을 체크 후 id를 할당해야 하지만, 이는 insert후 넣은 값만 가져오므로 안된다.
## @Column
* `@Column(name = "member_id") private Long id`
* 객체 필드를 테이블의 컬럼과 매칭시켜준다.
* 이 때 name = "member_id"가 없다면 id라는 이름으로 테이블과 매칭하려한다.  table에는 member_id로 되어 있다면, id가 없으므로 에러가 뜨므로 `name = "이름"`으로 바꿔준다.
## 임베디드 타입
* 어떤 domain 객체에서 시작일, 종료일을 값으로 갖고있는 것은 응집력이 떨어지고 좋지않다. 이를 해결하기 위해 이를 하나의 객체로 갖고 있게 되는데 이때 연관성을 표현하는 것이 임베디드 타입이다. 즉 일급컬렉션을 표현하기 위해 사용된다.
* @Embedable: 값 타입을 정의하는 곳에서 표시
* @Embeded: 값 타입을 사용하는 곳에서 표시

## 시퀀스 전략
* id값들은 중복되지 않아야 한다. 이를 위해 내부에서 자동적으로 순차적으로 할당하는 것

## 상속관계 전략
객체에는 상속이 존재한다. 하지만 관계형 데이터베이스에서는 상속관계가 존재하지 않고 비슷한 슈퍼타입과, 서브타입만이 존재한다. 
따라서 jpa에서는 상속관계를 표현하는 방법이 필요하다.
* @Inheritance(strategy = InheritenaceType.xxx)이며 xxx에는 세가지가 들어올 수 있다.
	* JOINED
	* SINGLE_TABLE
		* 슈퍼클래스와 하위 클래스를 한 테이블에 관리. 그럼 하위 클래스들을 서로 구분할 필요가 생긴다. 이를
			* @DiscriminateValue("이름")을 하위 클래스에 붙여준다.
			* @DiscriminatorColumn(name = "dtype") 을 슈퍼클래스에 붙여준다.
	* TABLE_PER_CLASS

## Enum타입을 속성으로 
* @Enumerated(EnumType.ORDINAL), @Enumerated(EnumType.STRING): enum을 사용하는 객체안에 붙여준다.


\# 객체 생성
jpa기본스펙이 reflection등을 필요로 하기에 기본 생성자를 필요로한다. public은 좀 그러고 protected까지 허용한다.
![[Pasted image 20231119005556.png]]


## 가급적 setter,getter는 지양하자.
* getter를 지양하자: 객체지향적 관점에서 값이 노출되는 것은 좋지 못하기에 지양하자
* setter를 지양하자: 값이 변경될 수 있고, 크기가 큰 비즈니스에서는 로직을 변경하려 할때 setter를 찾는 게 쉽지 않다.

## 모든 연관관계는 지연로딩으로 설정
* EAGER는 절대 안된다.  모든 xToOne은 EAGER는 LAZY로 세팅하자.

## 초기화는 생성자에서 말고 선언과 동시에 초기화 하자
![[Pasted image 20231120230110.png]]
이유
* null에 대한 걱정이 없다.
* 영속성 컨텍스트에 넣는 순간 컬렉션을 하이버네이트에 맞게 변경시켜버린다. 즉 하이버네이트가 바꿔놨는데 누군가 set한다면??

## jpa 변수명 전략
* 카멜케이스를 언더스코어로
* 점을 언더스코어로
* 대문자를 소문자

## cascade
![[Pasted image 20231120231339.png]]
양방향 관계에서 일반적으로는 child를 parent에 넣어준 상태에서 child도 영속성 컨텍스트에 넣어줘야 한다. 하지만 이는 번거로우므로 parent넣을 때 한번에 넣게 하는 것이 cascade이다.
![[Pasted image 20231120231322.png]]

다만 cascade는 신중히 사용해야 한다. A가 B, C를 관리하고 B,C는 다른 곳에서 관리 안하는 상황에서 정도만 

## 양방향일때 연관관계 매서드 
```java
member.getOrders().add(order);
order.setMember(memeber);
```
이는 불편하다. 이를 객체 안에 매서드 설정해서 한번에 하도록 하는 것
```java
public void setMember(Member member){
	this.member = member;
	member.getOrders().add(this);
}
```


도메인 안에서 해결할 수 있는 비즈니스로직은 도메인에서 처리!,

생성 메서드를 작성하는 경우 기본 생성자는 protected로 막아주자(private는 스프링부트에 의해 안된다.)
혹은 @NoArgsConstructor(access = AccessLevel.PROTECTED) 로 하자


## 도메인 모델 패턴(domain model pattern)
이는 service에 비즈니스 로직이 있는 게 아니라, entity(domain)에 비즈니스 로직 대부분이 있는 것이다.
즉 service 계층은 entity에 필요한 역할을 위임하는 형태, 즉 시키는 역할.
## 트랜잭션 스크립트 패턴(transaction script pattern)
이는 위와는 반대로 service에서 대부부의 비즈니스 로직을 처리하는 것이다.