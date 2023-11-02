* ### org.junit.Assert.assertThat
	* junit에서 지원하는 assertThat이다.
	* 기본 형태는 아래와 같다
	```java
	public static <T> void assertThat(T actual, Matcher<? super T> matcher)
	```
	* 첫번째 매개변수로 비교할 값
	* 두번째 매개변수로 비교하는 로직(matcher)를 넣는다.
	* matcher를 직접 작성하는 것은 좋지 못하고 주어진 것을 사용하자.
	* 이는 기능적으로 불편하여 assertj의 assertThat을 주로 사용한다.
	
* ### org.assertj.core.api.Assertions.assertThat
	* assertThat(타겟).메소드().메소드() 의 메소드 체이닝 패턴 형식으로 사용한다.
	* 사용될 수 있는 메소드로는 `isEqualTo(e)`, `contains(e)`, `doesNotContain(e)`, `startsWith(e)`, `endsWith(e)`, `isNotEmpty()`, `isPositive(n)`, `isGreaterThan(n)` 등이 있다.
	* 값 비교
		* assertThat(a).isEqualTo(b);
			* a와 b의 참조가 같은 지
		* assertThat(a).isEqualToComparingFieldByFieldRecursively(b);
			* a와 b의 필드 값이 같은지 
	* Boolean 비교
		* assertThat(a).isTure();
		* assertThat(a).isFalse();
		* 
	* assertThat(a).contains(b);
		* a가 b를 포함하는 지
		* iterable,array,assertion 모두가능
	* assertThat(a).isNotEmpty();
		* 리스트가 비어있지 않은 지
		* iterable,array,assertion 모두가능
	* assertThat(a).doesNotContain(b);
		* a가 b를 포함하지 않는 지
		* iterable,array,assertion 모두가능
	* assertThat(a).startsWith(b);
		* a가 b로 시작하는지
		* iterable,array,assertion 모두가능
	* assertThat(a).endsWith(b);
		* a가 b로 끝나는 지
		* iterable,array,assertion 모두가능
	* assertThat(a).isNotEmpty(b);
		* iterable,array,assertion 모두가능
	* assertThat(a).isPositive(b);
	* assertThat(a).isGreaterThan(b);
	* assertThat(result).contains("2", "1");  
		* result가 2, 1을 포함하는지
	* assertThat(result).containsExactly("1", "2");
		* result가 1,2만 있는지
	* assertThatThrownBy
		* 예시
		```java
		    assertThatThrownBy(() -> input.charAt(input.length()).isInstanceOf(StringIndexOutOfBoundsException.class);
		```
		* 익셉션이 발생하는 케이스를 테스트할 때 사용
			* isInstanceOf(a)
				* 예상되는 예외를 a에 입력
			* hasMessageContaining(a)
				* 예외 메세지에 a가 포함되어 있는 지
## 정상 작동하는 지 확인 하는 방법
```java
Assertions.assertThatCode(  
        () -> Validator.randomNumberRange(randomNumber)  
).doesNotThrowAnyException();
```
assertThatCode().doesNotThrowAnyException을 이용하면 예외가 안터졌을 때 테스트를 통과한다.

## 출력문을 테스트 하고 싶을 때
* system.setOut을 통해 콘솔 출력



## PrameterizedTest
* ValueSource
```java
@ParameterizedTest 
@ValueSource(strings = {"q", "qwerasdfzxcv", "qq23"}) void createUserException(String name){
}
```
valueSource는 리터럴 값의 단일 배열을 지정할 수 있다. 매개 변수화 된 테스트 호출마다 단일 인수를 제공하는 데 사용
`@ValueSource`는 다음 유형의 리터럴 값을 지원합니다.

- `short`, `byte`, `int`, `long`, `float`, `double`, `char`, `boolean`,
- `java.lang.String`, `java.lang.Class`
`@ValueSource` 안에 `ints`, `strings` 등 원하는 타입을 적어준 뒤 리터럴 값을 넣어주면 됩니다.

* Null 과 Empty Sources
`@NullSource` `@EmptySource`를 사용하면 파라미터 값으로 null과 empty를 넣어줍니다.

* csvsource
만약 값과 그에 대한 결과값을 같이 파라미터로 주고 싶다면 csvSource를 사용하면 된다.
CsvSource(value = "입력, 결과")등으로 사용하면 된다.
```java
@ParameterizedTest  
@CsvSource(value = {"3,false","4,true","5,true"})  
@DisplayName("4이상에만 이동할지 결정하는 지 테스트")  
void 이동_결정_테스트(int randomNumber, boolean result) {
Assertions.assertThat(Car.decideToMove(randomNumber)).isEqualTo(result);  
}  
```
## AssertThat이란

## Give, When, Then 패턴
* 각각 준비, 실행, 검증이다.
* 


## Mockito
* 여러 논리가 섞인 메소드를 테스트할 때에 사용한다. 쉽게 말하자면 특정 메소드가 의존성을 가져, 여러 논리로 A라는 결과를 도출한다면, 우리는 의존성이 갖는 부분을 mock으로 만들어 내용을 무시하고 A라는 결과를 도출하게 만드는 것이다.
