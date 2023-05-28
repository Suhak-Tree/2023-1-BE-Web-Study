WIL8

스프링을 이해 필수 = POJO(Plain Old Java Object)를 기반, 스프링 삼각형 = 스프링의 3대 프로그래밍 모델(IoC/DI, AOP, PSA)에 대한 이해가 필수

스프링 프레임워크와 스프링 삼각형의 관계는 영어문장과 알파벳 관계와 같다. 스프링 프레임워크와 스르핑 프레임워크를 활용한 부가 프레임워크는 스프링 삼각형을 이해하고 접근하면 어렵지 않다. 거대함 속 단순함( = 스프링 삼각형)

IoC/DI = 제어의 역전/의존성 주입 = Inversion of Control / Dependency Injection

자바에서의 의존성

new Car();
Car 객체 생성자에서 new Tire();

-> 의존성은 new이며, new를 실행하는 Car와 Tire사이에서 Car가 Tire에 의존함 => 전체가 부분에 의존함 => 의존하는 객체(전체)와 의존되는 객체(부분) 사이에 집합관계(집-냉장고), 구성관계(사람-심장)로 구분할 수 있음
-> 중요 포인트 2개 : 전체가 부분에 의존함, 프로그래밍에서 의존 관계는 new로 표현됨

스프링 적용 X. 기존방식  -> 스프링 Annotation 방식으로 조금씩 변경

-1. 스프링 적용 X. 기존방식. 직접 의존성을 해결하는 코드

STS - File - New - Spring project - Spring MVC Project -> Project name : ExpertSpring30 -> Next -> please specify the top-level package e.g. com.mycompay.myapp\*에 com.heaven.mvc 입력 -> finish -> 스프링 MVC 프로젝트 생성 -> STS 좌측 package Explorer에서 src/main/java 밑에 expert001_01 패키지 만들기 -> 클래스로 Car.java, KoreaTire.java, America.java, 인터페이스로 Tire.java, 테스트용 Driver.java 만들기 (모두 다 src/main/java/expert001_01 패키지 안에 있음)

*코드는 책 참조
*코드 중 내가 주의 해야할 부분
Tire.java 는 interface Tire{}의 블록 안에 세부내용 string getbrand(); 메서드 구현, 세부적인 내용은 KoreaTire, AmericaTire에서 리턴값 등 구현
인터페이스를 구현한 KoreaTire.java, AmericaTire.java는 public class 클래스명 implements Tire {}로 블록 안에 public String getbrand() {} 의 블록에서 구현. (return "코리아 타이아";)
Tire를 생산(new)하고 사용할 Car.java는 class 내부에 Tire tire;로 tire를 선언하고, public Car() 메서드 블록 안에 tire = new KoreaTire(); 로 의존관계를 만듦, getTireBrand() 메서드도 구현
Driver.java는 Driver 클래스를 만들고 그 안에 main(String[] args) 메서드를 넣고, Car car = new Car(); 로 차를 만들고 System.out.println(car.getTireBrand());로 결과를 확인한다

-

\*테스트용 src/test/Java 의 expert001_01 폴더 안에 CarTest.java 파일 만들기 - JUnit Test 클래스

import static org.junit.Assert.\*;

import org.junit.Test;

public class CarTest{
@test
public void 자동차*장착*타이어브랜드\_테스트() {
Car car = new Car();

assertEquals("장착된 타이어: 코리아 타이어", car.getTireBrand());
}
}

-

실제 배포용 코드를 포함한 src/main/java 폴더의 소스에는 한글로된 메서드명을 권장하지 않음. 하지만, 배포하지 않은 테스트 코드를 포함한 src/test/java 폴더에서는 영어보다는 한글로 정확히 무슨 테스트를 하고 있는 것인지 적어주는 것을 권장함(더욱 직관적이기 위해서)

실행 방법 : Driver.java 파일 우클릭 - Run As - Java Application -> STS console 창으로 확인 가능
JUnit Test 클래스 실행 방법 : src/test/java/expert001_01/CarTest.java 파일 우클릭 - Run as - JUnit Test -> 테스트 성공(녹색 막대)

예시로 얻어낸 결론 -> 자동차는 타이어에 의존함, 운전자는 자동차에 의존함

-2. 의존성을 주입하는 코드

-01. 생성자를 통한 의존성 주입
주입 = "외부에서"라는 뜻을 내포 = 외부에서 생산된 타이어를 자동차에 장착하는 작업 (자동차 내부에서 타이어 생산X)

운전자가 타이어를 생산한다, 운전자가 자동차를 생산하면서 타이어를 장착한다. -> Tire tire = new KoreaTire(); Car car = new Car(tire);

