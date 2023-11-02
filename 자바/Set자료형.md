set 자료형 특징
* 중복된 값은 허용되지 않는다
* 특정한 순서를 가지고 있지 않다.
* index로 값을 가져올 수 없다.
  
시간복잡도
* add -> O(1)
	* add가 O(1)인 이유는 해쉬 값을 통해 바로 중복을 확인하기 때문이다.
* contains -> O(1)
	* get이 O(1)인 이유는 값을 해쉬해서 해쉬포인트로 확인하기 때문이다.
* next -> O(h/n)
```java
	HashSet<String> set = new HashSet<String>();
	set.add("a");
	set.add("a");

```