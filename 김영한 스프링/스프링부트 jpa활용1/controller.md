## 로그
### 방법1
로그를 찍기 위해서는 controller 클래스에 
`Logger log = LoggerFactory.getLogger(getClass());`
log를 `Logger`객체를 만들어준다(참고로 이건 slf4j에 있는 것이다.)
### 방법2
`controller`클래스 상단에 `@Slf4j`를 `import`해준다.

이렇게 로그를 추가했다면
각 메서드에
`log.info("이름")`등으로 사용하면 된다.




## 모든 controller는 service를 거쳐야할까?
아니다. 매우 단순한 기능이면 service를 거치지 않고 바로 repository를 이용해도 무관하다.
즉 단순하게 찾는 기능이면 repository를 바로 접근해도 무관하고
만약 쓰기 기능(@Transactional을 필요로 하는)이면 반드시 서비스 계층을 통해야 한다.

## @ModelAttribute
```java
@GetMapping("/orders")  
public String orderList(@ModelAttribute("orderSearch") OrderSearch orderSearch, Model model) {  
    List<Order> orders = orderService.findOrders(orderSearch);  
    model.addAttribute("orders", orders);  
  
    return "order/orderList";  
}
```
위와 같이 `@ModelAttribute`를 사용한다면 `model.addAttribute`를 사용하지 않아도, 자동으로 model에 등록된다.uu