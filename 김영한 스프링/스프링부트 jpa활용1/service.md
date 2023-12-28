repository에서 만든 메서드들을 사용하여 실제 서비스에서 사용하는 메서드 로직을 작성한다.

서비스는 주로 엔티티 연결, 호출을 위임하는 정도 한다. 비즈니스 로직은 엔티티 안에서 하자

모든 데이버베이스 정보 변경등은 앞에 @transactional 이 붙어야한다.
* @Transactional(readOnly = true)는 부하를 주지말고 읽기 전용으로 쓴다고 메서드 위에 붙여준다.
* 클래스 위에 붙이면 모든 메서드에 적용되지만, 특정 메소드는 다른 방식으로 하고 싶다면 그 메서드 위에만 따로 붙여주면된다. 그래서 많은 부분이 readOnly라면 클래스 위에 readOnly를 붙여주자.

## 수정
수정에는 변경 감지와 병합이라는 두 가지 방법이 존재한다.
그 중 변경 감지가 jpa가 권유하는 방법이다.

기본 변경 감지 메커니즘은 데이터를 수정하고 별도의 update문을 날리지 않아도 EntityManager가 dirtyChecking을 통해 변경을 감지하고 flush때 알아서 update문을 날린다.


---
### 준영속 엔티티란?
영속성 컨텍스트가 더 이상 관리하지 않는 엔티티

예를 들어 db에 Book이라는 객체를 1이라는 id를 통해 만들어서 넣었다고 하자.
나중에 다시 Book이라는 객체를 1이라는 id를 통해 만든다면 이는 영속성 컨텍스트가 관리하지 않는다.

---
준영속 엔티티는 jpa가 관리하지 않기에 수정에서 문제가 생긴다.

그럼 준영속 엔티티는 어떻게 수정해야 할까

## 준영속 엔티티를 수정하는 2가지 방법
* 변경 감지 기능 사용
* 병합(merge) 사용
## 변경 감지 기능 사용
```java
@Transactional
void update(Item itemParam) {
	Item findItem = em.find(Item.class, itemParam.getId()); // 같은 엔티티를 조
	findItem.setPrice(itemParam.getPrice()); //데이터 수정
}
```
위처럼 `em.find(`)를 통해 item을 가져오면 영속 상태가 되기에, 따로 save등을 하지 않아도 영속성 컨텍스트가 알아서 update를 해준다.

## 병합 사용
준영속 상태의 엔티티를 이용해 영속상태 엔티티를 만들어 사용하는 기능
```java
@Transactional void update(Item itemParam) { //itemParam: 파리미터로 넘어온 준영속 상태의 엔티티 
	Item mergeItem = em.merge(itemParam); 
}
```
여기서 `em.merge`는 모든 필드를 `itemParam`으로 변경해주는 기능이다.
그럼 `em.merge`는 영속된 엔티티를 반환해 준다.
즉 `itemParam`의 엔티티는 `mergeItem`과 다른 것이다.
즉 더 쓸일이 있다면 `mergeItem`을 사용해야 한다.

## 주의
### 변경감지를 사용하자
다만 변경 감지 기능은 원하는 속성만 선택해서 변경할 수 있지만, 병합을 사용하면 모든 속성이 변경되고 병합시 값이 없으면 `null`로 업데이트 되므로 위험하다.

따라서 웬만하면 병합을 사용하지말고 변경감지를 사용하자!

### 컨트롤러에서 어설프게 엔티티를 생성하지 말자
```java
@PostMapping("items/{itemId}/edit")  
public String updateItem(@PathVariable String itemId, @ModelAttribute("form") BookForm form) {  
  
    Book book = new Book();  
    book.setId(form.getId());  
    System.out.println("item의 id: " + form.getId());  
    book.setName(form.getName());  
    book.setPrice(form.getPrice());  
    book.setStockQuantity(form.getStockQuantity());  
    book.setAuthor(form.getAuthor());  
    book.setIsbn(form.getIsbn());  
  
    itemService.saveItem(book);  
    return "redirect:/items";  
}
```
위 코드는 `controller`에서 `Book`엔티티를 생성해서 사용한 것이다.
이렇게 사용하지 말고

```java
//itemController
@PostMapping("items/{itemId}/edit")  
public String updateItem(@PathVariable Long itemId, @ModelAttribute("form") BookForm form) {  
  
    itemService.updateItem(itemId, form.getName(), form.getPrice(), form.getStockQuantity());  
    return "redirect:/items";  
}
```
```java
//itemService
@Transactional  
public void updateItem(Long itemId, String name, int price, int stockQuantity) {  
    Item findItem = itemRepository.findOne(itemId);  
    findItem.setPrice(price);  
    findItem.setName(name);  
    findItem.setStockQuantity(stockQuantity);  
}
```
위와 같이 service에서 처리하도록 하게 하자

참고로 여기서도 Setter를 쓰지말고 차라리 findItem에 메서드를 만들어 사용하자
setter는 유지보수에 좋지 못하다.