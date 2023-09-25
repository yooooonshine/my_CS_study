1. 자바(Java)&객체 지향 프그래밍 정리

## 자바(Java)

썬 마이크로시스템즈에서 개발한 객체지향 프로그래밍 언어

> ###### 자바의 특징
> 
> > 1. ###### 운영체제에 독립적이다.
> >   
> >   - WORA (Write Once, Run Anywhere) :  자바로 작성된 프로그램은 운영체제와 하드웨어에 관계없이 실행 가능하다.
> >     
> >   - JVM을 이용한다
> >     
> > 2. ###### 객체지향언어이다.
> >   
> >   - 상속, 캡슐화, 다형성이 잘 적용된 순수한 객체지향언어
> >     
> >   - 재사용성과 유지보수의 용이
> >     
> > 3. ###### 자동 메모리 관리(Garbage Collection)
> >   
> > 4. ###### 네트워크와 분산처리를 지원한다.
> >   
> > 5. ###### 멀티쓰레드를 지원한다.
> >   
> > 6. ###### 동적 로딩을 지원한다.
> >   

## 객체지향 프로그래밍

1. 프로그래밍에서 필요한 데이터를 추상화
  
2. 상태와 행위를 가진 객체로 만듦
  
3. 객체들간의 상호작용을 통해 로직을 구성하는 프로그래밍 방법.
  

###

#### 객체지향 프로그래밍 장점과 단점

> 장점
> 
> > - 코드의 재사용성이 높다.
> >   
> > - 유지 보수가 쉽다.
> >   
> > - 대형 프로젝트에 적합하다.
> >   
> 
> 단점
> 
> > - 처리 속도가 상대적으로 느리다
> >   
> > - 객체가 많으면 용량이 커질 수 있다.
> >   
> > - 설계 시 많은 시간과 노력이 필요하다.
> >   

### 객체지향 프로그래밍 특징

1. ###### 추상화
  
  - 객체에서 공통된 속성과 행위를 추출 하는 것
    
  - 공통의 속성과 행위를 찾아서 타입을 정의하는 과정
    
  - 추상화는 불필요한 정보는 숨기고 중요한 정보만을 표현함으로써 프로그램을 간단하게 만드는 것
    
2. ###### 캡슐화
  
  - 데이터 구조와 데이터를 다루는 방법들을 결합 시켜 묶는 것
    
    (변수와 함수를 하나로 묶는 것을 뜻함)
    
  - 낮은 결합도를 유지할 수 있도록 설계하는 것
    
  - 속성과 기능을 정의하는 변수와 메소드를 클래스라는 캡슐에 넣어서 분류하는 것으로 재사용이 원활하다는 장점이 있고 캡슐화를 통해서 정보은닉을 활용 할 수도 있다. (접근제어자의 활용)
    
3. ###### 상속
  
  - 클래스의 속성과 행위를 하위 클래스에 물려주거나 하위 클래스가 상위 클래스의
    
    속성과 행위를 물려받는 것
    
  - 새로운 클래스가 기존의 클래스의 데이터와 연산을 이용할 수 있게 하는 기능
    
4. ###### 다형성
  
  - 하나의 변수명, 함수명이 상황에 따라 다른 의미로 해석 될 수 있는 것
    
  - 어떠한 요소에 여러 개념을 넣어 놓는 것
    

###### 오버라이딩

- 상위 클래스가 가지고 있는 메소드를 하위 클래스가 재정의해서 사용하는 것

###### 오버로딩

- 같은 이름의 메서드가 인자의 개수나 자료형에 따라 다른 기능을 하는 것

### 객체지향 설계 원칙 (SOLID)

for 클린 코드를 위한 / 시간이 지나도 유지 보수 및 확장이 쉬울 수 있도록 하기 위한 원칙!

1. ###### 단일 책임 원칙 (SRP, Single Responsibility Principle)
  
  - 하나의 클래스는 단 하나의 책임만 가짐.
    
  - 단일 책임 원칙을 지키지 않을 경우 한 책임의 변경에 의해 다른 책임과 관련된 코드
    
    에 영향이 갈 수도 있음.
    
2. ###### 개방-폐쇄 원칙 (OCP, Open/Closed Principle)
  
  - 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 함.
    
  - 기능을 변경하거나 확장할 수 있으면서 기능을 사용하는 코드는 수정하지 않음.
    
