# CS 1

---

https://thebook.io/080326/0004/

# 1.1 디자인 패턴

- 프로그램 설계 시 발생했던 문제점들을 객체 간의 상호 관계 등을 통해 해결할 수 있도록 “규약” 형태로 만들어 놓은 것

## 1.1.1 싱글톤 패턴(Singleton Pattern)

- **하나의 클래스에 오직 하나의 인스턴스**만 포함
- 하나의 클래스를 기반으로 여러 개의 개별 인스턴스 생성이 가능하지만 단 하나의 인스턴스만를 생성해 이를 기반으로 로직 구현 → **데이터베이스 연결 모듈에 많이 사용**
- 하나의 인스턴스 생성 후 이를 다른 모듈들이 공유하며 사용하므로 생성 비용 ↓ / 의존성 ↑

### **자바스크립트의 싱글톤 패턴**

- `{}` or `new object` 로 객체 생성 시 다른 객체와 동일하지 않아 이 자체만으로 싱글톤 패턴 구현 가능

```jsx
const obj = {
	a: 27
}
const obj ={
	a: 27
}
console.log(obj === obj2)
///false -> obj와 obj2는 다른 인스턴스를 가짐
```

→ 위 코드도 어느 정도 싱글톤 패턴이라 볼 수 있으나, 실제 싱글톤 패턴은 아래와 같음

```jsx
class Singleton{
	constructor(){
		if(!Singleton.instance){
			Singleton.instance = this
		}
		return Singleton.instnace
	}
	getInstance() {
		return this.instance
	}
}
const a = new Singleton()
const b = new Singleton()
console.log(a === b) // true
```

→ Singleton.instance라는 하나의 인스턴스를 가지는 Singleton 클래스를 구현 (a와 b가 하나의 인스턴스를 가짐)

### 데이터베이스 연결 모듈

- 싱글톤 패턴은 데이터베이스 연결 모듈에 많이 사용
- 하나의 인스턴스를 기반으로 여러 개 생성 가능 → 데이터베이스 연결에 관한 인스턴스 생성 비용을 아낄 수 있음

### 자바에서의 싱글톤 패턴

- 중첩 클래스를 이용해서 만드는 방법이 가장 대중적

```java
class Singleton {
    private static class singleInstanceHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return singleInstanceHolder.INSTANCE;
    }
}

public class HelloWorld{ 
     public static void main(String []args){ 
        Singleton a = Singleton.getInstance(); 
        Singleton b = Singleton.getInstance(); 
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());  
        if (a == b){
         System.out.println(true); 
        } 
     }
}
/*
705927765
705927765
true

1. 클래스안에 클래스(Holder), static이며 중첩된 클래스인 singleInstanceHolder를 
기반으로 객체를 선언했기 때문에 한 번만 로드되므로 싱글톤 클래스의 인스턴스는 애플리케이션 당 하나만 존재하며 
클래스가 두 번 로드되지 않기 때문에 두 스레드가 동일한 JVM에서 2개의 인스턴스를 생성할 수 없습니다. 
그렇기 때문에 동기화, 즉 synchronized를 신경쓰지 않아도 됩니다. 
2. final 키워드를 통해서 read only 즉, 다시 값이 할당되지 않도록 했습니다.
3. 중첩클래스 Holder로 만들었기 때문에 싱글톤 클래스가 로드될 때 클래스가 메모리에 로드되지 않고 
어떠한 모듈에서 getInstance()메서드가 호출할 때 싱글톤 객체를 최초로 생성 및 리턴하게 됩니다. 
*/
```

### mongoose의 싱글톤 패턴

- Node.js에서 MongoDB 데이터베이스를 연결할 때 쓰는 mongoose 모듈에서 싱글톤 패턴 사용
- mongoose의 데이터베이스를 연결 시 사용하는 connect() 함수는 싱글톤 인스턴스를 반환

### MySQL의 싱글톤 패턴

- Node.js에서 MySQL 데이터베이스를 연결할 때 싱글톤 패턴 사용
- 메인 모듈에서 데이터베이스 연결에 관한 인스턴스 정의 후 다른 모듈에서 해당 인스턴스를 기반으로 쿼리를 보내는 형식

### 싱글톤 패턴의 단점