외부에서 생산된 tire 객체를 Car 생성자의 인자로 주입(장착)하는 형태 = 타이어 생산 -> 타이어 생산 위임 -> 자동차 생산 + 타이어 장착의 과정으로 실행 -> Car의 생성자에 tire:Tire 인자가 생김

\*개별 메서드의 작동 방식은 액티비티 다이어그램으로 표기 = 기존의 순서도를 대체함 (액티비티 다이어그램보다 순서도나 NS차트 권장, 의사소통 필요하거나 논리가 복잡한 메서드 정도만 액티비티 다이어그램, 순서도)

코드 달라진 부분 :
Car.java에서 Car 클래스 내부에 생성자가 생김, new가 사라지고 생성자에 인자가 추가됨 = public Car(Tire tire) {this.tire = tire;}
Driver.java에서는 main 메서드 안에 Tire tire = new KoreaTire(); Car car = new Car(tire); 로 바뀜
new를 통해 타이어를 생산하는 부분이 Car.java에서 Driver.java로 이동했음 + 생산된 tire의 객체 참조 변수를 Car 생성자의 인자로 전달

장점 : 기존 코드 = Car가 구체적으로 KoreaTire를 생산할지 AmericaTire를 생산할지 결정 -> 유연성이 떨어짐
변경된 코드 = 자동차가 생산될 때 어떤 타이어를 생산해서 장착할지 자동차가 고민X, 운전자가 차량을 생산할 때 운전자가 어떤 타이어를 장착할까 고민하게 함 -> 자동차는 어떤 타이어를 장착할까 고민하지 않아도 됨

-Test 코드 달라진 부분

테스트() 메서드의 블록 안에는 Tire tire1 = new KoreaTire(); Car car1 = new Car(tire1);으로 방식이 바뀜

ExpertSpring30 -> 마우스 우클릭 -> Run as - JUnit Test -> 두개의 테스트 클래스가 한번에 실행될 수 O

\*의존성 주입의 장점 = Car는 Tire 인터페이스를 구현한 어떤 객체가 들어오기만 하면 정상적으로 작동함 = 의존성 주입 -> 확장성 좋아짐(새로운 Tire 브랜드가 생겨도 Tire 인터페이스 구현하면 Car.java 코드 변경할 필요 X, 재컴파일할 필요 X)
제품화 시, Car.java, Tire.java를 하나의 모듈, Driver.java, KoreaTire.java, AmericaTire.java를 각각 하나의 모듈 -> ChinaTire.java,가 생겨도 Driver.java, ChinaTire.java만 컴파일해서 배포하면 됨 (다른 코드 재컴파일 및 재배포 필요 X) => 실제 제품에서 더 많은 코드를 재배포할 필요 X -> 재컴파일과 재배포에 대한 부담 감소 = 인터페이스를 구현했기에 얻는 이점 (표준화 - 페트병 병마개)

현실 세계의 표준 규격 준수 = 프로그래밍 세계의 인터페이스 구현 \*전략 패턴의 3요소 = 클라이언트, 전략, 컨텍스트 = 전략 메서드를 가진 전략 객체(Tire를 구현한 KoreaTire, AmericaTire), 전략 객체를 사용하는 컨텍스트(Car의 getTireBrand() 메서드), 전략 객체를 생성해 컨텍스트에 주입하는 클라이언트(Driver의 main() 메서드)

단점 : 더이상 타이어를 교체 장착할 방법이 없음

-02. 속성을 통한 의존성 주입 : get, set 속성

장점 : 운전자가 원할 때 Car의 Tire를 교체 가능 -> 속성을 통한 의존성 주입이 필요

\*참고 : 최근에는 생성자를 통한 의존성 주입을 선호하는 사람들이 많음, 프로그램에서는 한번 주입된 의존성을 계속 사용하는 경우가 더 일반적이기 때문

car.setTire(tire);

Car 클래스에서 생성자가 사라짐 -> 자바 컴파일러가 기본 생성자를 제공해줄 것임

-코드 달라진 부분
Car.java에 생성자가 없어지고 public Tire getTire(){return tire;} 메소드와, public void setTire(Tire tire){this.tire = tire;} 메소드가 추가되었다. = tire 속성에 대한 접근자 및 설정자 메서드가 생겼다
Driver.java에서도 Car car = new Car(); car.setTire(tire); 로 바뀌었다.

-Test 코드에서 달라진 부분
Tire tire1 = new KoreaTire(); Car car1 = new Car(); car1.setTire(tire1);assertEquals("장착된 타이어: 코리아 타이어", car1.getTireBrande());

-3. 스프링을 통한 의존성 주입

