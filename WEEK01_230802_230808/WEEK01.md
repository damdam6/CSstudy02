# ~1.1 디자인 패턴(p55)

날짜: August 2, 2023 → August 8, 2023

# 1장 디자인 패턴과 프로그래밍 패러다임

## 1.1 디자인 패턴

참조 url : <https://readystory.tistory.com/114>

### 디자인 패턴이란?

프로그램을 설계할 때 발생했던 문제점을 객체 간의 상호 관계 등을 이용하여 해결할 수 있는 하나의 규약 형태로 만들어 놓은 것

### 왜 디자인 패턴이 필요한가?

- "바퀴를 재발명하지 마라"  
   : 이미 발생한 문제에 대한 해답은 적극적으로 사용하라

- 개발자들간 의사소통 원활

### 디자인 패턴의 종류

- #### 생성 (Creational) 패턴
  - Singleton
  - Abstract Factory
  - Factory Method
  - Builder
  - Prototype
- #### 구조 (Structual) 패턴
  - Adapter
  - Composite
  - Decorator
  - Facade
  - Flyweight
  - Proxy
- #### 행동 (Behavioral) 패턴
  - Command
  - Interpreter
  - Iterator
  - Mediator
  - Memento
  - Observer
  - State
  - Strategy
  - Template Method

### 어떻게 공부하면 좋을까?

- 생성, 구조, 행동 패턴이 각각 어떤 문제들을 해결하는지 파악
- 패턴들 간의 관련성 파악
- 비슷한 목적의 패턴을 모아서 함께 공부
- 반드시 예제 코드 직접 작성

## 1.1.1 싱글톤 패턴

> 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴

어떠한 클래스의 인스턴스가 오직 하나임을 보장ㅎ며, 이 인스턴스를 접근할 수 있는 전역적인 접촉점을 제공하는 패턴입니다. 즉, 프로그램 시작부터 종료시까지 어떤 클래스의 인스턴스가 메모리 상에 단 하나만 존재할 수 있게 하고, 이 인스턴스에 대해 어디에서나 접근할 수 있도록 하는 패턴

### 싱글톤 패턴 고안 배경

개발을 하다보면 어떤 클래스에 대해 단 하나의 인스턴스만을 갖도록 하는 것이 좋은 경우가 있습니다.

예를 들어, 로그를 찍는(Logging) 객체라던가 쓰레드 풀, 윈도우 관리자 등 여러 객체를 관리하는 역할의 객체는 프로그램 내에서 단 하나의 인스턴스를 갖는 것이 바람직합니다.

### 주요 사용 예시

데이터베이스 연결 모듈

### 전역변수와는 어떻게 다른가?

### 단점

싱글톤 패턴은 TDD (Test Driven Development)를 할 때 걸림돌이 된다. TDD의 전제조건인 테스트가 서로 독립적이지 못하다는 문제가 있다. (단위테스트 X) 싱글톤으로 만들어진 객체는 각 테스트마다 독립적이지 못하기 때문이다.

또한 싱글톤 패턴은 모듈간의 결합을 강하게 만드는 단점이 존재한다  
이를 해결하기 위해 의존성 주입(DI, Dependency Innjection)을 통해 모듈 간의 결합을 조금 더 느슨하게 만들 수 있다.

### DI의 구현과 원칙

메인 모듈이 직접 다른 하위 모듈에 대한 의존성을 주기보다 중간에 의존성 주입자를 두어 메인 모듈이 "간접적으로" 의존성을 주입하는 방식. 이로 인해 메인 모듈은 하위 모듈에 대한 의존성이 떨어짐 (디커플링)

의존성 주입은 두가지 원칙이 있다.

1. 상위 모듈은 하위 모듈에서 어떠한 것도 가져오지 않는다.
2. 둘다 추상화에 의존, 세부사항에 의존하지 말아야 한다.

### DI의 장단점

- 장점

  - 모듈을 쉽게 교체할 수 있는 구조가 된다 > 테스팅, 마이그레이션에 유리
  - 구현시 추상화 레이어를 넣고 이를 기반으로 구현체를 넣어줌 > 애플리케이션 의존성 방향이 일관, 모듈간의 관계 명확

- 단점
  - 모듈이 더 분리되면서 클래스 수가 늘어남 > 복잡성 증가
  - 런타임 패널티 발생

## 구현 방법

1. private 생성자만을 정의해 외부 클래스로부터 인스턴스 생성을 차단합니다.

2. 싱글톤을 구현하고자 하는 클래스 내부에 멤버 변수로써 private static 객체 변수를 만듭니다.

3. public static 메소드를 통해 외부에서 싱글톤 인스턴스에 접근할 수 있도록 접점을 제공합니다.

### 싱글톤 구현 코드

총 6가지 방법 정도가 있다고 알려져 있으나 가장 간단한 방법인 Eager Initilization과  
가장 널리 쓰이는 방식인 Bill Pugh Singleton Implementaion 방식을 알아보자