- TDD(Test Driven Development) 시 문제 발생 가능
    
    → 단위 테스트 시 테스트가 독립적이어야 하며 테스트가 순서 상관없이 실행 가능해야 하는데 싱글톤은 하나의 인스턴스를 기반으로 구현하므로 **각 테스트 별로 독립적인 인스턴스 생성이 어려움**
    

### 의존성 주입

- 싱글톤 패턴은 사용하기 쉽고 실용적이나 모듈 간 결합을 강하게 만들 수 있음
- 의존성 주입(DI, Dependency Injection)을 통해 모듈 간 결합을 느슨하게 만들 수 있음
- **메인 모듈(main module)이 ‘직접’ 다른 하위 모듈에 대한 의존성을 주기보다 의존성 주입자(dependency injector)를 통해 메인 모듈이 ‘간접’적으로 의존성을 주입하는 방식**
- 메인 모듈(상위 모듈)은 하위 모듈에 대한 의존성이 떨어지게 됨(디커플링)

**의존성 주입의 장점**

- 모듈을 쉽게 교체 가능한 구조가 되어 테스팅하기 쉽고 마이그레이션하기도 수월해짐
- 구현 시 추상화 레이어를 넣고 이를 기반으로 구현체를 넣어주므로 애플리케이션 의존성 방향이 일관됨
- 애플리케이션을 쉽게 추론 가능
- 모듈 간 관계들이 명확해짐

**의존성 주입의 단점**

- 모듈들이 여러 개로 분리되므로 클래스 수가 늘어나 복잡성 증가
- 런타임 패널티 발생

## 1.1.2 팩토리 패턴(Factory Pattern)

- 객체를 사용하는 코드에서 **객체 생성 부분을 떼어내 추상화**한 패턴
- **상속 관계에 있는 두 클래스에서 상위 클래스가 중요한 뼈대를 결정, 하위 클래스에서 객체 생성에 관한 구체적 내용을 결정**
- 상위 클래스와 하위 클래스가 분리되므로 느슨한 결합을 가짐 → 상위 클래스에서는 인스턴스 생성 방식에 대해 알 필요가 없어지므로 유연성 증가
- 객체 로직이 따로 떼어져 있으므로 코드를 리팩터링해도 한 곳만 고칠 수 있어 유지 보수성 ↑

ex) 상위 클래스 : 바리스타 공장에서 레시피 토대로 생산

하위 클래스 : 여러 레시피들의 구체적 내용을 포함

### 자바 스크립트의 팩토리 패턴

- new Object( )로 구현 가능

```jsx
const num = new Object(42)
const str = new Object('abc')
num.constructor.name; // Number
str.constructor.name; //String
```

- 숫자 전달 혹은 문자열 전달에 따라 다른 타입의 객체 생성
    
    → 전달 받은 값에 따라 다른 객체 생성 후 인스턴스 타입 등 결정
    

```jsx
class Latte {
    constructor() {
        this.name = "latte"
    }
}
class Espresso {
    constructor() {
        this.name = "Espresso"
    }
} 

class LatteFactory {
    static createCoffee() {
        return new Latte()
    }
}
class EspressoFactory {
    static createCoffee() {
        return new Espresso()
    }
}
const factoryList = { LatteFactory, EspressoFactory } 
 
class CoffeeFactory {
    static createCoffee(type) {
        const factory = factoryList[type]
        return factory.createCoffee()
    }
}   
const main = () => {
    // 라떼 커피를 주문한다.  
    const coffee = CoffeeFactory.createCoffee("LatteFactory")  
    // 커피 이름을 부른다.  
    console.log(coffee.name) // latte
}
main()
```

- CoffeeFactory(상위 클래스): 중요한 뼈대 결정 / 하위 클래스(LatteFactory): 구체적인 내용 결정
- 의존성 주입이라고도 볼 수 있음 → CoffeeFactory에서 LatteFactory의 인스턴스를 생성하는 것이 아닌 LatteFactory에서 생성한 인스턴스를 CoffeeFactory에 주입하고 있으므로
- 정적 메서드(static createCoffee())를 사용하여 클래스의 인스턴스 없이 호출이 가능하도록 하여 메모리 절약 및 개별 인스턴스에 묶이지 않도록 설정해 클래스 내의 함수 정의 가능

### 자바의 팩토리 패턴