-01. XML 파일 사용

운전자가 종합 쇼핑몰(스프링 프레임 워크)에서 타이어를 구매한다, 운전자가 종합쇼핑몰에서 자동차를 구매한다, 운전자가 자동차에 타이어를 장착한다.

자바로 표현 - 속성 메서드를 사용한다
ApplicationContext context = new ClassPathXmlApplicationContext("expert002.xml", Driver.class);
Tire tire = (Tire)context.getBean("tire");
Car car = (Car)context.getBean("car");

스프링을 통한 의존성 주입 = 생성자를 통한 주입, 속성을 통한 주입 모두 지원 (속성만 살펴볼 것)
-> Driver 클래스만 살짝 달라지고, 스프링 설정 파일 하나만 추가하면 작업 끝임 -> 현실세계와 더욱 유사해짐 (객체 지향은 현실 세계 지향임)

-코드 달라진 점
Driver.java - 생산 과정이 구매 과정으로 바뀌고, 상품을 구매할 종합 쇼핑몰에 대한 정보가 필요함

import org.springframework.context.ApplicationContext ;
import org.springframework.context.support.ClassPathXmlApplicationContext;

스프링 프레임워크(종합쇼핑몰)에 대한 정보를 가지고 있는 패키지이다.

Driver 클래스의 main 메서드 안에
ApplicationContext context = new ClassPathXmlApplicationContext("expert002/expert002.xxml");
Car car = context.getBean("car", Car.class);
Tire tire = context.getBean("tire", Tire.class);
가 추가된다

스프링 프레임워크(종합 쇼핑몰)에 대한 정보라 context로 선언되는 부분이다. 그 후에는 Car와 Tire를 구매하는 코드라고 볼 수 있다.

/src/main/java/expert002/expert002.xml 안에 쇼핑몰에서 구매 가능한 상품 목록이 등록되어 있어야한다. -> 상품 목록이 담긴 XML 파일을 만들어야 한다 -> expert002 패키지 마우스 우클릭 -> New - Other - Spring - Spring Bean Configuration File 차례로 선택 -> 이름 정하기 => expert002.xml로 지정하면 만들어짐

상품 등록 = bean 태그를 이용해 등록, 이때 id속성(상품 구분용), class 속성(상품을 어떤 클래스를 통해 생산(인스턴스화)해야할지 나타냄) 함께 지정

KoreaTire.java - XML 파일에서 id=tire인 bean태그와 연결 - Driver.java의 main() 메서드 안의 context.getBean("tire", Tire.class)와 연결됨 = KoreaTire라고 하는 상품이 tire라는 이름으로 진열되어있고, 구매(getBean)할 수 있다

-Test 코드에서 달라진 점 : 없음(동일)

스프링을 도입해서 얻는 이득 = 자동차 타이어 브랜드를 변경할 때 재컴파일/재배포 하지 않고 XML 파일만 수정하면 프로그램의 실행 결과를 바꿀 수 있음

Driver.java의 Tire tire = context.getBean("tire",Tire.class); 부분 = 타이어를 구매하는 부분 but, 자바 코드 어디에서도 KoreaTire 클래스나 AmericaTire 클래스를 지칭하는 부분 없음 => expert002.xml에 해당하는 내용이 있기 때문(id가 tire인 bean 태그의 class 어트리뷰트가 KoreaTire로 지정되어있음) -> AmericaTire로 바꿔야 하더라도 자바코드를 변경/재컴파일/재배포할 필요 X, XML 파일을 변경하고 프로그램 실행 -> 변경사항 적용(id가 tire인 것의 class가 무엇이냐가 중요!)

-02. 스프링 설정파일(XML)에서 속성 주입

-코드 변경되는 부분

<bean id="koreaTire" class="expert003.KoreaTire"> </bean>
<bean id="car" class="expert003.Car">  <property name="tire" ref="koreaTire"> </property> </bean>

자바에서 접근자 및 설정자 메서드 = 속성(property) 메서드 => Driver.java에서 car.setTire(tire) 부분을 XML 파일의 property 태그를 이용해 대체함

Driver.java에서는 Tire tire = context.getBean("tire", Tire.class); car.setTire(tire) 부분을 삭제한다

자바코드, XML 설정 -> 현실적인 내용을 반영, 이해하기 쉬움, 유지보수가 편함

-JUnit 테스트 케이스 변경되는 부분

import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("expert003.xml")

public class 에서 시작 부분에 @Autowired 가 추가된다

-03. @AutoWired를 통한 속성 주입

