* main함수는 public이어야한다
	* 메인함수는 어디서나 접근 가능한 함수여야 한다.
	* 만약 private나 protected라면 그게 불가능하므로 public이다.
* static
	* ![[Pasted image 20231021211537.png]]
		* static은 앞에 static을 붙여주고
		* heap은 new 생성자 등으로 만든다.
		* 메인 함수는 gabage collector에 죽으면 프로그램이 망가지니 static으로 설정된다.
* void
	* 메인함수가 끝나면 프로그램이 종료되기에 return값이 있어서는 안된다.
* String args\[]
	* 메인 함수는 처음으로 실행되는 함수이기에, 외부로부터 값을 받아올 수 있어야 한다.
	* 그래서 외부에서 받아오기 위해 String args[]를 사용하는 것이다.
	* 이 때 배열인 이유는 여러 값을 받아올 수도 있기에