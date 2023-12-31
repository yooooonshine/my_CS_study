# 불필요한 객체 생성을 피하라
---
똑같은 기능의 객체보다는 객체 하나를 재사용하는 편이 나을 때가 많다.
## 1. String
예를 들어
```java
String s = new String("a");
```
의 경우 위 문장을 호출할 때마다 새로운 객체를 생성하게 된다.
```java
String s = "a";
```
하지만 java의 경우 String은 따로 관리하여 모든 "a"라는 String은 하나의 객체만 만들어서 동일한 객체를 호출하게 하므로 첫 번째와 같이 new를 통해 매번 "a"를 생성하는 것은 멍청한 짓이다.

## 2.KeySet
두 번째 예로 Map 인터페이스의 KeySet 메서드이다.
Map 인터페이스의 KeySet메서드는 Map에 존재하는 모든 Key들을 포함한 Set뷰를 반환한다. 

여기서 view(adapter)는 실제 작업은 뒷단 객체에 위임하고 자신은 인터페이스 역할만 하는 객체이다. 즉 view는 뒷단 객체만 관리한다.

이어서 한 Map에서 여러번 keyset을 호출하였다 하더라도 여러 set뷰가 만들어지는 게 아니다. 오직 하나의 객체가 만들어져 동일한 객체가 리턴된다. 따라서 호출한 여러 객체중 하나를 변경하면 나머지도 변경된다. 왜냐하면 같은 객체이니까.

따라서 keySet은 뷰 객체를 여러개 만들지 않고 하나만 만드므로, 불필요한 객체를 여러개 만들지 않는 예시 중 하나이다.
## 3. 오토박싱
오토박싱이란 Long과 long과 같이 기본타입과 박싱된 기본 타입을 프로그래머가 섞어 쓸 때 자동으로 상호 변환해주는 기술이다. 
하지만 기본타입과 박싱된 기본 타입은 명확히 다르며, 분명히 구분이 존재한다.
아래 예시를 보자
```java
private Static long sum() {
	Long sum = 0L;
	for(long i = 0; i <= Integer.MAX_VALUE; i++) {
		sum += i;
	}
}
```
위 코드를 보면 sum객체는 Long 객체이고 long이 아니다. 
그러므로 sum += i를 할 때마다 새로운 객체를 생성하게 되므로 Integer의 최대 값인 2^31까지 총 2^31번 객체를 생성하므로 최악의 성능을 만들어 낸다.
하지만 Long대신에 long을 사용한다면 단일 객체를 사용하므로 문제가 되지 않는다.

따라서 웬만하면 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.


요약하면 똑같은 기능을 하면서 불변 객체라면 객체 하나를 재활용하는 것이 좋다는 것이다.
하지만 여기서 주의할 점이 무조건 객체 생성을 피하라는 것은 아니다. 요즘은 작은 객체를 만드는 데에 비용이 거의 들지 않는다.
또한 객체 풀(pool)을 만드는 것도 좋지 않은 경우가 많다. 예를 들어 데이터베이스 연결을 위한 객체를 만들 때는 비용이 너무 커서 객체 풀에 연결 객체를 넣어 재활용한다.
하지만 이는 비용이 아주 클 때이며, 대부분의 객체는 객체 풀을 만드는 것보다, 새로 생성하는 게 더 적은 비용이 든다.
