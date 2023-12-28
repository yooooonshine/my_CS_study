# 타임리프
---
controller에서 모델을 통해 객체(MemberForm등의 DTO)를 넘기면,
클라이언트 쪽에서 이에 데이터를 실어 다시 서버로 넘길 수 있다.

이때 `form`태그에
```html
<form role="form" action="/members/new" th:object="${memberForm}" method="post">
```
위와 같이 th라는 타임리프를 의미하는 문법 뒤에, object로 memberForm을 받겠다고 명시한다.

```html
<input type="text" th:field="*{city}" class="form-control" placeholder="도시를 입력하세요">
```
여기서 field는 태그의 id와 name을 city로 만들어 준다. 물론 이는 memberFomr에 있어야 한다.

web에서는 memberForm에 있는 필드를 getter,setter로 가져오므로 getter, setter가 MemberForm에 있어야 한다.

## dto
dto에는 @NotEmpty같은 애노테이션을 적을 수 있다. 그럼 비어있지 않는 필드를 요구한다는 것인데

예를 들어
```java
@Getter @Setter  
public class MemberForm {  
  
    @NotEmpty(message = "회원 이름은 필수 입니다")  
    private String name;  
  
    private String city;  
    private String street;  
    private String zipcode;  
}
```
위와 같이 dto 필드에 `@NotEmpty`를 붙여 준다면, 
```java
@PostMapping("memebers/new")  
public String create(@Valid MemberForm form) {  
    Address address = new Address(form.getCity(), form.getStreet(), form.getZipcode());  
  
    Member member = new Member();  
    member.setName(form.getName());  
    member.setAddress(address);  
  
    memberService.join(member);  
    return "redirect:/";  
}
```
위와 같이 `@Valid`애노테이션을 명시해주면 자동으로 validation해준다.

만약 이렇게해서 validation에서 error가 검출되면
springboot가 기본적으로 제공하는 Whitelabel Error Page로 리다이렉트된다.
즉 `/error`로 리다이렉트된다.

그럼 error가 났을 때 클라이언트한테 띄울려면 어떻게 해야할까
```java
@PostMapping("memebers/new")  
public String create(@Valid MemberForm form, BindingResult result) {  
    if (result.hasErrors()) {  
        return "members/createMemberForm";  
    }  
      
    Address address = new Address(form.getCity(), form.getStreet(), form.getZipcode());  
  
    Member member = new Member();  
    member.setName(form.getName());  
    member.setAddress(address);  
  
    memberService.join(member);  
    return "redirect:/";  
}
```
위와 같이 기존의 `@Valid MemberForm form`옆에 `BindingResult result`를 명시하면
에러에 관한 여러정보가 `result`에 담기고, `result.hasError()`로 확인해 정보를 페이지로 간다.
그럼 `Model`과 같이, `BindingResult`객체가 html로 넘어가고
```java
<div class="form-group">  
    <label th:for="name">이름</label>  
    <input type="text" th:field="*{name}" class="form-control" placeholder="이름을 입력하세요"  
           th:class="${#fields.hasErrors('name')}? 'form-control fieldError' : 'form-control'">  
    <p th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Incorrect date</p>  
</div>
```
여기서 `${#fields.hasErrors('name')}`을 통해 name에 에러가 있으면 `form-control`클래스에 `form-control fielError`등으로 변경하라고 할 수 있으며,
또한 p태그처럼 `th:if="${#fields.hasErrors('name')}"`과 같이 name필드에 에러가 있으면,
`th:errors="*{name}"`을 통해 errormessage를 뽑아서 text로 넣어준다.
## 반복해서 출력하기
특정 dto에 있는 list를 html에 반복적으로 뿌릴려면 어떻게 해야할까?
```java
@GetMapping("/members")  
public String list(Model model) {  
    model.addAttribute("members", memberService.findMembers());  
    return "members/memberList";  
}
```

```html
<tr th:each="member : ${members}">  
    <td th:text="${member.id}"></td>  
    <td th:text="${member.name}"></td>  
    <td th:text="${member.address?.city}"></td>  
    <td th:text="${member.address?.street}"></td>  
    <td th:text="${member.address?.zipcode}"></td>  
</tr>
```
위와 같이,  members라는 객체를 html로 넘기고
html에서 `th:each="member : ${members}"`등으로 forEach문을 members에 대해 돌릴 수 있다.

참고로 여기서 `?`는 address가 null이면 출력 안하도록 하는 용도이다.


## 중요
* api를 만들 때는 절대 entity를 반환해서는 안된다. 무조건 dto를 만들어서 반환해야 한다.