프로그래머의 3대 스킬 : C&P(복사&붙여넣기), D&C(분할 & 정복, C&I(창조적 게으름) -> SpringFramework 개발자의 경우 -> @Autowired로 게으름을 표현함 - 스프링의 속성 주입 방법임

import org.springframework.beans.factory.annotation.Autowired;

@Autowired

import문 하나와 @Autowired 어노테이션 이용 -> 설정자 메서드 이용하지 않고도 스프링프레임워크가 설정 파일을 통해 설정자 메서드 대신 속성 주입해줌

-변경된 코드
변경된 스프링 파일(expert004.xml) - 마우스 우클릭 - Open With - Spring Config Editor 차례로 선택 -> 편집기 열림 -> 하단의 Namespaces 탭 클릭 -> context 체크 -> Source 탭으로 돌아감 -> <context:annotation-config /> 추가 -> car에 해당하는 bean 태그 수정 -> 저장

Car.java - @Autowired를 사용하도록 바뀜. 그 외 파일 변경 X
import org.springframework.beans.factory.annotation.Autowired;

car 클래스 안에 Tire 선언 전, @Autowired 필요

@Autowired의 의미 = 스프링 설정 파일을 보고 자동으로 속성의 설정자 메서드에 해당하는 역할을 해줌 -> XML 파일에서 property 태그가 사라짐 => @Autowired를 통해 car의 property를 자동으로 엮어줌(자동 의존성 주입) -> 생략 O

if) AmericaTire로 변경된 Driver.java를 실행하려면? -> 재컴파일 할 필요 없이 expert.xml에서 bean의 id 속성만 변경
if) KoreaTire 부분을 완전히 삭제하고, AmericaTire의 id 속성을 삭제하면? -> 정상적으로 구동됨!! => 인터페이스의 구현 여부에 따라 달라짐

스프링의 @Autowired 마법 = type 기준 매칭 -> 만약 같은 타입을 구현한 클래스가 여러개? -> bean태그의 id로 구분해서 매칭 (빈이 1개이거나, bean이 여러개여도 id가 일치하는 bean이 1개여야 유일한 빈을 객체에 할당할 수 있음) -> id와 type 중 type 구현에 우선순위가 있음

만약 id로 구분할 수 없다면? -> 에러 = 유일한 빈 선택 불가 -> 스프링도 포기함 => bean 태그의 id 속성을 작성하는 습관 중요!!!

-03. @Resource를 통한 속성 주입

변경되는 코드 : @Autowired 부분 -> @Resource / 나머지 동일

@Autowired = 스프링의 어노테이션(스프링 프레임워크 사용하지 않는다면 @Autowired 사용 불가), type과 id 가운데 매칭 우선순위는 type이 높음
@Resource = 자바 표준 어노테이션, 매칭 우선순위 id가 높음, id로 매칭할 빈을 찾지 못한 경우 type으로 매칭할 빈을 찾게 됨

-04. @Autowired vs @Resource vs <property> tag

연구 1. XML - 한 개 빈이 id 없이 tire 인터페이스를 구현한 경우

ex) expert006.xml 파일에 <bean class="expert006.KoreaTire"></bean>
@Resource 사용해서 Car의 tire 속성 주입 : Car.java 파일에 Car class 안에 @Autowired 대신 @Resource로. import javax.annotation.Resource; 추가
@Autowired 사용해서 Car의 tire 속성 주입 : Car.java 파일에 Car class 안에 @Autowired. import org.springframework.beans.factory.annotation.Autowired; 추가

연구 2. XML - 두 개의 빈이 id 없이 인터페이스를 구현한 경우

ex) expert006.xml 파일에 <bean class="expert006.KoreaTire"></bean>, <bean class="expert006.AmericaTire"></bean>
@Resource, @Autowired 모두 오류 메시지

연구 3. XML - 두 개의 빈이 tire 인터페이스를 구현하고 하나가 일치하는 id를 가진 경우

ex) expert006.xml 파일에 <bean id="tire" class="expert006.KoreaTire"></bean>, <bean id="tire2" class="expert006.AmericaTire"></bean>
@Resource, @Autowired 모두 정상 작동

연구 4. XML - 두 개의 빈이 tire 인터페이스를 구현하고 일치하는 id가 없는 경우

ex) expert006.xml 파일에 <bean id="tire1" class="expert006.KoreaTire"></bean>, <bean id="tire2" class="expert006.AmericaTire"></bean>
@Resource, @Autowired 모두 오류 메시지

연구 5. XML - 일치하는 id가 하나 있지만 인터페이스를 구현하지 않은 경우

ex) expert006.xml 파일에 <bean id="tire" class="expert006.Door"></bean>, Door.java 파일에는 tire 인터페이스가 구현되어있지 않음
서로 다른 오류 메시지가 나온다.