```java
abstract class Coffee { 
    public abstract int getPrice(); 
    
    @Override
    public String toString(){
        return "Hi this coffee is "+ this.getPrice();
    }
}

class CoffeeFactory { 
    public static Coffee getCoffee(String type, int price){
        if("Latte".equalsIgnoreCase(type)) return new Latte(price);
        else if("Americano".equalsIgnoreCase(type)) return new Americano(price);
        else{
            return new DefaultCoffee();
        } 
    }
}
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
```

- if (”Latte”.equalsIgnoreCase(type))을 통해 문자열 비교 기반으로 로직 구성
- Enum 또는 Map을 이용해 if문을 쓰지 않고 매핑해서도 가능
    
    ※ Enum : 상수의 집합을 정의할 때 사용되는 타입. 상수 뿐 아니라 메서드를 집어넣어 관리 가능. 이를 활용하면 코드를 리팩터링 할 때 상수 집합에 대한 로직 수정 시 이 부분만 수정하면 됨. 또한, thread safe하기 때문에 싱글톤 패턴을 만들 때 도움이 됨.
    

## 1.1.3 전략 패턴(Strategy Pattern)

- = 정책 패턴 (policy pattern)
- 객체를 행위를 바꾸고 싶은 경우 ‘직접 수정’하지 않고 전략이라고 부르는 **‘캡슐화한 알고리즘’을 컨텍스트 내에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴**

### 자바의 전략 패턴

```java
import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.List;
interface PaymentStrategy { 
    public void pay(int amount);
} 

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
```

→ 결제 방식의 ‘전략’만 바꿔서 두 가지 방식으로 결제하는 코드 

### passport의 전략 패턴

- passport는 Node.js에서 인증 모듈 구현 시 사용하는 미들웨어 라이브러리
- 여러 전략을 기반으로 인증 가능하도록 함 (서비스 내의 정보를 기반으로 인증하는 LocalStrategy, 다른 서비스를 기반으로 인증하는 OAuth 등)

```jsx
var passport = require('passport')
    , LocalStrategy = require('passport-local').Strategy;

passport.use(new LocalStrategy(
    function(username, password, done) {
        User.findOne({ username: username }, function (err, user) {
          if (err) { return done(err); }
            if (!user) {
                return done(null, false, { message: 'Incorrect username.' });
            }
            if (!user.validPassword(password)) {
                return done(null, false, { message: 'Incorrect password.' });
            }
            return done(null, user);
        });
    }
));
```

→ passport.use( ) 메서드에 전략을 매개변수로 넣어서 로직을 수행

## 1.1.4 옵저버 패턴(Observer Pattern)

- 주체가 어떤 객체의 상태 변화를 관찰하다가 상태 변화가 있을 때마다 메서드 등을 통해 옵저버 목록에 있는 옵저버들에게 변화를 알려주는 디자인 패턴
- 주체 : 객체의 상태 변화를 보고 있는 관찰자
    
    옵저버 : 객체의 상태 변화에 따라 전달되는 메서드 등을 기반으로 ‘추가 변화 사항’이 생기는 객체들
    
- 주체와 객체를 따로 두지 않고 상태 변경이 일어나는  객체를 기반으로 구축하기도 함
- 주로 이벤트 기반 시스템에 사용
- MVC(Model-View-Controller) 패턴에 사용 → 주체라고 볼 수 있는 model에서 변경 사항이 생겨 update( ) 메서드로 옵저버인 뷰에 알려주고 이를 기반으로 controller 등이 작동

### 자바에서의 옵저버 패턴

```java
import java.util.ArrayList;
import java.util.List;

interface Subject {
    public void register(Observer obj);
    public void unregister(Observer obj);
    public void notifyObservers();
    public Object getUpdate(Observer obj);
}

interface Observer {
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

    public TopicSubscriber(String name, Subject topic) {
        this.name = name;
        this.topic = topic;
    }

    @Override
    public void update() {
        String msg = (String) topic.getUpdate(this); 
        System.out.println(name + ":: got message >> " + msg); 
    } 
}

public class HelloWorld { 
    public static void main(String[] args) {
        Topic topic = new Topic(); 
        Observer a = new TopicSubscriber("a", topic);
        Observer b = new TopicSubscriber("b", topic);
        Observer c = new TopicSubscriber("c", topic);
        topic.register(a);
        topic.register(b);
        topic.register(c); 
   
        topic.postMessage("amumu is op champion!!"); 
    }
}
/*
Message sended to Topic: amumu is op champion!!
a:: got message >> amumu is op champion!!
b:: got message >> amumu is op champion!!
c:: got message >> amumu is op champion!!
*/
```