3. ###### 리스코프 치환 원칙 (LSP, Liskov Substitution Principle)
  
  - 프로그램 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있음.
    
  - 상위 타입의 객체를 하위 타입의 객체로 치환해도, 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 함.
    
  - 서브 클래스가 확장에 대한 인터페이스를 준수해야 함
    
4. ###### 인터페이스 분리 원칙 (ISP, Interface Segregation Principle)
  
  - 범용 인터페이스 하나보다 클라이언트를 위한 여러 개의 인터페이스로 구성하는 것이 좋음.
    
  - 인터페이스는 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 함.
    
  - 클라이언트가 필요로 하는 인터페이스로 분리함으로써 각 클라이언트가 사용하지 않는 인터페이스에 변경이 있어도 영향을 받지 않도록 만들어야 함.
    
  - 인터페이스의 단일 책임
    
5. ###### 의존관계 역전 원칙 (DIP), Dependency Inversion Principle)
  
  - 구체화에 의존하면 안됨 >> 추상화에 의존해야 됨.
    
  - 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안되고 저수준 모듈은 고수준 모듈에서 정의한 추상 타입에 의존해야 함.
    

### 자바의 객체 지향

- #### 클래스
  
  1. 맴버 변수 (상태)
    
    클래스의 상태를 나타내는 맴버 변수로 이루어져 있음. 객체가 가질 수 있는 속성임.
    
  2. 생성자
    
    클래스의 생성자는 객체를 초기화할 때 사용됨.
    
  3. 매서드(행동)
    
    객체의 행동을 정의함.
    
  4. Getter 와 Setter 매서드
    
    맴버 변수에 접근하고, 수정하기 위한 매서드. 이를 통해 맴버 변수에 접근 제어 및 데이터 무결성 유지가능
    
- #### 캡슐화
  
  캡슐화를 통해 데이터의 무결성을 유지, 객체 내부 상태를 캡슐화하여 보호할 수 있음.
  
- #### 상속
  
  부모 클래스의 모든 맴버 변수와 매서드를 상속받음.
  
  그러므로 클래스의 모든 맴버 변수와 매서드 호출 가능.
  
- #### 다향성
  
  해당 객체의 오버라이드된 메서드가 실행됨.
  
  동일한 메서드 호출로 인해 서로 다른 객체에 대한 다양한 동작을 구현할 수 있음.
  
- #### 추상화
  
  추상 클래스를 상속받음으로 추상 매서드를 구현 할 수 있음.


## Main

---



```java
public class Main {  
    public static void main(String[] args) {  
        Calculator calculator = new CalculatorImpl();  
  
        System.out.println("1234 + 4321 = " + calculator.plus(1234, 4321));  
        System.out.println("911 - 170 = " + calculator.minus(911, 170));  
        System.out.println("2 * 3 = " + calculator.mul(2, 3));  
        System.out.println("33 / 11 = " + calculator.div(33, 11));  
  
        System.out.println("\n");  
  
        Cat cat = new Cat(); // 다른 동물 하셔도 상관 없습니다!  
        Dog dog = new Dog();  
        Animal[] animals = {cat, dog};  
        for (Animal animal : animals) {  
            animal.speak();  
        }  
    }  
}
```

## Animal
---
```java
public abstract class Animal {  
    public abstract void speak();  
}

```

## Calculator
---
```java
public interface Calculator {  
    int plus(int a, int b); // a + b 값 반환  
    int minus(int a, int b); // a - b 값 반환  
    int mul(int a, int b); // a * b 값 반환  
    int div(int a, int b); // a / b 값 반환  
  
}

```

## CalculatorImpl
---
```java
public class CalculatorImpl implements Calculator {  
  
    @Override  
    public int plus(int a, int b) {  
        return a + b;  
    }  
  
    @Override  
    public int minus(int a, int b) {  
        return a - b;  
    }  
  
    @Override  
    public int mul(int a, int b) {  
        return a * b;  
    }  
  
    @Override  
    public int div(int a, int b) {  
        return a / b;  
    }  
}
```

## Cat
---
```java
public class Cat extends Animal{  
    @Override  
    public void speak() {  
        System.out.println("cat says Meow!");  
    }  
}
```

## Dog
---
```java
public class Dog extends Animal{  
    @Override  
    public void speak() {  
        System.out.println("dog says Woof!");  
    }  
}
```