# ~1.1 디자인 패턴(p55)

날짜: August 2, 2023 → August 8, 2023

# 1장 디자인 패턴과 프로그래밍 패러다임
<hr>
## 1.1 디자인 패턴

참조 url : <https://readystory.tistory.com/114>

#### 디자인 패턴이란?

프로그램을 설계할 때 발생했던 문제점을 객체 간의 상호 관계 등을 이용하여 해결할 수 있는 하나의 규약 형태로 만들어 놓은 것

#### 왜 디자인 패턴이 필요한가?

- "바퀴를 재발명하지 마라"  
   : 이미 발생한 문제에 대한 해답은 적극적으로 사용하라

- 개발자들간 의사소통 원활

#### 디자인 패턴의 종류

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

#### 어떻게 공부하면 좋을까?

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

#### 1. Eager Initilization

    public class Singleton {

    private static final Singleton instance = new Singleton();

    // private constructor to avoid client applications to use constructor
    private Singleton(){}

    public static Singleton getInstance(){
        return instance;
    }

}

#### 특징

싱글톤 클래스의 인스턴스를 클래스 로딩 단계에서 생성하는 방법이다. 그러나 어플리케이션에서 해당 인스턴스의 사용 유무와 관련 없이 인스턴스를 생성하여 낭비가 있을 수 있다. 또한 Exception에 대한 Handling을 제공하지 않는다.

#### 2. Bill Pugh Singleton Implementation

    public class Singleton{
        private Singleton(){}
        private static class SingletonHelper{
            private static final Singleton INSTANCE = new Singleton();
        }

        public static Singleton getInstnace(){
            return SingletonHelper.INSTANCE;
        }

    }

#### 특징

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

> 객체의 상태 변화를 관찰하는(전달받기를 기다리는) 옵저버(관찰자)들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 notify를 통해 객체가 각 옵저버에게 통지하도록 하는 디자인 패턴
>   >발행/구독 모델로 알려져있기도 하다.

* 주체 : 객체의 상태변화를 보고 있는 관찰자 (인스타 그램 계정이라면)
* 옵저버 : 이 객체의 상태 변화에 따라 전달되는 메서드 등을 기반으로 "추가 변화 사항" 이 생기는 객체들을 의미 (그 계정 팔로워들)

뉴스 피드나 유튜브로 비유해서 이해해보자. 유튜브 채널은 발행자(Subject)가 되고 구독자들은 관찰자(Observer)가 되는 구조로 보면 된다. 실제로 유튜버가 영상을 올리면 여러명의 구독자들은 모두 영상이 올라왔다는 알림을 받는데, 이를 패턴 구조로 보자면 구독자들은 해당 채널을 구독함으로써 채널에 어떠한 변화(영상을 올리거나 커뮤니티에 글을 쓰거나)가 생기게 되면 바로 연락을 받아 탐지하는 것이다. 반면 구독을 해지하거나 안한 시청자에게는 알림이 가지않게 된다.   

** 옵저버 패턴은 관찰한다는 단어에서 능동적으로 대상을 관찰하는 것 같지만, 실제로는 수동적으로 객체에게 전달받기를 기다리고 있다. 


### 특징
1. 일대다(one-to-many) 의존성 : 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용
2. 프록시객체를 이용하여 구현 가능

### 활용

1. 앱이 한정된 시간, 특정한 케이스에만 다른 객체를 관찰해야 하는 경우
2. 대상 객체의 상태가 변경될 때마다 다른 객체의 동작을 트리거해야 할 때
3. 한 객체의 상태가 변경되면 다른 객체도 변경해할 때 && 그런데 어떤 객체들이 변경되어야 하는지 몰라도 될 때
4. MVC 패턴에도 사용된 (Model, View, Controller)
    * MVC의 Model과 View의 관계는 Observer 패턴의 Subject 역할과 Observer 역할의 관계에 대응된다.
    * 하나의 Model에 복수의 View가 대응한다.
5. 트위터에서 사용
6. 자바에 내장 옵저버 객체 존재 (java.util.Observable, java.util.Observer) >> 자바는 다중 상속이 불가능함으로 옵저버 패턴을 개발자가 직접 구현해야 할 것이다.
7. java.util.EventListener의 모든 구현체

#### 장점
1. Subject의 상태 변경을 주기적으로 조회하지 않고 자동으로 감지할 수 있다.
2. 발행자의 코드를 변경하지 않고도 새 구독자 클래스를 도입할 수 있어 개방 폐쇄 원칙(OCP)Visit Website 준수한다
3. 런타임 시점에서에 발행자와 구독 알림 관계를 맺을 수 있다.
4. 상태를 변경하는 객체(Subject)와 변경을 감지하는 객체(Observer)의 관계를 느슨하게 유지할 수 있다. (느슨한 결합)

#### 단점
1. 구독자는 알림 순서를 제어할수 없고, "무작위 순서"로 알림을 받음
    * 하드 코딩으로 구현할수는 있겠지만, 복잡성과 결합성만 높아지기 때문에 추천되지는 않는 방법이다.
2. 옵저버 패턴을 자주 구성하면 구조와 동작을 알아보기 힘들어져 코드 복잡도가 증가한다.
3. 다수의 옵저버 객체를 등록 이후 해지하지 않는다면 메모리 누수가 발생할 수도 있다.