### 1. Eager Initilization

    public class Singleton {

    private static final Singleton instance = new Singleton();

    // private constructor to avoid client applications to use constructor
    private Singleton(){}

    public static Singleton getInstance(){
        return instance;
    }

}

### 특징

싱글톤 클래스의 인스턴스를 클래스 로딩 단계에서 생성하는 방법이다. 그러나 어플리케이션에서 해당 인스턴스의 사용 유무와 관련 없이 인스턴스를 생성하여 낭비가 있을 수 있다. 또한 Exception에 대한 Handling을 제공하지 않는다.

### 2. Bill Pugh Singleton Implementation

    public class Singleton{
        private Singleton(){}
        private static class SingletonHelper{
            private static final Singleton INSTANCE = new Singleton();
        }

        public static Singleton getInstnace(){
            return SingletonHelper.INSTANCE;
        }

    }

### 특징

private inner static class를 두어 싱글톤 인스턴스를 갖게한다. SingletonHelper 클래스는 Singleton 클래스가 load될 때도 Load 되지 않다가 getInstance()가 호출되었을 때 비로소 JVM 메모리에 로드되고, 인스턴스를 생성하게 된다. 아울러 **synchronized를 사용하지 않기 때문에 성능 저하에 유리**하다.  
실제로 Java 오픈소스 프로젝트의 코드를 살펴보면 이와 같은 방식으로 싱글톤을 사용하고 있는 것을 알 수 있다.

## 1.1.2 팩토리 패턴

> 객체를 사용하는 코드에서 객체 생성을 떼어다 추상화한 패턴
>
> > 상위 클래스가 중요한 뼈대를 결정, 하위 클래스가 객체 생성에 관한 구체적 내용을 결정 하는 패턴

예를 들어 카페(공장)라는 상위 클래스가 존재하고, 에스프레소, 에이드, 차 레시피라는 구체적인 내용이 들어있는 하위 클래스가 컨베이어 벨트를 통해 상위 클래스로 전달되어 이를 토대로 생산한다고 비유할 수 있다.

생성 패턴 중 하나로 생성 패턴에는 크게 2가지 중요한 이슈가 있다.  
 1. 생성 패턴은 시스템이 어떤 Concrete Class를 사용하는지에 대한 정보를 캡슐화  
 2. 생성패턴을 이들 클래스의 인스턴스들이 어떻게 만들어지고 어떠헥 결합하븐지에 대한 부분을 완전히 가려줌

팩토리 패턴은 여러 개의 서브 클래스를 가진 슈퍼 클래스가 있을 때 인풋 하나에 따라 하나의 자식 클래스의 인스턴스를 리턴해주는 방식이다. 팩토리 패턴에서는 클래스의 인스턴스를 만드는 시점을 하위 클래스로 미루고, 인스턴스화에 대한 책임을 객체를 사용하는 클라이언트에서 팩토리 클래스로 가져온다.

### 활용

1. 어떤 클래스가 자신이 생성해야 하는 객체의 클래스를 예측할 수 없을 때
2. 생성할 객체를 기술하는 책임을 자신의 서브클래스가 지정했으면 할 때

### 활용 예시

1. ava.util 패키지에 있는 Calendar, ResourceBundle, NumberFormat 등의 클래스에서 정의된 getInstance() 메소드가 팩토리 패턴을 사용하고 있습니다.
2. Boolean, Integer, Long 등 Wrapper class 안에 정의된 valueOf() 메소드 또한 팩토리 패턴을 사용했습니다.

### 장점

상위 클래스와 하위 클래스가 분리되어 느슨한 결합을 가지고, 상위 클래스는 객체 생성에 관여하지 않아 유연하다. 객체 생성 로직이 따로 떼어져 있음으로 코드 리팩터링이 쉬워 유지보수가 용이하다.

## 구현 방법 (예제 코드)

    //상위 클래스
    abstract class Coffee {
    public abstract int getPrice();

    @Override
    public String toString(){
        return "Hi this coffee is "+ this.getPrice();
    }
    }
    // 팩토리 클래스
    class CoffeeFactory {
    public static Coffee getCoffee(String type, int price){
        if("Latte".equalsIgnoreCase(type)) return new Latte(price);
        else if("Americano".equalsIgnoreCase(type)) return new Americano(price);
        else{
            return new DefaultCoffee();
        }
    }
    }
    //하위 클래스들
    class DefaultCoffee extends Coffee {
    private int price;

    public DefaultCoffee() {
        this.price = -1;
    }

    @Override
    public int getPrice() {
        return this.price;
    }
    }
    class Latte extends Coffee {
    private int price;

    public Latte(int price){
        this.price=price;
    }
    @Override
    public int getPrice() {
        return this.price;
    }
    }
    class Americano extends Coffee {
    private int price;

    public Americano(int price){
        this.price=price;
    }
    @Override
    public int getPrice() {
        return this.price;
    }
    }

    public class HelloWorld{
     public static void main(String []args){
        Coffee latte = CoffeeFactory.getCoffee("Latte", 4000);
        Coffee ame = CoffeeFactory.getCoffee("Americano",3000);
        System.out.println("Factory latte ::"+latte);
        System.out.println("Factory ame ::"+ame);
     }
    }
    /*
    Factory latte ::Hi this coffee is 4000
    Factory ame ::Hi this coffee is 3000
    */