→ topic을 기반으로 옵저버 패턴 구현

- topic : 주체이자 객체
- class Topic implements Subject : Subject interface
- Observer a = new TopicSubscriber(”a”, topic); : 옵저버 선언 시 해당 이름과 어떠한 토픽의 옵저버가 될 것인지 설정

**자바: 상속과 구현**

- 상속(extends)
    - 자식 클래스가 부모 클래스의 메서드 등을 상속받아 사용하며 자식 클래스에서 추가 및 확장을 할 수 있는 것
    - 재사용성, 중복성의 최소화 이뤄짐
- 구현(implements)
    - 부모 인터페이스를 자식 클래스에서 재정의하여 구현하는 것
    - 상속과 달리 반드시 부모 클래스의 메서드를 재정의하여 구현해야 함
- 상속 ↔ 구현
    - 상속은 일반 클래스, abstract 클래스 기반으로 구현
    - 구현은 인터페이스 기반으로 구현

### 자바 스크립트에서의 옵저버 패턴

- 프록시 객체를 통해 구현 가능
- **프록시 객체 : 어떠한 대상의 기본적인 동작(속성 접근, 할당, 순회, 열거, 함수 호출 등)의 작업을 가로챌 수 있는 객체**
    - 자바스크립트에서 프록시 객체는 두개의 매개변수
        - target : 프록시할 대상
        - handler : target 동작을 가로채고 어떠한 동작을 할 것인지 설정되어 있는 함수

```jsx
const hander = {
	get: function(target, name) {
		return name === 'name' ? '${target.a} ${target.b}' : target[name]
	}
}
const p = new Proxy({a: 'KUNDOL', b: 'IS AUMUMU ZANGIN'}, handler)
console.log(p.name) // KULDOL IS AUMUMU ZANGIN
```

→  new Proxy( )로 a와 b 속성을 가진 객체와 handler 함수를 매개변수로 넣고 p 라는 변수 선언. 이후, p 의 name 속성을 참조하니 a 와 b라는 속성밖에 없는 객체가 handler의 “name이라는 속성에 접근 시 a와 b를 합쳐서 문자열을 만들라”는 로직에 따라 문자열 생성. 즉 , **특성 속성 접근 시 그 부분을 가로채서 어떠한 로직을 강제할 수 있는 것이 프록시 객체**

- 프록시 객체를 이용한 옵저버 패턴

```jsx
function createReactiveObject(target, callback) { 
    const proxy = new Proxy(target, {
        set(obj, prop, value){
            if(value !== obj[prop]){
                const prev = obj[prop]
                obj[prop] = value 
                callback(`${prop}가 [${prev}] >> [${value}] 로 변경되었습니다`)
            }
            return true
        }
    })
    return proxy 
} 
const a = {
    "형규" : "솔로"
} 
const b = createReactiveObject(a, console.log)
b.형규 = "솔로"
b.형규 = "커플"
// 형규가 [솔로] >> [커플] 로 변경되었습니다
```

→ set( ) : 속성에 대한 접근을 “가로채”서 형규라는 속성이 솔로에서 커플로 되는 것을 감시

※프록시 객체의 함수

- get( ) : 속성과 함수에 대한 접근을 가로챔
- has( ) : 함수는 in 연산자의 사용을 가로챔
- set( ) : 속성에 대한 접근을 가로챔

### Vue.js 3.0의 옵저버 패턴

- 프런트엔드에서 많이 사용하는 프레임워크 Vue.js 3.0에서 ref나 reactive로 정의하면 해당 값 변경 시 자동으로 DOM에 있는 값이 변경
    
    → 앞서 나온 프록시 객체를 이용한 옵저버 패턴을 이용하여 구현한 것
    

## 1.1.5 프록시 패턴과 프록시 서버

### 프록시 패턴

- 프록시 패턴(proxy pattern)은 대상 객체(subject)에 접근하기 전 그 접근에 대한 흐름을 가로채 대상 객체 앞단의 인터페이스 역할을 하는 디자인 패턴
- 객체의 속성, 변환 등을 보완
- 보안, 데이터 검증, 캐싱, 로깅에 사용 → 프록시 서버로도 활용

### 프록시 서버

- 서버와 클라이언트 사이에서 클라이언트가 자신을 통해 다른 네트워크 서비스에 간접적으로 접속 가능하도록 해주는 컴퓨터 시스템 혹은 응용 프로그램

