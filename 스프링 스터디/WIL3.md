* 1. SpringBean 이란 무엇인가?
	* 스프링 컨테이너에 의해 관리되는 객체
	* @Bean 을 통해 등록할 수도 있고, @Component를 통해 자동으로 빈을 등록하게 할 수도 있다.
* 2. SpringBean을 등록하는 방법을 정리하라
	* #### 수동빈등록
		* 스프링에서 빈을 수동으로 등록하려면 설정 class 위에 @Configuration을 적고 @Bean을 이용하여 수동으로 빈을 등록한다.
	* #### 자동 빈등록
		* 1. @ComponentScan 이용
			* @component @Autowired를 이용하여 등록한다. 
			* @Configuraton, @ComponentScan 담긴 클래스가 있고, 구현체에 @Component와 @Autowired를 넣으면 된다.
		* 2. @Component를 포함하는 @Controller, @Service, @Repository를 사용한다.
		* 3. @SpringBootApplication 이용, 이안에는 `@EnableAutoConfiguration`, `@ComponentScan` 세 가지가 존재한다. 
			* 세가지
				* @EnableAutoConfiguration
					* 스프링 부트의 Application Context 설정을 자동으로 수행한다는 어노테이션으로, `META-INF/spring.factories`에 정의되어 있는 configuration 대상 클래스들을 빈으로 등록한다.
				*  `@ComponentScan` 
					* components, service, configurations 들을 찾는다.
			* SpringBootApplication 애노테이션이 붙은 클래스안의 main 메소드는 SpringApplication.run() 를 사용한다. 즉 애플리케이션을 실행하라는 뜻이다
* 3. ![[Pasted image 20231002235711.png]]
* ![[Pasted image 20231002235720.png]]