## 상단에 붙여줄 것
* @RunWith(SpringRunner.class)
* @SpringBootTest
* @Transactional
	* 트랜잭셔널은 자동으로 롤백을 해준다. 롤백을 하기 싫다면 메서드에 @Rollback(false) 를 붙여주자.
* 기본적으로 롤백을 하기 때문에 db에 넣고 싶다면, em.flush() 를 통해 영속성 컨텍스트 내용을 db에 넣어주자.

## 인메모리 db
존재하는 데이터베이스를 가지고 테스트하는 것은 불편하다. 따라서 text패키지안에 따로 resources 패키지를 만들고 application.yml을  복사한다음 url을 아래와 같이 바꿔주면 인메모리로 동작한다.
`url: jdbc:h2:mem:test`