**프록시 서버로 쓰는 nginx**

- nginx ⇒ 비동기 이벤트 기반의 구조와 다수의 연결을 효과적으로 처리 가능한 웹 서버
- 주로 Node.js 서버 앞단의 프록시 서버로 활용 → Node.js의 버퍼 오버플로우 취약점 예방 가능
- 익명의 사용자가 직접적으로 서버에 접근하는 것을 차단, 보안 강화 가능

**프록시 서버로 쓰는 CloudFlare**

- 전 세계적으로 분산된 서버가 있어 이를 통해 빠르게 특정 시스템의 콘텐츠 전달이 가능한 CDN 서비스
- 웹 서버 앞단에 프록시 서버로 두어 DDOS 공격 방어 및 HTTPS 구축에 사용
- 해외에서 의심스러운 트래픽이 다수 발생 시 많은 클라우드 서비스 비용 발생 가능 → 이를 먼저 판단하여 CAPTCHA 등을 기반으로 일정 부분 막아주는 역할 수행
    
    ※ **DDOS 공격 방어**
    
    - 짧은 기간 동안 네트워크에 많은 요청을 보내 네트워크를 마비시켜 웹 사이트의 가용성을 방해하는 사이버 공격 유형
    - CloudFlare는 의심스러운 트래픽(특히 사용자가 아닌 시스템을 통해 오는 트래픽)을 자동 차단하여 DDOS 공격으로부터 보호
    - CloudFlare의 거대한 네트워크 용량과 캐싱 전략으로 소규모 DDOS 공격은 쉽게 방어 가능
    
    ※ **HTTPS 구축**
    
    - CloudFlare 사용 시 별도의 인증서 설치 없이 손쉽게 HTTPS 구축 가능

**CORS와 프런트엔드의 프록시 서버**

- CORS(Cross-Origin Resoure Sharing)는 서버가 웹 브라우저에서 리소스 로드 시 다른 오리진을 통해 로드하지 못하게 하는 HTTP 헤더 기반 메커니즘
- 프런트엔드 개발 시 프런트엔드 서버를 만들어서 백엔드 서버와 통신할 때 주로 CORS 에러
    
    → 이를 해결하기 위해 프런트엔드에서 프록시 서버를 만들기도 함
    
    Ex) 프런트엔드는 127.0.0.1:3000, 백엔드는 127.0.0.1:12010 ⇒ 포트 번호가 다르기 때문에 CORS 에러 발생. 이 때 프록시 서버를 둬서 프런트엔드 서버에서 요청되는 오리진을 변경
    
    ※ 오리진: 프로토콜과 호스트 이름, 포트의 조합
    
- 프런트엔드 서버 앞단에 프록시 서버를 놓으면 CORS 에러 해결은 물론 다양한 API 서버와의 통신도 매끄럽게 가능

## 1.1.6 이터레이터 패턴(Iterator pattern)

- 이터레이터(iterator)를 사용하여 컬렉션(collection)의 요소들에 접근하는 디자인 패턴
- 순회 가능한 여러 가지 자료형의 구조와는 상관없이 이터레이터라는 하나의 인터페이스로 순회 가능 → 다른 객체/컬렉션이라도 이터레이터 하나로 순회 가능

### 자바스크립트에서의 이터레이터 패턴

```jsx
const mp = new Map() 
mp.set('a', 1)
mp.set('b', 2)
mp.set('cccc', 3) 
const st = new Set() 
st.add(1)
st.add(2)
st.add(3) 
const a = []
for(let i = 0; i < 10; i++)a.push(i)

for(let aa of a) console.log(aa)
for(let a of mp) console.log(a)
for(let a of st) console.log(a) 
/* 
a, b, c 
[ 'a', 1 ]
[ 'b', 2 ]
[ 'c', 3 ]
1
2
3
*/
```

→ 다른 자료 구조인 set과 map임에도 똑같은 for a of b라는 이터레이터 프로토콜을 통해 순회

## 1.1.7 노출모듈 패턴(revealing module pattern)

- 즉시 실행 함수를 통해 private, public 같은 접근 제어자를 만드는 패턴
- 자바스크립트는 private, public 같은 접근 제어자가 존재하지 않고 전역 범위에서 스크립트 실행
    
    → 노출모듈 패턴을 통해 private와 public 접근 제어자를 구현
    