## 구현
주체 메시지 입력 > (모든 옵저버 notify) > 옵저버 update하여 구독하고 있는 토픽 정보 

    import java.util.ArrayList;
    import java.util.List;

    // 주체
    interface Subject {
        // 등록, 구독
        public void register(Observer obj);
        // 등록 취소, 구독 취소
        public void unregister(Observer obj);
        // 주체가 옵저버들에게 변화를 알려주면
        public void notifyObservers();
        // 옵저버가 주체의 변화를 가져간다.
        public Object getUpdate(Observer obj);
    }

    // 옵저버
    interface Observer {
        // 옵저버들은 이를 받아 단순 업데이트 한다
        public void update(); 
    }

    class Topic implements Subject {
        private List<Observer> observers;
        private String message; 

        public Topic() {
            this.observers = new ArrayList<>();
            this.message = "";
        }

        @Override
        public void register(Observer obj) {
            if (!observers.contains(obj)) observers.add(obj); 
        }

        @Override
        public void unregister(Observer obj) {
            observers.remove(obj); 
        }

        @Override
        public void notifyObservers() {   
            this.observers.forEach(Observer::update); 
        }

        @Override
        public Object getUpdate(Observer obj) {
            return this.message;
        } 
        
        public void postMessage(String msg) {
            System.out.println("Message sended to Topic: " + msg);
            this.message = msg; 
            notifyObservers();
        }
    }

    class TopicSubscriber implements Observer {
        private String name;
        private Subject topic;

        // 어떤 토픽의 옵저버인지 설정
        public TopicSubscriber(String name, Subject topic) {
            this.name = name;
            this.topic = topic;
        }

        // 자기가 옵저빙하는 토픽에 대한 정보를 업데이트한다
        @Override
        public void update() {
            String msg = (String) topic.getUpdate(this); 
            System.out.println(name + ":: got message >> " + msg); 
        } 
    }

    public class Helloworld { 
        public static void main(String[] args) {
            Topic topic = new Topic(); 
            Topic topic2 = new Topic(); 
            Observer a = new TopicSubscriber("a", topic);
            Observer b = new TopicSubscriber("b", topic);
            Observer c = new TopicSubscriber("c", topic);
            Observer d = new TopicSubscriber("d", topic2);
            topic.register(a);
            topic.register(b);
            topic.register(c); 
            topic2.register(c);
            topic2.register(d);

            topic.postMessage("amumu is op champion!!"); 
            topic2.postMessage("Hell No!!"); 
        }
    }
    /*
    Message sended to Topic: amumu is op champion!!
    a:: got message >> amumu is op champion!!
    b:: got message >> amumu is op champion!!
    c:: got message >> amumu is op champion!!
    Message sended to Topic: Hell No!!
    코드에서 C가 2개의 토픽을 모두 받고있어서 의도한대로 안나오는듯
    c:: got message >> amumu is op champion!!
    d:: got message >> Hell No!!

    */ 

## 1.1.5 프록시 패턴
>   대상 객체에 접근하기 전, 그 접근에 대한 흐름을 가로채 대상 객체 앞단의 인터페이스 역할을 하는 디자인 패턴

#### 장점
1. 사이즈가 큰 객체가 로딩되기 전에도 프록시를 통해 참조를 할 수 있다.
2. 실제 객체의 public, protected 메소드를 숨기고 인터페이스를 통해 노출시킬 수 있다.
3. 로컬에 있지 않고 떨어져있는 객체를 사용할 수 있다.
4. 원래 객체에 접근에 대해 사전처리를 할 수 있다.

#### 단점 
1. 객체를 생성할 때 한 단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하될 수 있다.
2. 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되어야 하는 경우 성능이 저하될 수 있다.
3. 로직이 난해해져 가독성이 떨어질 수 있다.

#### 활용
1. 객체의 속성 변환 보완
2. 데이터 검증, 캐싱, 로깅
3. nginx : 비동기 이벤트 기반의 구조와 다수의 연결을 효과적으로 처리 가능한 웹서버로 Node.js 서버 앞단의 프록시 서버로 활용
    └ 익명 사용자의 직접적인 서버의 접근을 차단하여 보안성 강화
4. CloudFare : CDN 서비스로 프록시 서버 활용 (각 사용자가 인터넷에 접속하는 곳과 가까운 곳에 콘텐츠를 캐싱 or 배포하는 서버 네트워크로 콘텐츠 다운로드 시간을 줄일 수 있다) 
    1. DDOS 공격 방어 : 의심스러운 트래픽 & 사용자가 아닌 시스템을 통해 을 자동으로 차단하여 DDOS 공격으로부터 보호. 
    2. HTTPS 구축 : 별도 인증서 설치 없이 좀더 쉽게 HTTPS 구축 가능

    // Image.java
    public interface Image {
        public void displayImage();
    }
    // Real_Image.java
    public class Real_Image implements Image {
        private String fileName;
        
        public Real_Image(String fileName) {
            this.fileName = fileName;
        }
        
        private void loadFromDisk(String fileName) {
            System.out.println("로딩: " + fileName);
        }
        
        @Override
        public void displayImage() {
            System.out.println("보여주기: " + fileName);
        }
    }
    // Proxy_Image.java
    public class Proxy_Image implements Image {
        private String fileName;
        private Real_Image realImage;
        
        public Proxy_Image(String fileName) {
            this.fileName = fileName;
        }
        
        @Override
        public void displayImage() {
            if (realImage == null) {
                realImage = new Real_Image(fileName);
            }
            realImage.displayImage();
        }
    }
    // Proxy_Pattern.javva
    public class Proxy_Pattern {
        public static void main(String args[]) {
            Image image1 = new Proxy_Image("test1.jpg);
            Image image2 = new Proxy_Image("test2.jpg);
            
            image1.displayImage();
            image2.displayImage();
        }
    }

<https://velog.io/@newtownboy/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-%ED%94%84%EB%A1%9D%EC%8B%9C%ED%8C%A8%ED%84%B4Proxy-Pattern>

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