Coffee라는 상위 클래스가 뼈대를 결정, 하위 클래스인 Latte가 구체적인 내용을 결정하고 있다. 이는 의존성 주입이라고 볼 수 있다. 상위 클래스에서 하위클래스의 인스턴스를 생성하는 것이 아닌, 하위 클래스에서 생성한 인스턴스를 상위 클래스로 주입하기 때문이다.

또한 getCoffee()를 static으로 정의했는데, 정적 메서드를 쓰면 인스턴스 없이 호출이 간으하여 메모리를 절약할 수 있고, 개별 인스턴스에 묶이지 않으며 클래스 내의 함수를 정의할 수 있는 장점이 있다.

## 1.1.3 전략 패턴 (Strategy Pattern)

> 객체의 행위를 바꾸기 위해 이를 '직접' 수정하지 않고 전력이라고 하는 "캡슐화된 알고리즘"을 컨텍스트 안에서 바워주면서 상호 교체가 가능하도록 하는 패턴

### 활용

1. Java Comparator의 compare(), Collection.sort()
2. Node.js의 passport 라이브러리

## 구현

    import java.text.DecimalFormat;
    import java.util.ArrayList;
    import java.util.List;

    // 전략 클래스 캡슐화를 위한 인터페이스 생성
    interface PaymentStrategy {
        public void pay(int amount);
    }

    // 전략 1
    class KAKAOCardStrategy implements PaymentStrategy {
        private String name;
        private String cardNumber;
        private String cvv;
        private String dateOfExpiry;

        public KAKAOCardStrategy(String nm, String ccNum, String cvv, String expiryDate){
            this.name=nm;
            this.cardNumber=ccNum;
            this.cvv=cvv;
            this.dateOfExpiry=expiryDate;
        }

        @Override
        public void pay(int amount) {
            System.out.println(amount +" paid using KAKAOCard.");
        }
    }

    //전략 2
    class LUNACardStrategy implements PaymentStrategy {
        private String emailId;
        private String password;

        public LUNACardStrategy(String email, String pwd){
            this.emailId=email;
            this.password=pwd;
        }

        @Override
        public void pay(int amount) {
            System.out.println(amount + " paid using LUNACard.");
        }
    }

    class Item {
        private String name;
        private int price;
        public Item(String name, int cost){
            this.name=name;
            this.price=cost;
        }

        public String getName() {
            return name;
        }

        public int getPrice() {
            return price;
        }
    }

    class ShoppingCart {
        List<Item> items;

        public ShoppingCart(){
            this.items=new ArrayList<Item>();
        }

        public void addItem(Item item){
            this.items.add(item);
        }

        public void removeItem(Item item){
            this.items.remove(item);
        }

        public int calculateTotal(){
            int sum = 0;
            for(Item item : items){
                sum += item.getPrice();
            }
            return sum;
        }

        // 전략 사용하여 pay
        public void pay(PaymentStrategy paymentMethod){
            int amount = calculateTotal();
            paymentMethod.pay(amount);
        }
    }

    public class HelloWorld{
        public static void main(String []args){
            ShoppingCart cart = new ShoppingCart();

            Item A = new Item("kundolA",100);
            Item B = new Item("kundolB",300);

            cart.addItem(A);
            cart.addItem(B);

            // 여러가지 전략 사용
            // pay by LUNACard
            cart.pay(new LUNACardStrategy("kundol@example.com", "pukubababo"));
            // pay by KAKAOBank
            cart.pay(new KAKAOCardStrategy("Ju hongchul", "123456789", "123", "12/01"));
        }
    }
    /*
    400 paid using LUNACard.
    400 paid using KAKAOCard.
    */

이후 추가 페이 기능이 생기거나 수정해야 할 때 코드 수정이 매우 용이해진다

## 1.1.4 옵저버 패턴

> this is block quote
>
> > this is second block code
> >
> > > this is third block code

- 안녕

* 잘 봐봐

- 다 똑같아!

코드블록은

    이렇게 중간을 띄어써서

한다

수평선은

<hr>
이렇게 한다

링크는 : <https://www.naver.com> 이렇게 넣어준다  
줄바꿈을 하려면 이렇게 띄어쓰기를  
세칸 이상해야한다  
요로코롬

강조는  
_single asterisks_  
_single underscores_  
**double asterisks**  
**double underscores**  
~~cancelline~~  
이렇게