```jsx
const pukuba = (() => {
    const a = 1
    const b = () => 2
    const public = {
        c : 2, 
        d : () => 3
    }
    return public 
})() 
console.log(pukuba)
console.log(pukuba.a)
// { c: 2, d: [Function: d] }
// undefined
```

- a, b : 다른 모듈에서 사용할 수 없는 변수나 함수, private 범위
- c, d : 는 다른 모듈에서 사용 가능한 변수나 함수, public 범위

※ CJS 모듈 방식 : 노출모듈 패턴을 기반으로 만든 자바스크립트 모듈 방식

## 1.1.8 MVC 패턴(Model / View / Controller)

- 모델, 뷰, 컨트롤러로 이루어진 디자인 패턴

- 애플리케이션의 구성 요소를 세 가지 역할로 구분
- 개발 프로세스에서 각각의 구성 요소에만 집중해서 개발 가능
- 장점 : 재사용성과 확장성이 용이
    
    단점 : 애플리케이션이 복잡해질수록 모델과 뷰의 관계가 복잡해짐
    

### 모델

- 애플리케이션의 데이터인 데이터베이스, 상수, 변수 등
- 뷰에서 데이터 생성/수정 시 컨트롤러를 통해 모델 생성 혹은 갱신
    
    ex) 사각형 모양의 박스 안에 글자가 들어있을 때
    
    - 사각형 모양의 박스 위치 정보, 글자 내용, 글자 위치, 글자 포맷 등에 관한 정보

### 뷰

- inputbox, checkbox, textarea 등 사용자 인터페이스 요소
- 모델을 기반으로 사용자가 볼 수 있는 화면
- 모델이 가진 정보 저장 X, **단순히 화면에 표시하는 정보만 포함**
- 변경이 일어날 시 컨트롤러에 전달

### 컨트롤러

- 하나 이상의 모델과 하나 이상의 뷰를 잇는 다리 역할
- 이벤트 등의 메인 로직 담당
- 모델과 뷰의 생명주기 관리
- 모델과 뷰의 변경 통지를 받으면 이를 해석하여 각각의 구성 요소에 해당 내용 전달

### MVC 패턴의 예 스프링

- 스프링의 WEB MVC는 웹 서비스 구축 시 편리한 기능을 많이 제공
- 애너테이션(@RequestParam, @RequestHeader, @PathVariable 등)을 기반으로 사용자의 요청 값들을 쉽게 분석 가능
- 사용자의 요청이 유효한 요청인지 판단 가능
    
    ex) 숫자를 입력해야 하는데 문자를 입력하는 사례 등
    
- 재사용 가능한 코드, 테스트, 쉬운 리디렉션 등의 장점

## 1.1.9 MVP패턴(Model / View / Presenter)

- 뷰와 프리젠터는 일대일 관계 → MVC 보다 강한 결합
- 장점: View와 Model을 분리시켜 MVC나 Apple의 MVC에서 하기 힘들었던 테스트가 용이
- 단점: View와 Presenter의 의존관계가 강해지고 Controller 대신 Presenter가 복잡해짐

**MVP패턴에서 뷰가 업데이트 되는 과정**

※ **MVC패턴은 Model과 View가 서로 연결**되어 있어 의존관계

※ **MVP패턴은 Model과 View는 서로 연결 X,** Presenter 통해서 변화 확인

## 1.1.10 MVVM

- MVC의 C에 해당하는 **컨트롤러가 뷰모델(view model)로 바뀐 패턴**
    - 뷰모델 : 뷰를 추상화한 계층


- MVVM 패턴은 MVC 패턴과 다르게 커맨드와 데이터 바인딩을 가짐
- 장점
    - 뷰와 뷰모델 사이의 양방향 데이터 바인딩 지원
    - UI를 별도의 코드 수정 없이 재사용 가능
    - 단위 테스팅 가능

### 뷰(Vue.js)

- **대표적인 MVVM 패턴의 프레임 워크**
- 반응형(reactivity)이 특징인 프런트엔드 프레임워크
    - ex) watch와 computed 등으로 쉽게 반응형적인 값 구축 가능
- 함수를 사용하지 않고 값 대입만으로 변수 변경 가능
- 양방향 바인딩 가능
- html을 토대로 컴포넌트 구축 가능
    
    → 재사용 가능한 컴포넌트 기반으로 UI를 구축 가능하여 BMW, 구글 등에서 사용
