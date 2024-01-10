

  ### **“Spring 을 잘하는 개발자” 는 2가지 의미**

    - Spring **프레임워크**에 대한 **구성 및 원리 이해**
    - Java **언어**를 잘 작성하는 법 = **디자인 패턴 및 SOLID 원칙**
        - Java **언어**에 대한 **구성 및 원리 이해** 도 추가되면 좋으나 시간적 한계

  이에 따라 오늘의 수업은 아래 절차로 진행한다.

    1. 가장 먼저 **Java 언어 구성 및 원리 이해** 를 위해 간단히 **컴파일과 런타임(JVM)** 을 짚고
    2. Java 언어를 잘 작성하는 방법인 **디자인 패턴 및 SOLID 원칙** 학습 후
    3. 프레임워크인 Spring 으로 이동하여 **Spring 프레임워크 특징과 Spring Boot** 알아본 뒤
    4. Spring MVC 와 Layered Architecture 를 통해 **Spring 구성 및 동작 원리를 이해**한다.

  ### Java 컴파일과 런타임

    - 컴파일 과정 Compile Time : **Java 코드 (.java) ⇒ Bytecode (.class)** | javac 컴파일러 를 통해
    - 런타임 과정 Runtime Time : **Bytecodes (.class) ⇒ 기계어 (OS 에 따른)** | *JVM 엔진에 의해*
    - 컴파일 에러 vs 런타임 에러
        - **컴파일 에러** : 구문 에러
            - **Checked Exception** ⚠️ **수업내용 정정 : Checked Exception 은 컴파일 에러로 분류됩니다.**
                - **반드시 예외 처리**를 해야한다 (2가지 방식 존재 **try-catch** 혹은 **throw**)
        - **런타임 에러** : 객체 사용시 잘못된 프로그래밍으로 NPE
            - **Unchecked Exception (Runtime Exception)**
                - 예외 처리를 강제하지 않는다
        - 재미있는 글 : Checked Exception 필요한가? 결과를 먼저 얘기하자면, 쓰지말자로 결론

      [Java의 Checked Exception은 실수다?](https://velog.io/@eastperson/Java의-Checked-Exception은-실수다-83omm70j)


    ### Java 런타임 : JVM
    
    JVM 역할 = 런타임 = 바이트코드를 OS 에 특화된 기계어로 바꾼다.
    
    JVM 은 아래 3개만 기억/학습하면 된다.
    
    1. **Class Loader**
    2. **Runtime Data Area** (JVM 메모리)
    3. **Execution Engine** (JVM 엔진)
    
    ---
    
    - **1. Class Loader**
        
        앞서 javac 를 통해 컴파일된 바이트코드는 **Class Loader** 가 아래 **Runtime Data Area** 에 적재
        
        - **동적 로딩(Dynamic Loading)** : 필요한 바이트코드만을 **Runtime Data Area** 에 적재
        - 3가지 절차를 수행 : Loading → Linking → Initialization
    - **2. Runtime Data Area** (JVM 메모리)
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d6a64ebe-ed37-48a9-adf3-caf54b16d12e/Untitled.png)
        
        - **공유 영역** (모든 Thread 가 공유)
            - **Heap** : 객체(인스턴스, 배열) 저장 ← **Garbage Collection 의 대상**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/74e3c969-1466-4473-8dad-1ff0a822fe0b/Untitled.png)
                
                - **Young/New Generation** ⇒ **Minor GC** 대상 (높은 주기, 짧은 시간)
                    
                    > -Xmn 옵션
                    > 
                    - Eden
                    - Survivor 1
                    - Survivor 2
                - **Old/Tenured Generation** ⇒ **Major GC** 대상 (낮은 주기, 긴 시간)
                    
                    > -Xms, -Xmx 옵션
                    > 
                - **Permanent Generation**
                    
                    > -XX:PermSize, -XX:MaxPermSize 옵션
                    > 
                
                참조 : Minor GC / Major GC 모두 Stop-The-World Event (다른 스레드 모두 멈춤)
                
            - **Method** : 전역변수, Static 변수, Final Class, **Class 의 필드와 메서드 정보** 등
                - 프로그램 시작부터 끝까지 메모리에 상주
        - **Thread 영역** (Thread 마다 하나씩 생성)
            - **Stack** : 지역변수, 파라미터, 리턴값
            - PC Register : 스레드 생성될때마다 생성되는 영역, 스레드 실행되는 부분의 주소와 명령 저장
            - Native Method Stack : 자바 이외의 언어 수행을 위한 개별 스택
    - **3. Execution Engine** (JVM 엔진)
        - Runtime Data Area 에 **적재된걸 실행** : **Execution Engine** (실행 엔진)
            - **인터프리터** : Bytecode → 기계어로 변환하여 실행
            - **JIT 컴파일러** : 실행이 잦은 Bytecode → 기계어로 미리 컴파일해놓는것
        - Runtime Data Area 에 **적재된걸 정리** : **Garbage Collection**
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/4b979949-e658-4a71-9ebf-242424c3e0d7/Untitled.png)
    
    ### **디자인 패턴 및 객체지향 설계원칙 SOLID**
    
    Java 는 **객체지향 프로그래밍 OOP** ⇒ **객체지향 = 분업화** 목표 **= 모듈화** 목표
    
    - 모듈의 기반이 클래스와 객체인것 = **특정 객체는 특정타입의 업무만을 수행**
        - 추상화
        - 다형성
        - 캡슐화
        - 상속
    
    **디자인 패턴 ⇒ 재사용성** 목표
    
    - 객체지향 패러다임에서 더 좋은 코드란 무엇인가에 대한 고민의 결과
        - **중복의 최소화** : 하나의 수정이 다른 하나의 수정을 동반해선 안된다
        - **코드 변경의 용이성** : 코드는 항상 완벽하지 않고, 요구사항은 상시 바뀔 수 있습니다.
        - **재사용성** : 정돈된 코드는 전혀 다른 요구사항 및 비슷한 경우에도 그대로 사용이 가능합니다.
    - 디자인 패턴을 위한 1, 2 원칙
        - **구현보다 인터페이스에 맞춰서 코딩한다.**
            - 구현은 언제나 바뀔 수 있다. 인터페이스를 통해 유연하게 구현하자
        - **‘상속’보다는 인터페이스 ‘구성(Composite)’을 사용하자.**
            - ‘상속’ 이 아닌 인터페이스 ‘구성’ 시 원하는 구현을 붙였다 떼었다 할 수 있다.
    - 디자인 패턴 종류
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/5048e29c-36c4-4ccc-8bce-dac8a5f19341/Untitled.png)
        
        - 생성(Creational) 패턴
            - 추상 팩토리
            - 빌더
            - 팩토리 메서드
            - 프로토타입
            - 싱글톤
        - 구조(Structural) 패턴
            - 어댑터
            - 브리지
            - 컴포지트
            - 데코레이터
            - 퍼사드
            - 플라이웨이트
            - 프락시
        - 행위(Behavioral) 패턴
            - 책임연쇄
            - 커맨드
            - 인터프리터
            - 이터레이터
            - 미디에이터
            - 메멘토
            - 옵저버
            - 스테이트
            - 스트레티지
            - 템플릿 메서드
            - 비지터
    
    **객체지향 설계원칙 SOLID ⇒ High Cohesion, Loose Coupling** 목표
    
    - **S, Single Responsibility (단일책임)** : 하나의 모듈(한 클래스 or 메소드)은 하나의 책임/역할만 가짐
    - **O, Open-Closed (개방폐쇄)** : 확장에 열려있다 | 수정에 닫혀있다 = 인터페이스에 구현체 갈아끼기
    - **L, Liscov Substitution (리스코프 치환)** : “상속 시” 부모 클래스에 대한 가정 그대로 자식 클래스 동일
        - 하위 타입은 항상 상위 타입을 대체 할 수 있어야 한다
            
            > 상속 시 하위 클래스는 상위 클래스의 모든 가정을 그대로 갖고있어야함.
            > 
            > 
            > 상위 클래스를 사용하는 개발자가 가정하는 모든것들 제공해야함
            > 
            
            ```tsx
            Rectangle rectangle = new Square();
            resize(rectangle, 100, 150);
            // Rectangle 입장에선 말이되나, Square 입장에선 아예 말이 안되는 코드
            // 개발자는 Rectangle 를 사용할때 이게 정사각형이다라는걸 가정할 수 없다.
            ```
            
    - **I = Interface Segregation (인터페이스 분리)** : 인터페이스 내에 메소드는 최소한 개수로
        - 하나의 일반적인 인터페이스보다 여러 개의 구체적인 인터페이스가 낫다.
            - 도메인같은걸로 인터페이스를 쪼개어놓으면
                - 필요한 구현은 1개인데 구현체에 나머지 쓰지도 않는것들까지 구현해야함
                
                예) 라면끓이기 객체 - 물끓이기, 스프넣기, 면넣기, 파썰어넣기, 계란넣기
                
                - 파썰어넣기, 계란넣기 내 아무것도 구현이 없음 (텅 빈 Empty 구현)
                - **기본 라면 끓이기, 라면에 고명넣기** 등으로 쪼개어야함
    - **D = Dependency Inversion (의존성 역전)** : 인터페이스로 구현체를 연결
        - 고수준 모듈 - 인터페이스(추상화) - 저수준 모듈
    
    ### Spring 프레임워크 및 Spring Boot
    
    **제어 역전(IoC, Inversion of Control)**
    
    > 제어 역전 = 객체 생성 및 주입
    > 
    - 객체의 생성을 직접할것인가, 객체가 필요한 순간에 개발자가 코딩 내 직접 생성하여 사용할건가?
    - 아니면 **어떤 주체**가 객체가 필요할때, 객체를 생성하여 필요한곳에 주입해줄것인가?
        - 제어 역전 구현 방법 5가지
            
            ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/84ff2497-6e0d-4338-b578-592041361bf8/Untitled.png)
            
            - Template Pattern : 추상 클래스 부분 구현
            - Delegate : 위임 (실행 결과를 받는것까지 모두 위임, 자기 자신을 보낸다)
            - Event : 이벤트 발행 (Publisher / Subscriber)
            - Service Locator / Lookup : Service Locator 에서 직접 가져와쓰기, **주입하는것**
                - Service Locator 와 DI 의 차이, 그림으로 이해하기
                    - **Service Locator : 주입하는것**
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/8ec527ed-7620-4bab-9680-18cea708e9ef/Untitled.png)
                    
                    - **DI(Dependancy Injection) : 주입되는것**
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/9aebf4ac-4dfa-4f83-b3c7-cc5a03bba99f/Untitled.png)
                    
            - **DI(Dependancy Injection) 의존성 주입** : Container 가 Bean 직접 **주입해주는것**
                
                : 필요 객체들이 생성되어 **주입되는것**
                
                과거 Spring 에서는 필요 객체들을 개발자가 개발한뒤, XML 을 통해 일일히 Bean 등록
                
                **현재 Spring** 에서는 XML 아닌 @Container, @Repository 등의 지정으로 Bean 등록
                
                - XML 에 비해 훨씬 더 간편해졌다. (과거 Spring 책 읽어보면 XML 기반으로 설명)
                
                위와 같이 Bean 을 등록하게되면
                
                - (IoC) Container 는 (1) 수집하고, (2) 필요 객체가 필요할시 주입해준다.
                
                ---
                
                3가지 방법론이 있음
                
                - **생성자 주입 : Spring 공식 추천 (1) 순환참조를 컴파일 시 방지 (2) Final 적용 가능**
                    - **Spring 공식 추천 이유**
                        - **(1) 순환참조를 컴파일 시 방지**
                            
                            : 순환 참조 여부를 객체 생성 후가 아닌 생성 전에 인지하여 개발 실수 방지
                            
                        - **(2) Final 적용 가능**
                            
                            : Bean 객체의 불변성 보장 (한번 주입되면 이후에 바뀌지 않는다는걸 보장)
                            
                    
                    ---
                    
                    - 다른 방법들과 쉬운 비교 및 이해를 위한 코드
                    
                    ```tsx
                    // 생성자 1. 다른 방법들과 쉬운 비교 및 이해를 위한 코드
                    class MyClass {
                    	MyService service;
                    	
                    	@Autowired
                    	public MyClass(MyService service) {
                    		this.service = service
                    	}
                    
                    	public void test() {
                    		service.test()
                    	}
                    }
                    ```
                    
                    - 실제 현업에서 사용하는 코드 (@RequiredArgConstructor + private final)
                        - **@RequiredArgConstructor** ⇒ (Lombok) 생성자 자동 생성
                        - **private final Bean** ⇒ final 의 의미는 한번 초기화 후 변하지 않는다는 뜻
                    
                    ```tsx
                    // 생성자 2. 실제 현업에서 사용하는 코드
                    **@RequiredArgConstructor**
                    class MyClass {
                    	**private final** MyService service;
                    
                    	public void test() {
                    		service.test()
                    	}
                    }
                    ```
                    
                - 필드 주입 (필드 객체 선언)
                    
                    ```tsx
                    class MyClass {
                    	@Autowired
                    	private MyService service;
                    
                    	public void test() {
                    		service.test()
                    	}
                    }
                    ```
                    
                - 수정자 주입 (Setter 메서드)
                    
                    ```tsx
                    class MyClass {
                    	private MyService service;
                    
                    	@Autowired
                    	public void setService(MyService service) {
                    		this.service = service;
                    	}
                    }
                    ```
                    
    
    ---
    
    **Library 와 Framework 의 차이**
    
    - **Library** : 개발자는 필요한 Library 들을 **선택하고, 연결하고, 설정하는** 모든걸 다 직접 해야함
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/4cad8b18-5471-43ad-9f33-859c66fbb6ac/Untitled.png)
        
    - **Framework** : 개발자가 직접 구현한것 혹은 Library 들을 **연결하고, 설정하는** 것들을 제공
        - 그렇기때문에 Framework 는 개발자에게 **“비지니스 구현”** 만 신경쓰도록 만들 수 있는것
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/becf61ea-9783-4d1e-8cd3-175d0249ff02/Untitled.png)
        
    
    ---
    
    **Spring 프레임워크 = Framework 의 제어 역전(IoC, Inversion of Control)**
    
    - **Library 혹은 구현체의 연결**과 **설정 및 객체 생성 및 주입** 제공
    
    ---
    
    **Spring Boot**
    
    > React 와 CRA(Create-React-App) 의 관계와 유사
    > 
    - **의존성 관리** : 모든 의존성 신경쓰지 않고, 버전 충돌없이 잘 말아놓은 최상위 패키지 사용
        
        > **CRA 케이스** : React 본격적 사용을 위해 필요한 수많은 라이브러리를 CRA 자체적으로 가짐
        > 
        - **spring-boot-starter-web** : WAS 개발을 위한 모든것 = Tomcat 내장 및 ‘자동 설정’ 기능
        - spring-boot-starter-security : Spring Security 및 인증, 인가, 권한 라이브러리
        - spring-boot-starter-jdbc : HikariCP 커넥션 풀을 활용한 JDBC 기능 제공
        - spring-boot-starter-data-jpa : Spring JPA 및 Hibernate 등
    - **자동 설정** : 의존성 관리에서 꽤 많은 라이브러리들을 내포하게되는데, 이 모든것에 대한 설정이 문제
        
        > **CRA 케이스** : 정말 많은 Webpack, Babel 등의 세부설정들 신경쓸 필요없음
        > 
        - **@SpringBootApplication** : Spring Boot 에 필요한 모든것을 세팅 및 기본 설정
            - @SpringBootConfiguration : @Configuration 을 통해 **추가 @Bean 등록 가능**
            - @EnableAutoConfiguration : 사전 정의된 라이브러리들에 대한 **기본(Default) 설정값**
            - @ComponentScan : XML 아닌 @Controller 등 어노테이션 기반 **Bean 수집 규칙 정의**
    
    **Spring 과 Spring Boot 의 차이**
    
    - **Spring**
        - **WAR (Web Application Archive) 생성**
            
            : Servlet Container 에 배치할 수 있는 웹 애플리케이션 압축 포맷
            
        - **외장 톰캣 필요 (의존성)**
            
            : WAR 를 구동시킬 별도의 웹 컨테이너(WAS)
            
            - **이미 구동중인 서버**에 어플리케이션을 배포한다 (WAR 를 배포한다)
                - 극단적으로는 단일(하나의) Tomcat 서버에 복수의 WAR 배포도 가능하다.
    - **Spring Boot**
        - **JAR (Java Archive) 생성**
            
            : JRE 로 바로 실행 가능한 자바 어플리케이션 압축 포맷
            
            - **[공식 Spring 문서 Executable Jar Format](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)** : JAR, WAR 가능
        - **내장 톰캣 정의**
            
            : 언제 어디서나 같은 환경에서 스프링 부트 배포
            
            - WAS 서버를 구동시킴과 동시에 어플리케이션을 배포한다.
                - WAR 와 달리 단일(하나의) Tomcat 서버에는 하나의 어플리케이션만 구동될 수 있다.
    
    ### Spring MVC 와 3-Layered Architecture
    
    Spring 은 **MVC 아키텍쳐 패턴** 과 **3 계층 아키텍쳐 패턴** 으로 구성 및 동작
    
    - **MVC 아키텍쳐 패턴**
        - **Front Controller**
            - URL 에 알맞은 **Controller** 를 찾아(HandlerMapping) 호출(HandlerAdapter) 역할
                - Controller 는 View name 및 **Model** 을 반환
            - View name 에 알맞은 View Template 를 찾아서 Model 과 결합하며 **View 생성**
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/133daea9-40e6-4441-bf67-c1fcd234b867/Untitled.png)
        
    - **3 계층 아키텍쳐 패턴** : 상세 구현에서 관심사의 분리
        - **Presentation Layer** : 앞서 설명한 MVC 아키텍쳐 패턴
        - **Business Layer** : 위 이미지에서 Service, Repository 부분
            
            > *“Spring 프레임워크는 개발자에게 **“비지니스 구현”** 에만 신경쓰도록 한다.”*
            > 
            - 위 문장에서 ***“비지니스 구현”*** 에 해당하는 부분
        - **Data Access Layer** : 비지니스 구현을 위한 데이터 조회와 같이 CRUD 제공
    
    ---
    
    Spring 에서의 **MVC 아키텍쳐 패턴 상세 설명**
    
    - **Front Controller** 패턴이란 무엇인가?
        
        Spring 은 Java 기반 웹 어플리케이션 프레임워크이기에 Java 기반 WAS 인 **Tomcat** 내 동작
        
        Tomcat 은 **Servlet** Container 를 기반으로 동작하는 Java 기반 WAS
        
        Servlet 은 Java 를 웹 어플리케이션으로써 동작 가능하게해주는 웹 표준 기술
        
        - **Front Controller 이전의 Servlet : Java EE 시절**
            
            Java 는 거대 웹 어플리케이션 프로젝트를 위해 **Java EE** 라는 Java 웹 표준 기술을 만들었었다
            
            - Java 의 웹 표준 중 **Java 기반 CGI 프로그램 표준으로 Servlet 이 등장**
            - 이때, Java 의 웹 표준 중 JSP 도 등장했었다.
                - 당시 Java EE 표준 상에서의 Servlet 은 URL 마다 할당되어 개발되었다.
                    - GET `/hello` → HelloGetSerlvet
                    - POST `/hello` → HelloPostSerlvet
                    - DELETE `/world` → WorldDeleteSerlvet
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/09861573-43aa-49ee-8524-74cdc5971d60/Untitled.png)
                    
        - **Front Controller 적용된 Servlet : Spring 시절**
            - Spring 에서는 Servlet 을 URL 마다 정의하지 않고, 단일 Serlvet 만을 사용
                - * → **DispatcherSerlvet**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/de4290c6-c768-49c8-882a-4dec88839ae8/Untitled.png)
                
            - 왜 Front Controller 가 필요한가? ⇒ **중앙화 = 중앙처리 + 중앙관리**
                1. Controller 호출의 **중앙화**
                    - HandlerMapping (**중앙관리**) : URL 마다 Controller 들을 정돈/검색
                    - HandlerAdaptor (**중앙처리**) : 앞서 찾은 Controller 호출을 담당
                        - 무조건 (공통적으로) Model 과 View 이름을 반환한다
                2. View 생성의 **중앙화**
                    - ViewResolver (**중앙관리**) : View 이름마다 Template 파일들을 정돈/검색
                    - View (**중앙처리**) : 앞서 찾은 Template 에 Model 을 합쳐 반환할 View 렌더링
                - **참조하면 좋은 글 : [Front Controller 를 직접 구현해보며 이해하기](https://devraphy.tistory.com/502)**
            
    - **Front Controller** 상세 흐름 (Spring)
        1. EC2 서버에서 Tomcat 이 처음 구동될때 2개의 Container 가 생성된다.
            
            > *참고 : **Container = Context** 라고 생각하는것이 이해에 편하다*
            > 
            - **Servlet Container (ServletContext)**
                - 가장 먼저, ServletContext 이 생성됨
                - ServletContextListener 이 실행됨
                    - 아래 Spring Container (ApplicationContext) 생성
            - **Spring Container (ApplicationContext)**
                - 하나로 표현하긴했지만, 실제로는 2개의 계층으로 이뤄져있다. ([**추가 설명](https://jaehun2841.github.io/2018/10/21/2018-10-21-spring-context/#Web-Application-Context), [2](https://yeoooo.github.io/spring/day23/)**)
                    - Servlet Web**ApplicationContext** (구 servlet-context.xml)
                        
                        > Servlet 에서만 사용하는 Bean
                        > 
                        - HandlerMapping
                        - ViewResolver
                        - @Controller Beans 등
                    - Root Web**ApplicationContext** (구 root-context.xml)
                        
                        > 인프라, 비지니스 서비스 등 공통 Bean
                        > 
                        - @Service
                        - @Repository
                        - @Compoentn
                        - @Configuration 등
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/422a8a78-3698-472c-9dda-15b5df4e77b0/Untitled.png)
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/6a615a73-1759-4795-b15d-8233d2473a0d/Untitled.png)
                    
        2. Tomcat 및 Container 들이 모두 생성된 뒤에는 클라이언트 요청을 받을 수 있다.
        3. 클라이언트 요청에 따라 Tomcat 은 정적 페이지가 존재하는지 확인
        4. 정적 페이지가 존재하지않는다면, **Servlet Container** 가 요청을 받아 **Servlet** 할당
            - 단일 DispatchServlet 생성
                
                : 앞서 설명했듯, 원래는 URL 규칙에 따라 다양한 Servlet 생성인데, Spring 에선 단일
                
        5. **DispatchServlet** 은 **Front Controller** 로써 역할을 충실히 해낸다.
            
            : 여기서부터 복잡한 이유는 Front Controller 가 모든걸 중앙화해 복잡도를 혼자 다 끌어안음
            
            1. Controller 호출의 **중앙화**
                - HandlerMapping (**중앙관리**) : URL 에 따른 Controller Bean 검색
                - HandlerAdaptor (**중앙처리**) : 앞서 찾은 Controller Bean 호출에 대한 실행을 위임
                    - Controller Bean 은 결과로 Model 과 View 이름을 반환
                        - **ModelAndView** 객체로 반환하거나 (구버전 Spring 에서)
                            
                            ```java
                            @RequestMapping(value = "/")
                            public ModelAndView index() {
                                **ModelAndView mav = new ModelAndView();**
                            
                                List<BoardDto> lists = boardService.getLists(start, end, searchKey, searchValue);
                                mav.addObject("lists", list);
                                **mav.setViewName("main/index");**
                            
                                return mav;
                            }
                            ```
                            
                        - **Model** 객체와 **View 이름**(String) 반환 (최근 Spring 방식)
                            
                            ```java
                            @RequestMapping(value = "/")
                            public String index(**Model model**) {
                                model.addAttribute("lists", boardService.getLists(start, end, searchKey, searchValue));
                                **return "main/index";**
                            }
                            ```
                            
            2. View 생성의 **중앙화**
                - ViewResolver (**중앙관리**) : Controller가 반환한 View 이름에 맞는 Template 검색
                - View (**중앙처리**) : 앞서 찾은 Template 에 Model 객체를 합쳐 반환할 View 렌더링
                    - **Server-side Template Engine**
                        
                        : 템플릿 양식과 특정 데이터 모델에 따른 입력 자료를 합성하여 결과 문서를 출력
                        
                        = View Template + Model = View 생성
                        
                        - JSP, Thymleaf 등 다양. 현재 Spring 표준은 **Thymleaf**
                        - **Thymeleaf** 는 다른 템플릿 기술과 달리 HTML 형태로 프로토타입 확인가능
                        
                        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/9f203e78-4a17-48c5-8fa8-bc5f1c3d5882/Untitled.png)
                        
                    - Client-side Template Engine
                        - React, Vue 등
        6. 최종적으로 View 가 클라이언트에게 반환된다.
    
    ---
    
    **3-Layered Architecture**
    
    목적은 **관심사의 분리 (Separation of Concern)** = 높은 유지보수성과 쉬운 테스트
    
    1. **Presentation Layer** : 클라이언트 요청에 따른 실행, 화면 생성 및 반환 = **Spring MVC 구조**
    2. **Business Layer** : 비지니스 로직 수행 - Controller 가 반환하는 **Model 에 채울 데이터 생성**
        - @Service
    3. **Data Access Layer** : 어플리케이션 영속성 유지 및 CRUD
        - **Repository ~= DAO** : 이 두 개념은 다르나, Spring 에서 혼용해서 쓰기도한다.
            - **DAO** : DB 에 직결된 CRUD 함수 (구현체는 EntityManager 통해 수행)
                
                ```java
                public interface UserDao {
                    void create(User user);
                    User read(Long id);
                    void update(User user);
                    void delete(String userName);
                }
                ```
                
            - **Repository** : Encapsulating DAO (DAO 의 조합) = 필요한 함수만 쓰기
                - CRUD 중 R 만 쓸수있도록 Encapsulating
                
                ```java
                public interface UserRepository {
                    User get(Long id); // 원하는거만 구현해도된다. 내부에선 Dao 를 조합
                    // void add(User user);
                    // void update(User user);
                    // void remove(User user);
                }
                ```
                
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d6bc8551-a1da-42cf-adf8-2a6a39a52b51/Untitled.png)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/8c680017-aca3-4988-bb69-909fa88ccede/Untitled.png)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/acdac76d-7557-4e6d-831a-3e853799a5ad/Untitled.png)
    
    - **DTO (Data Transfer Object) 는 무엇인가? = 그냥 데이터 담은 객체 (가변성)**
        
        : 그냥 데이터를 갖고있는 객체 (다양한 메서드를 가졌다기보다 **Getter, Setter 메소드만 갖는다**)
        
        데이터들은 DTO 객체 형태로 여러 메소드와 객체들을 여행(?)한다.
        
    - **VO (Value Object) = DTO 에서 Setter 메소드를 빼면 VO (불변성)**
        - DTO = 가변 객체 | VO = 불변 객체 (MyBatis 쿼리를 통해 가져와진 객체 = VO)
            - 예) MyBatis 로 DB 로부터 읽어온 SELECT 결과 객체
    
    ---
    
    **MSA (Microservice Architecture)**
    
    > 왜 Spring 하면 항상 MSA 라는 말이 등장하는가?
    > 
    - 엔터프라이즈 웹 제작을 위해서는 많은 설정 및 하나의 무거운 서버를 필요로함
    - Spring 의 등장과 함께 어플리케이션 설정 및 개발의 경량화
        - **모놀리딕 → 마이크로서비스 아키텍쳐**
            
            : 단일점 에러(SPoF) 방지를 위해 여러 서버들을 도메인별 쪼개어 개발 수월
            
    - **API GW (Gateway)** 의 등장
        - 하나의 모놀리딕 서버에서 함수 → 함수로 전달하던것이
        - 다수의 마이크로 서버간에 서버 → 서버 전달로 변경됨에 따라
            - API 개수가 기하급수적으로 증가
                - 수많은 마이크로서비스의 수많은 API 관리를 위한 API GW 필수
                - RESTful REST API 설계가 중요해짐
                    - 행위(동사) = Method
                    - 결과(명사) = Resource
    - **MSA 내 각 마이크로서비스들은 독립적**이기 때문에
        - 각각 가장 적합한 언어, 버전, DB 도 제약없이 선택 가능
        - 하나의 마이크로서비스에서 변경이 발생하여도 다른 서비스에 영향 X
        - 서로 다른 기본 기술과 배포 일정을 선택 가능 및 유지보수성 향상
    
    [좋은 Spring & Java 코드를 위한 방법](https://www.notion.so/Spring-Java-f07838174fcc4e8980803c0e4ed9f9e5?pvs=21)


  ## DB 와 Java (Web) Application 사이의 연결 및 쿼리 사용

  Java 는 JVM 위에서 동작하는 어플리케이션이고, DB 는 개별적으로 존재하는 시스템이다.
  Java 가 DB 를 사용하기 위해서는 아래 두 절차가 필요하다.

    1. **DB 연결** : 가장 먼저 **Java Application 이 DB 에 접속**하여야하고
    2. **DB 조작** : **DB 내 데이터 조작을 위한 언어인 Query** 와 **반환 결과를 Java 객체로 변환**해야한다.

  여기서 ****객체-관계형****(Object-Relational) ****모델 불일치**** 해결을 위해 객체 기반의 DB 조작인 ORM 이 등장

    - **객체-DB 조작** : DB 내 데이터에 대응되는 Entity 객체에 대한 조작을 통해 **간접 DB 조작**
        - **ORM**, Object-Relational Mapping

  ### 1. DB 연결 : **JDBC Driver**

  Java 가 DB 를 사용하기 위해 가장 먼저 필요한것은 Java Application 과 DB 를 연결하는 것

  DB 접속을 관리하기 위해 아래와 같이 **물리적 접속, 논리적 접속** 두 레이어를 두고있으며, 두 방식 중 하나를 선택하면 되는데, 전자는 검색해보면 구식의 방식으로 보이며, Spring 에서도 후자를 사용한다.

    - **DriverManager : 물리적 접속** = **Connection 자체 생성 및 파기**

      : DB 접속이 필요할때마다, DriverManager 생성 및 쿼리 수행 후 DB 접속 **Close**

        - 각 DBMS 업체마다 DriverManager 에 맞는 Driver 구현하여 제공 (MySQL, PostgresQL 등)
        - **접속 → 쿼리 → 종료** : Connection Pool, 분산 트랜잭션 지원 불가

        ```java
        // 1) 접속과 동시에 Connection 을 반환하는 코드
        **Connection connection = DriverManager.getConnection(URL, USER, PASSWORD);**
        ```

    - **DataSource : 논리적 접속** = Connection 을 위한 Factory (**미리 생성되어있는 Connection 반환**)

      : DB 접속이 필요할때마다, DataSource 에게 요청 및 쿼리 수행 후 DB 접속 반환

        - Connection Pool 중 한 접속을 가져와 원하는 쿼리 처리 후 **해당 접속을 다시 환원**
            - **주의** : DataSource 구현체 중 어떤건 자체 Connection Pool 을 갖고, 어떤건 갖고있지 않음
        - Spring Boot 2+ 부터 DataSource 표준은 **HikariCP**

            ```java
            
            // 0) DataSource 정의
            HikariConfig config = new HikariConfig();
            config.setJdbcUrl(URL);
            config.setUsername(USER);
            config.setPassword(PASSWORD);
            config.setDriverClassName("com.mysql.cj.jdbc.Driver");
            config.setMaximumPoolSize(20);
            config.setMinimumIdle(10);
            HikariDataSource hikariDataSource = new HikariDataSource(config);
            
            // 1) 원하는 Connection 을 Pool 에서 가져오는 코드
            **Connection connection = hikariDataSource.getConnection();**
            ```

        - **Connection Pool 최대 커넥션 수 설정**
            - 커넥션을 계속해서 재사용하기 때문에 생성되는 **최대 커넥션 수**를 제한적으로 설정하며, 커넥션 풀에 **사용 가능한 커넥션이 없을 경우 사용자는 커넥션이 반환될 때까지 순서대로 대기**

              **대기중에 지정한 timeout 시간이 만료되면 예외 발생**

            - 성능적인 부분을 고려할 때 **최대 커넥션 수는 WAS Thread 와 함께 고려해야한다.**
                - **WAS Thread Pool 스레드 개수 > Connection Pool 커넥션 수**

                  : 예를 들어서 Thread Pool 의 크기보다 Connection Poo l의 크기가 더 클 경우 트랜잭션을 처리하는 Thread 가 사용하는 Connection 외에 남는 Connection 은 실질적으로 메모리 공간만 차지하게 되기 때문


  ### 2.1. DB 조작 : **JDBC APIs**

  JDBC 는 DB 접속, 쿼리, 결과에 대한 명세(API)를 제공. Spring 에서 학습했던 DAO 의 개념에 매핑된다.

  **JDBC APIs** 는 크게 세가지만 이해하면된다.

    - **Connection 객체** : DB 접속 (위 JDBC Driver 를 통해 취득한)
    - **Statement 객체** : DB 쿼리
        - **PreparedStatement 객체**
            - **SQL 인젝션 방어** : 작은 따옴표 및 기타 특수문자를 변환 (Java 이스케이핑)
                - SQL 인젝션 : string-concatenating 기법으로 쿼리 내 파라미터를 주입할때 발생

                  : name 변수에 `“' OR 1=1 AND 'a'='a”` 입력 시 모든 MEMBER 결과 조회

                    ```java
                    "SELECT * FROM MEMBER WHERE NAME = '" + name + "'"
                    ```

                - PreparedStatement 는 **내부적으로 이스케이핑 수행 후 값 주입**

                    ```java
                    String prepareStatement = "SELECT * FROM MEMBER WHERE NAME = ?";
                    PreparedStatement preparedStatement = connection.prepareStatement(prepareStatement);
                    preparedStatement.setString(1, loginName);
                    ```

            - **캐싱** : SQL Statement 구문 기반 캐싱 ([**구체적 동작 원리 및 설정**](https://stackoverflow.com/a/12141681))
                - **“Statement 자체”를 키로 사용하여 캐싱**
                    - `select a, b from t where c = 3`
                    - `select a, b from t where c = 2` → 앞선 `c = 3` 실행 플랜 재사용 불가
                    - `**select a, b from t where c = ?**` → **Statement 기반 키로 재사용 가능**
    - **ResultSet 객체** : 위 DB 쿼리의 결과
    - 예시 코드를 통해 이해하기 : **DAO** → **DataSource** → Connection 을 가져와 **DB 조작**

        ```java
        @RequiredArgsConstructor
        public class MemberDao {
            private final DataSource dataSource;
            
            public int count() throws SQLException {
        		    try {
        		        Connection connection = dataSource.getConnection();
        		        Statement stmt = connection.createStatement());
        		        ResultSet resultSet = stmt.executeQuery(
        		            **"select count(*) from MEMBER"**
        			      )
        				    resultSet.next();
        		        int result = resultSet.getInt(1);
        						return result;
        		    } catch (SQLException e) {
        		      // 예외처리
        		    } finally {
        		      // 자원반납
        		      if (stmt != null) stmt.close();
        		      if (connection != null) connection.close();
        		    }
            }
        }
        ```


    ### 2.2. DB 조작 : JDBC Template (Spring)
    
    - **간편 사용 : Spring** 은 **JDBC APIs** 의 3개 인터페이스를 한데 묶어 ****JdbcTemplate**** 로 간편히 제공
        - **실제로 개발자가 작성하는 부분의 코드는 2개뿐**
            1. 단 한줄의 Query 코드 : `"select count(*) from MEMBER"`
            2. 어떤 결과로 반환할지에 대한 코드 : `int result = resultSet.getInt(1);`
        
        ```java
        @RequiredArgsConstructor
        public class MemberDao {
            private final DataSource dataSource;
          
        		public Member selectByEmail(String email) {
        			List<Member> results = jdbcTemplate.query(
        					"select * from MEMBER where EMAIL = ?",
        					// 저번에 학습했던 **익명 구현 객체**에 해당 (**익명 함수**를 위한)
        					new RowMapper<Member>() {
        							@Override
        							public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
        									Member member = new Member(
        											rs.getString("EMAIL"),
        											rs.getString("PASSWORD"),
        											rs.getString("NAME"),
        											rs.getTimestamp("REGDATE").toLocalDateTime());
        									member.setId(rs.getLong("ID"));
        									return member;
        							}
        					}, email);
        			return results.isEmpty() ? null : results.get(0);
        		}
        }
        ```
        
    - **구조적인 반복의 해결** : JDBC APIs 사용 시 **반복되는 패턴을 추상화**하여 제공
        1. **Try-with-Resources**
        2. **SQLException**
        3. **Transaction**
        - 위 JDBC APIs 예시 코드를 JDBC Template 으로 변환하면 아래와 같이 된다.
            
            ```java
                public int count() throws SQLException {
            				jdbcTemplate.queryForObject("select count(*) from MEMBER", Integer.class);
            		}
            ```
            
    - **간편 트랜잭션** : JDBC Template 는 Spring 이 제공하는것이기에, 어떤 설정없이 트랜잭션 적용
        - **@Transactional 만 사용하면 됨** (JDBC API 에서는 **`connection**.commit()` 별도)
            - **로컬 트랜잭션** : 하나의 DB Connection에 종속
            - **글로벌 트랜잭션** : 트랜잭션 관리자를 통해 트랜잭션을 관리하는 글로벌 트랜잭션 방식을 사용
                - Spring 은 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술을 제공
                    - JDBC, Hibernate, JPA 등 너무 다양한 글로벌 트랜잭션 API 들 **모두 추상화**
                    - **@PlatformTransactionManager 을 통해 → 트랜잭션 경계 설정**
    
    ## **객체-DB 조작** : ORM → JPA (**Java 표준 명세 API)**
    
    앞선 JDBC (APIs 그리고 Template 모두) 처럼 DB 를 직접적으로 사용한다면 아래의 불편함을 마주할 것
    
    - **Connection 객체** : 트랜잭션에 대한 관리를 개발자 개인이 신경써야한다.
    - **Statement 객체** : 일일히 원하는 쿼리문을 Native 하게 작성해야한다.
    - **ResultSet 객체** : 결과(ResultSet)에 대한 매핑 함수(RowMapper)를 정의해야, 객체로 사용가능
    
    ### 0. ****The Object-Relational Impedance Mismatch****
    
    이 중 DB 의 **“관계형 모델”**과 **“객체 모델”** 사이의 매핑 이슈가 제일 복잡하고 불편하다.
    다양한 불일치성이 있고, 여러 글마다 정리해놓은것들이 많은데, 몇개만 추려 정리하자면 아래와 같다.
    
    - **Granularity** : 객체 모델이 관계형 모델보다 세분화 될 수 있다
    - **Subtypes (Inheritance)**
        - 객체 모델에서는 상속이 자연스러운 패러다임이지만
        - 관계형 모델은 표준이 아니다.
    - **Identity**
        - 관계형 모델에서는 PK 하나로 일치여부를 판단하지만
        - 객체 모델에서는 `==`  와 `equals()` 로 일치여부를 판단한다.
    - **Associations**
        - 관계형 모델에서는 FK 로 연관된 테이블 참조가 가능하나
        - 객체 모델에서는 각각의 객체에서 선언해야 한다.
    - **Data Navigation**
        - 객체 모델은 객체간 데이터 탐색을 필드 참조를 사용하지만
        - 관계형 모델에서는 JOIN 을 통해 한번에 불러와서 탐색한다.
    
    ### 1.1. ORM (Object-Relational Mapping)
    
    DB 의 **“관계형 모델”** 과 **“객체 모델”** 을 자동으로 매핑하여, **Entity 객체에 대한 조작 결과**가 **DB 로 적용**
    
    - Entity 객체에 대한 조작 : (아래 JPA 에서는) JPQL
    - DB 로 적용 : Transaction 이 정상 commit() 되었을 시 SQL 로 전환되어 DB 에 적용
    
    ### 1.2. JPA (Java Persistence API)
    
    DB 의 **“관계형 모델”** 과 **Java** 의 **“객체 모델”** 을 자동으로 매핑해주는 ORM 기술에 대한 Java 표준 명세
    
    - 직접 SQL 문을 사용하지 않고, **Java Entity 객체에 대한 JPQL 문을 통해 DB 에 간접 접근, 조작**
        - 내부적으로 JDBC 를 사용 (JDBC Template 즉, JDBC APIs 를 감싸 JPA(ORM) 을 구현함)
    
    ### 1.2.1. **Persistence (영속성)**
    
    매번 데이터베이스에 접근하지 않고 EntityManager 를 통해 메모리(영속성 컨텍스트) 상에 작업을 한후 트랜잭션이 커밋되는 시점에 데이터베이스에 반영하는 구조
    
    **이 영속성(Persistence)은 JPA 의 가장 큰 특징**
    
    - **Persistence Context (영속성 컨텍스트)** : JPA 가 Entity 객체들을 모아두고 CRUD 하는 공간
        - **Entity 객체**들을 영구 저장할 수 있는 환경이며 논리적인 개념
        - Java 어플리케이션과 DB 사이에 있는 **1차 캐시 혹은 버퍼 개념**
            
            : **트랜잭션을 커밋하거나 플러시 호출 시 1차 캐시에 있는 Entity 변경 내역을 데이터베이스에 동기화**
            
            - 영속성 컨텍스트는 1차 캐시로써, **트랜잭션을 시작하고 종료할 때까지만 1차 캐시가 유효**
                - 애플리케이션 전체로 보면 데이터베이스 접근 횟수를 획기적으로 줄이지는 못하는 한계
            - **2차 캐시는 애플리케이션을 종료할 때까지 유지**
                
                : 네트워크를 통해 데이터베이스에 접근하는 시간 비용은 애플리케이션 서버에서 내부 메모리에 접근하는 시간 비용보다 수만에서 수십만 배 이상 비싸다.
                
                - **2차 캐시는 자동적으로 설정되지 않는다 !** 일반적으로 **EH**CAC**HE** (EhCache) 사용
                    - 1차 캐시 : (Hibernate Session) **Transaction-level Cache** of persistent data
                    - 2차 캐시 : **SessionFactory-level cache**
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/0e102981-b4aa-4c4e-8fa7-99621128d0b5/Untitled.png)
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/7376d94f-cfe8-425b-bdc3-81a51d9908ad/Untitled.png)
                    
                - 2차 캐시를 적정히 활용하면 데이터베이스 조회 횟수를 획기적으로 줄일 수 있다.
            - **2차 캐시를 사용할 경우 Hibernate SQL 로깅이 찍히지 않는다.**
                - **JPA 가 요청을 날리지 않기 때문 ([Baeldung 출처](https://www.baeldung.com/hibernate-second-level-cache#Cacheable))**
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/2ca8f028-98e1-4e71-8ffe-b4454220219d/Untitled.png)
                    
    - **Persistence Context (영속성 컨텍스트) 이점**
        - ****동일성 보장 (Identity)****
            - 1차 캐시를 통해 반복 가능한 읽기(REPEATABLE READ) 등급의 트랜잭션 격리 수준을
                - **DB가 아닌 아닌 애플리케이션 차원에서 제공**
        - ****엔티티 “등록” 시 트랜잭션을 지원하는 쓰기 지연 (Transactional Write-Behind)****
        - **엔티티 “수정” 시 변경 감지 (Dirty Checking) ⇒ Entity 를 DTO 로 쓸 시 위험한 기능**
    - **JQPL : Java Persistence Query Language**
        - **Persistence Context 내 Entity 객체를 대상으로 CRUD 하는 쿼리 언어**
            
            = JPA 에서 지원하는 SQL 의 추상화된 객체지향 쿼리 언어
            
            - **SQL 이 (DB) Table 을 쿼리한다면, JPQL 은 (Java) Entity 객체를 쿼리한다.**
        - **특정 DBMS 에 의존하지 않음. 단, 실제 DB 에 적용될때 SQL 전환하여 적용 필요**
            - 전환 시 DBMS 마다 다른 쿼리는 **Dialect** 라는 **추상화된 방언 클래스**를 통해 지원
                
                : DBMS 마다 조금씩 지원하는 쿼리 문법들이 다르다. 각 벤더에 맞는 구현체를 제공
                
                - 표준 SQL 인 **ANSI SQL** 을 기반으로, DBMS 각자 **독자적인 기능**을 위해 **추가 SQL** 존재
                    - PL/SQL (Oracle), PL/pgSQL (PostgresQL), T-SQL (MS-SQL) 등
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/1ae5522f-1305-4b35-bf5d-9610fe4394a3/Untitled.png)
                    
                - JPA 에서 **Dialect 만 설정해주면 해당 Dialect 를 참고하여 그에 맞는 쿼리를 생성**
                    
                    : JPA 에서 Oracle DB 에서 MySQL 로 바꾸면 Dialect 도 MySQLDialect 로 변환
                    
        - **SQL 과 문법이 유사** (SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 등)
            
            : **SQL 의 추상화된 객체지향 쿼리 언어**이기 때문에
            
            ```java
            String jpql = "select m From Member m where m.name like '%hello%'";
            List<Member> result = em.createQuery(jpql, Member.class).getResultList();
            ```
            
    - **JPA 의 인터페이스는 아래 3 요소로 구성**
        - **EntityManagerFactory** : 고객 요청마다 (Thread 가 하나 생성될 때마다) EntityManager 생성
            - WAS 로드 시점에 단 1개만 생성, WAS 종료 시점에 EntityManagerFactory 를 닫는다.
                
                : 닫을때 비로소 내부적으로 Connection pooling 에 대한 Resource 가 Release 됨
                
        - **EntityManager** : Entity 객체에 대한 CRUD 및 데이터베이스에 동기화 작업 수행
            - 쿼리 수행 단위에 해당하며 **스레드와 1:1 관계**
            - **Entity 생명주기 :** Persistence Context 내 EntityManager 가 관리
                - **비영속** (New/Transient) : 영속성 컨텍스트에 존재하지 않음
                - **영속** (Managed) : 영속성 컨텍스트에 **저장된 상태**
                - **준영속** (Detached) : 영속성 컨텍스트에 저장되었다가 **분리된 상태**
                - **삭제** (Removed) : **삭제된 상태**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/e0309e83-ae6b-46cd-88fd-c1a9062f01ed/Untitled.png)
                
                - **Entity 생명주기 상태를 변경하는 EntityManager 의 메서드들**
                    - em.**find**() : 엔티티 조회
                    - em.**persist**() : 엔티티 저장 = **영속성 컨텍스트에 저장**
                    - em.**remove**() : 엔티티 삭제
                    - em.**flush**() : 영속성 컨텍스트 내용을 데이터베이스에 반영
                    - em.**detach**() : 엔티티를 준영속 상태로 전환
                    - em.**merge**() : 준영속 상태의 엔티티를 영속상태로 변경
                    - em.**clear**() : 영속성 컨텍스트 초기화
                    - em.**close**() : **영속성 컨텍스트 종료**
                        - Transaction 수행 후에는 반드시 EntityManager 를 닫는다.
                            
                            : 그래야 내부적으로 DB Connection 을 반환한다.
                            
        - **EntityTransaction** : Data 를 “변경”하는 모든 작업은 반드시 Transaction 안에서 이루어져야 함
            - tx.**begin**() : Transaction 시작
            - tx.**commit**() : Transaction 수행
            - tx.**rollback**() : 작업에 문제가 생겼을 시
    
    ---
    
    - **JPA 장점 :** 결과적으로, JPA 을 활용한다면 개발자는 아래의 이점을 갖게 된다.
        - JDBC 반복 작업을 제거함으로써 **높은 생산성 & 유지보수성**
            - 객체 지향적인 코드 (**JPQL, Java Persistence Query Language**)
            - **비즈니스 로직에 집중**
            - 구조의 일관성 & 간결한 코드 & 재사용성 증가
        - JPA 인터페이스 추상화를 통한 **낮은 DBMS 종속성**
            - **낮은 결합도 & 교체 용이성**
    
    ### 1.2.2. **Hibernate = JPA 구현체**
    
    지연 초기화, 많은 Fetch 전략, 자동 버저닝과 타임스태핑을 통한 최적화 된 Lock 을 통한 우수한 성능 제공
    
    - Hibernate 의 구현체는 JPA 인터페이스 3요소를 아래와 같이 구현한것이다.
        - **SessionFactory (EntityManagerFactory)**
        - **Session (EntityManager)**
        - **Transaction (EntityTransaction)**
    
    ### 1.2.3. **ORM (Hibernate 로 대표) vs SQL Mapper (MyBatis 로 대표)**
    
    - ORM 은 **Persistence Context** 을 통해 **Entity** 객체를 조작하고, 그것이 DB 로 적용되도록 하는것
        
        > **Entity 객체 중심 개발** | 예, JPA, Hibernate
        > 
        - JdbcTemplate 의 불편함을 해결
        - 객체와 DB의 데이터를 자동으로 매핑
            - SQL 쿼리가 아닌 **메서드**로 데이터를 조작할 수 있습니다.
                - 객체간 관계를 바탕으로 **SQL 을 자동으로 생성** 합니다.
    - SQL Mapper 는 **Persistence Context** 가 존재하지 않기때문에 **Entity** 도 없다.
        
        > **SQL 쿼리 중심 개발** | 예, JdbcTemplate, SqlSessionTemplate
        > 
        - **SqlSessionTemplate** 을 사용하며, ****Mapper.xml 를 통해 간편하게 쿼리 및 매핑 정의
            - 최신 버전의 MyBatis 는 **SqlSessionTemplate** 이 아닌 **MapperInterface** 사용
        - “Entity 객체” 가 아닌 **“그냥 객체”** 와 (Native, DBMS) **SQL 쿼리**를 연결한 것
            
            : Java 의 **“그냥 객체”** 와 SQL 을 Mapping 해준다. 는 의미에서 ⇒ **SQL Mapper**
            
    
    ## JPA 추상화 방법
    
    JPA 를 추상화한 방법이 2개라고 보면된다 : **Spring Data JPA** & **QueryDSL**
    
    - 어쨌거나 둘 다 JPA 중 Hibernate 를 기반으로 추상화되어있음
    
    ### 1. Spring Data JPA
    
    > **Repository** : JPA를 한 단계 더 추상화시킨 인터페이스
    > 
    
    지루하게 반복되는 CRUD 문제를 세련된 방법으로 해결
    
    - 세련된 방법 : 구현클래스 없이 인터페이스를 통한 **Query Method 만 작성**해도 개발 완료
        - Spring Data JPA 자체적으로 **작성한 Query Method 의 이름을 분석해서 JPQL 을 실행**
            
            : 사용자가 Repository 인터페이스에 정해진 규칙대로 메소드를 입력하면,
            Spring 이 알아서 해당 메소드 이름에 적합한 쿼리를 날리는 구현체를 만들어 Bean 등록
            
            - 이것이 **우리가 사용하는 Repository 가 메소드만 딸랑 정의되어있는 인터페이스인 이유**
    
    우리는 사실 JPA 라고하면 Repository 밖에 기억나지 않을것이다.
    
    JDBC, JPA, 심지어 Hibernate 를 쓴다고하지만 그것조차도 Gradle 외엔 직접 본적도, 직접 접할일도 없다.
    
    - Spring Data JPA 까지 우리는 총 3번의 추상화를 거쳤다.
        - JDBC APIs → **JDBC Template** → **JPA** (ORM, Hibernate) → **Spring Data JPA**
    - **결국 주니어 개발자들이 접하는 Spring 에서의 JPA 는 Spring Data JPA 고 Repository 에 그친다.**
        - 혹시라도 커스텀하게 SQL 쿼리를 사용하고싶다면/해야한다면, **@Query** 혹은 **QueryDSL**
            - **@Query** : **JPQL** (Java Persistence QL) + **Query Method**
            - **QueryDSL** : 객체 기반으로 **HQL** (Hibernate QL) 작성
    - Spring Data JPA 의 Repository 상세 구현 살펴보기 : **SimpleJpaRepository**
        - Spring Data JPA 의 Repository 의 구현에서 JPA 를 사용하고 있음을 볼 수 있다.
            
            **: 내부적으로 EntityManager 사용**
            
            ```java
            public class SimpleJpaRepository<T, ID> implements JpaRepositoryImplementation<T, ID> {
            
                **private final EntityManager em;**
            
                public Optional<T> findById(ID id) {
                    Assert.notNull(id, ID_MUST_NOT_BE_NULL);
                    Class<T> domainType = getDomainClass();
                    if (metadata == null) {
                        return Optional.ofNullable(em.find(domainType, id));
                    }
                    LockModeType type = metadata.getLockModeType();
                    Map<String, Object> hints = getQueryHints().withFetchGraphs(em).asMap();
                    return Optional.ofNullable(type == null ? em.find(domainType, id, hints) : em.find(domainType, id, type, hints));
                }
            }
            ```
            
    - **JPA vs Spring Data JPA** 차이에 대한 그림
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/b4d29c71-e98b-42b8-9f15-08d563960869/Untitled.png)
        
    
    ### 1.1. Query Method 기능
    
    Spring Data JPA **메소드 이름만으로 쿼리를 생성하는 기능 = JPQL 을 생성 및 실행**
    
    - **후술할 파라미터 바인딩, 유연한 반환 타입, 페이징 및 정렬 지원 모두 JPQL 에서 비롯된 것**
    
    ```java
    public interface MemberRepository extends JpaRepository<Member, Long> {
    		Member findByUserName(Stirng username);
    }
    
    public interface ItemRepository extends JpaRepository<Item, Long> {
    		Member findByUserName(Stirng username);
    }
    ```
    
    - 쉬운 이해를 위한 **CrudRepository** 인터페이스 코드 : Entity 에 대한 CRUD 기능 제공
        - 본 기본 CrudRepository 를 확장하여 추가적인 기능을 가진 Repository 들이 생성된다.
            - interface **PagingAndSortingRepository** extends CrudRepository
        
        ```java
        @NoRepositoryBean
        public interface CrudRepository<T, ID> extends Repository<T, ID> {
        		<S extends T> S save(S entity);
        		Optional<T> findById(ID id);
        		boolean existsById(ID id);
        		Iterable<T> findAll();
        		long count();
        		void deleteById(ID id);
        		void delete(T entity);
        }
        ```
        
    - **Custom Query Method** 를 만들고 사용하기 위한 2가지 방법
        - **@NamedQuery** : Entity 클래스에 **NamedQuery** 정의하고 **Query Method Name** 과 연결
            
            ```java
            @Entity
            @NamedQuery(
            		name="Member.findByUsername",
                query="select m from Member m where m.username = :username"
            )
            public class Member {
            		...
            }
            ```
            
        - **@Query** : Repository 내 Query Method 를 정의하고 **거기에 맞는 Query 주입**
            - 실무에서는 @NamedQuery 보다 @Query 를 많이 활용
                - **위치 기반** 파라미터 바인딩
                
                ```java
                public interface MemberRepository extends JpaRepository<Member, Long> {
                		@Query("select m from Member m where m.username = ?1")
                		Member findByUserName(Stirng username);
                }
                ```
                
                - **이름 기반** 파라미터 바인딩
                
                ```java
                public interface MemberRepository extends JpaRepository<Member, Long> {
                		@Query("select m from Member m where m.username =  :username")
                		Member findByUserName(@Param("username") Stirng username);
                }
                ```
                
        
        ---
        
        - **파라미터 바인딩 2가지 방식**
            - **위치 기반** : `"select m from Member m where m.username = ?1"`
            - **이름 기반** : `"select m from Member m where m.username = :name"`
    - **유연한 반환 타입** : 반환값에 따라 단일 객체로 반환하거나 Collection 객체로 반환되는 유연성을 가졌다.
        
        ```java
        Member findByName(Stirng name);
        
        List<Member> findByName(Stirng name);
        ```
        
    - **페이징 및 정렬 지원** : 페이지를 위한 파라미터 지원 : **Pageable** & 정렬을 위한 파라미터 지원 : **Sort**
        
        ```java
        Page<Member> findByName(Stirng name, Pageable pageable);
        
        List<Member> findByName(Stirng name, Pageable pageable);
        
        List<Member> findByName(Stirng name, Sort sort);
        ```
        
    - **비동기 호출가능** : @Async 와 Future 사용
        
        ```java
        public interface CommentRepository extends JpaRepository<Comment, Long> {
        		@Async
        		Future<Comment> findCommentsByContent(String content);
        		
        		@Async
        		@Query("select c from Comment c where c.content=:content")
        		Future<Comment> findCommentsByC(@Param("content") String content);
        }
        ```
        
    
    ### 2. Query DSL
    
    Spring Data JPA 처럼 JPA (특히나 JQPL) 를 추상화하여 쉽게 쓰기위한 기술
    실무에서는 JQPL 써야되는 순간들에는 거의 모두 QueryDSL 를 사용한다고 생각하면된다.
    
    **Spring Data JPA 의 JpaRepository 와 같은 계층이고, 둘을 합쳐 사용할 수 있다.**
    
    - JPQL이 제공하는 모든 검색 조건을 제공
        - **JPQL** 비교를 위한 예시
            
            ```java
            public interface UserRepository extends JpaRepository<User, Long> {
            
            		@Query("SELECT u FROM User u WHERE u.name = :name")
            		Collection<User> findAllActiveUsers(@Param("name") String name);
            }
            ```
            
        - **QueryDSL** 비교를 위한 예시 (**Query DSL 의 3가지 구현 방식**)
            - 1) 상속/구현 없이 간단하게 만드는 **Repository 방법**
                
                ```java
                @RequiredArgsConstructor
                @Repository
                public class UserRepositorySupport {
                
                	  private final JPAQueryFactory jpaQueryFactory;
                	
                	  public List<User> findByName(String name) {
                	    return jpaQueryFactory.selectFrom(QUser.user)
                	        .where(QUser.user.name.trim().eq(name))
                	        .fetch();
                	  }
                }
                ```
                
            - 2) 처음 공부하는 사람을 위한 상세한 코드 : **QuerydslRepositorySupport 상속 방법**
                
                ```java
                @Repository
                public class UserRepositorySupport extends **QuerydslRepositorySupport** {
                
                	  private final JPAQueryFactory jpaQueryFactory;
                	
                	  public UserRepositorySupport(JPAQueryFactory jpaQueryFactory) {
                		    super(User.class);
                		    this.jpaQueryFactory = jpaQueryFactory;
                	  }
                	
                	  public List<User> findByName(String name) {
                	    return jpaQueryFactory.selectFrom(QUser.user)
                	        .where(QUser.user.name.trim().eq(name))
                	        .fetch();
                	  }
                }
                ```
                
            - 3) Spring Data JPA 의 **JpaRepository** 와 같이 사용 ([**Spring Custom Repository**](https://docs.spring.io/spring-data/jpa/docs/2.1.3.RELEASE/reference/html/#repositories.custom-implementations))
                
                ```java
                public interface UserRepositoryCustom {
                    List<User> findByName(String name);
                }
                
                @RequiredArgsConstructor
                public class UserRepositoryCustomImpl implements UserRepositoryCustom {
                
                  private final JPAQueryFactory jpaQueryFactory;
                	
                	@Override
                  public List<User> findByName(String name) {
                    return jpaQueryFactory.selectFrom(QUser.user)
                        .where(QUser.user.name.trim().eq(name))
                        .fetch();
                  }
                }
                
                public interface UserRepository extends JpaRepository<User, Long>, UserRepositoryCustomImpl {
                }
                ```
                
            
            > 현업에서 가장 많이 사용되는것은 3번 Spring Custom Repository
            > 
            > - ***“Spring Data JPA 의 JpaRepository 와 같은 계층이고, 둘을 합쳐 사용할 수 있다.”***
    - **Type Safety** : QueryDSL 의 경우엔 Query 검증을 Compile Time 에 진행가능 (Java 객체 문법)
        - JPQL 은 Runtime Error (Compile Error X) 발생 = String 으로 구성된 Query 이기에
    - **Consistency 일관성** : JPA, MongoDB, Scala 어떤것이든 **QueryDSL 사용법은 모두 동일**
    
    ### 2.1. Query DSL 사용을 위한 선결 조건 : Query Type
    
    Entity 클래스 앞에 Q 가 붙은 이름을 갖는다. (User Entity 객체는 **QUser**)
    
    - 위에서 살펴본 **Query DSL 의 3가지 구현 방식**의 JPAQueryFactory 에서
        - Query DSL 쿼리 작성을 위해 꼭 필요로 하는 **정적 Type 변수**이다.
        - Spring @Controller 잘 작성하는 방법
        
        ### **Exception 발생 근원지(?, Source)**
        
        Enum Exception 처리 로직을 String → Enum 로직과 떨어트리는게 좋은가? **No**
        
        - Enum 내 String → Enum 변환 **정적 메서드**
            - **이전** : null 을 반환하고, `from()` 메서드 **외부에서 Exception 발생**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/e907454a-1b63-432f-9a88-3622b590bb77/Untitled.png)
                
            - **이후** : **String** 에 해당하는 **Enum** 이 존재하지 않는 경우 **내부에서 Exception 발생**
                - Enum 미존재 Exception 은 String → Enum 변환 로직과 분리될 수 없다.
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/8a71dbd2-1470-487f-a5eb-daf4e0317276/Untitled.png)
                
        
        ### @RequestBody 의 Null + Validation 처리
        
        GetProducWithCondition 메서드 내 SearchReqDto 내 필드를 가져다 쓰는데 2가지를 선처리한다.
        
        - **Null 인지 여부를 검사**하고, 심지어 기본값을 주입해주는 로직을 가지고있다.
        - **유효한 날짜인지 여부를 검사**한다.
            - **이전** : 이 모든 로직들이 아래 코드에서 **약 16줄의 라인을 차지한다.**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/ee7ca79d-31fb-4a1e-a211-eb03e941fd80/Untitled.png)
                
            - **이후** : 이 모든 로직들은 **DTO 내 Getter 메서드 호출할때 혹은 JSON → 객체 Deserialize 때로 이관**
                - DTO 는 다음과 같다. 이를 기준으로 아래 순서에 따라 리팩토링 시도
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/6cfbe8fd-4800-465f-a252-9016e37f4c67/Untitled.png)
                    
                1. **Null 을 입력하지 않도록 막는 책임을 백엔드가 가져갈것인가, 프론트엔드가 가져갈것인가?**
                    - **프론트엔드가 가져간다면**, 백엔드는 **@NotNull 로 절대 Null 이어서는 안된다는 조건을 추가**
                    - **백엔드가 가져간다면**, 백엔드는 해당 필드를 Nullable 로 판단하고, **기본값을 넣어주도록 설정**
                        - 기본값을 설정할때도 sel_date 와 sel_date2 처럼 필드간 의존성을 두는것보다
                            
                            예) sel_date = “2023-10-10” & sel_date2 = “2023-10-11”
                            
                        - sel_date 와 Integer stay 와 같이 필드간 의존성을 없애는것이 좋지 않을까?
                            
                            예) sel_date = “2023-10-10” & stay = 1
                            
                2. **Validation 여부 판단을 Dto 를 사용하는곳에서 별개의 로직으로 구현하지않는것이 어떨지?**
                    - 어떤 시점에 Validation 판단할것인가? 2가지 정도로 나열해 볼 수 있지 않을까?
                        1. **@Valid** → JSON 을 @RequestBody 객체로 Deserialize 할때 (Jackson 에서)
                            - 참고 코드 : **[DTO 내 특정 필드에 대해 커스텀 어노테이션을 통해 필터링](https://stackoverflow.com/a/41718607)**할거면
                                
                                아래와 같이 @Valid 를 꼭 넣어주어야한다.
                                
                                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/3f1d628b-fddc-48e8-b5ad-0fa032d29f92/Untitled.png)
                                
                        2. **Get 메서드 호출 시 Validation 처리하여 반환**
                            - 예시 코드 : @Override 를 통해 @Getter 로 생성되는 자동 Getter 메서드를 재정의
                                
                                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d3ceb5d6-d502-4b19-b395-253bbbae9d54/Untitled.png)
                                
                - **결과적으로는 아래와 같이 Null 및 Validation 처리 로직이 모두 사라짐**
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d620d2cb-3337-4eeb-810f-82c9088299bf/Untitled.png)
                    
        
        ### Exception 공통 처리
        
        위 @RequestBody DTO 객체에서 @Valid 혹은 @Controller 에서의 @Secured 처리 **모두 로직 내 처리 불가**
        (@Secured : Spring Security, 로그인한 유저 세션에 할당된 권한 기반의 @Controller 호출 가능여부 처리)
        
        - @Valid 나 @Secured 처럼 추상화된 처리는 **try-catch 구문을 통해 개발자가 처리할 수 없다.**
        - @Service 내 발생하는 Exception 모두 **try-catch 하지 않으면** @Controller 를 넘어 클라이언트에 전가
            - 이 두 Exception 케이스의 공통점은 **@Controller 를 넘어 클라이언트에게 전가된다는 것**
                - **이전 :** 이를 처리하기 위해서 일반적으론 @Controller 내 try-catch 구문으로 방어하게된다.
                    - 만약 @Controller 로 정의된 API 메서드가 수백개면? **똑같은 try-catch 구문을 수백개 작성**
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/2ed0d7bb-d855-4f65-8c9c-baf6e7fe1c20/Untitled.png)
                    
                - **이후** : @ControllerAdvice 와 @ExceptionHandler 를 통해 try-catch 를 대신/중앙화
                    - @ExceptionHandler 는 어떤 Exception 을 받아서 처리할지 개별 정의가 가능
                        
                        : **try-catch 구문에서 catch 에 여러 Exception 들을 if-else 처럼 chaining 하는것처럼**
                        
                        - 2가지 방법
                            - @ExceptionHandler({ AException.class, BException.class }) 내 추가
                            - AException a, BException b 파라미터별로 @ExceptionHandler 정의
                        
                        : 단, 이럴 경우 AException 과 BException 이 동일 부모 클래스를 상속받지 않는 이상
                        
                        **공통된 필드를 한번에 처리할 수 있는 방법이 없다.** = 중복된 코드가 발생할 수 있다.
                        
                    - **추가 1)** `HttpServletRequest` 로 **어떤 유저가, 어떤 URL** 에서 에러 발생한건지 **추적 가능**
                    - **추가 2)** @ResponseBody + @ControllerAdvice = **@RestControlerAdvice**
                    - **추가 3)** @ControllerAdvice 내 파라미터를 통해 특정 패키지 내 @Controller 에만 적용가능
                    
                    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/1c041b01-5bbe-44f9-940e-b08bf1f5aa34/Untitled.png)
                    
        
        ### 프론트엔드에게 혹은 API 반환 시 일관된 객체(JSON) 형태로 반환하기
        
        백엔드 API 에서 발행되는 모든 @ResponseBody 는 일관된 객체(JSON) 형태로 가져야 처리에서 추상화 가능
        
        - **이전** : @Controller 내 API 메서드 각자 반환값이 달라서, 프론트엔드에서 각자 달리 처리 필요
            - **백엔드 개발 시 단점**
                - 다양한 Exception 들이 발생하는데, 이들 일일히 정의/반환 불가 (HTTP Status 500 만 가능)
                - 다양한 Exception 들에 대한 상세한 에러 메세지 반환 불가
                    - 단, Exception 에 대한 메세지 관리 책임을 백엔드 / 프론트엔드 둘 중 하나로 결정 필요
                        - 일반적으로 Exception 은 백엔드에서 발생하기에, 발생지-처리 원칙으로 백엔드가 처리
            - **프론트엔드 개발 시 단점**
                - 값이 반환되지 않은것인지, 에러가 발생한건지 여부 알기힘들다 (제대로 HTTP Status 미반환 시)
                - 백엔드에서 발생한 에러에 대한 메세지를 다원화하기 어렵다.
                    - 제대로 HTTP Status 반환한다해도, 유저에게 HTTP Status 기반의 에러 메세지만 발행 가능
            
            ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/edcb1add-51bc-49b0-9812-7783f2875b69/Untitled.png)
            
        - **이후** : @Controller 및 @ExceptionHandler 모두 동일한 **RequestResult<T>** 라는 객체를 반환
            - **백엔드** : Exception 에 따른 메세지를 다원화 가능, 성공/실패에 대한 간단한 정적 팩토리 메서드 활용
            - **프론트엔드** : (무지성으로) 백엔드가 보내준 RequestResult 를 통한 에러메세지 및 반환값 간단 처리
                - 프론트엔드는 정말 아무것도 신경쓰지 않고, 화면에 오롯이 집중할 수 있다.
            
            ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/7ef42aec-775e-4bfd-8175-0080c1a2c5a5/Untitled.png)
            
            - 아래는 @ControllerAdvice 내 @ExceptionHandler 에서 실패시의 RequestResult 반환
            
            ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/a3bb0ac2-4197-4d63-ad2f-be6c3d696028/Untitled.png)
            
        
        ### DTO 나 Controller 만들때 @FieldDefaults 사용하면 깔끔
        
        매번 필드에 **private final** 붙이는게 귀찮음 → @FieldDefaults 로 정리하여, 빠트릴 실수 방지하기
        
        - @Autowired 를 대체하기 위해 @RequiredArgsConstructor + Final 쓰는데, 그때도 유용
            
            ```java
            @RequiredArgsConstructor
            @FieldDefaults(makeFinal = true, level = AccessLevel.PRIVATE)
            public class PostService {
            		PostRepository postRepository;
            ```
            
        - 생성자 설정을 위한 Lombok 에서도 **생성자 메서드에 대한 접근자 설정 가능**
            - @NoArgsConstructor(**access = AccessLevel.PROTECTED**)
            - @AllArgsConstructor(**access = AccessLevel.PROTECTED**)
        
        ## Spring Cache
        
        ### Hibernate(JPA) 교육 시 **2차 캐시에 대한 언급이 있었는데 이에 대한 보완 설명**
        
        저번에 JPA 하면서 1차 캐시가 Persistence Context 라고 얘기하면서, 2차 캐시 얘기를 했는데
        
        - **2차 캐시**는 자동으로 설정되는게 아니라, 우리가 사용하고자하는 **Cache Provider** 를 설정해줘야함
            
            : Hibernate 구현체 기반으로 설명해보면
            
            - **1차 캐시 : Session-level Cache** (Transaction-level Cache)
            - **2차 캐시 : SessionFactory-level Cache** (WAS 뜨고, 지는 시간안에 생존 = 어플리케이션 레벨)
            - 1차 및 2차 캐시의 차이점에 대해 **이해가 쉬운 그림 설명**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/663e8649-8785-4e76-9817-52721360e9f6/Untitled.png)
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/0e102981-b4aa-4c4e-8fa7-99621128d0b5/Untitled.png)
                
        - 2차 캐시 사용을 위해서는 아래 절차를 따라 설정 (Spring Cache 에 대한 설명 - **[참조 1](https://adjh54.tistory.com/165)**, **[참조 2](https://jiwondev.tistory.com/282)**, **[참조 3](https://mangkyu.tistory.com/179)**)
            1. ****@EnableCaching 설정****
                
                ```java
                @EnableCaching
                @Configuration
                public class CacheConfig {
                    ... 
                }
                ```
                
            2. ****CacheManager Bean 설정****
                - **EhCacheCacheManager** or **ConcurrentMapCacheManager** 등
                    - **memcached** 의 경우엔, ConcurrentMap**CacheManager** 사용
                    - CacheProvider 어떤걸 쓸까? : Spring 에서 주로 사용되는 **EH**CAC**HE**
                        
                        !https://miro.medium.com/v2/resize:fit:1400/1*yihW_Etl_PHiWnzcR84AWw.jpeg
                        
                        - Redis 나 Memcached 같은 캐시 엔진들도 있지만, 저 2개의 캐시 엔진과는 달리 ehcache 는 데몬을 가지지 않고 Spring 내부적으로 동작하여 캐싱 처리를 한다.
                        - 따라서 Redis 같이 별도의 서버를 사용하여 생길 수 있는 네트워크 지연 혹은 단절같은 이슈에서 자유롭고, 같은 로컬 환경 일지라도 별도로 구동하는 Memcached 와는 다르게 ehcache 는 **서버 어플리케이션과 라이프사이클을 같이 하므로** 사용하기 더욱 간편하다.
                        
                        [Spring 로컬 캐시 라이브러리 ehcache](https://medium.com/finda-tech/spring-로컬-캐시-라이브러리-ehcache-4b5cba8697e0)
                        
            3. **Hibernate 에게 2차 캐시를 사용하겠다는 설정**을 알려주어야함
                - `spring.jpa.properties.**hibernate.cache.use_second_level_cache = true**`
                - `spring.jpa.properties.**hibernate.cache.region.factory_class` :** CacheProvider
                    
                    : `= org.hibernate.cache.ehcache.**EhCacheRegionFactory` (EhCache 를 사용한다면)**
                    
                - **+ 추가** : `spring.jpa.properties.hibernate.generate_statistics = true`
                    
                    : 하이버네이트가 여러 통계정보를 출력하게 해주는데 캐시 적용 여부를 확인할 수 있다.
                    
            4. 2차 캐시에 적재하고자하는 **@Entity 를 지정하여 @Cachable 과 @Cache 어노테이션 모두를 추가**
                
                *“Entity 저장은 @Cache / 메서드 저장은 @Cachable” 이라고 설명했으나 잘못된 설명이었음 !*
                
                - **@Cachable** : 캐시의 대상이라는것을 인식시켜주기 위함
                - **@Cache** : CacheConcurrencyStrategy 2차 캐시하는 @Entity 의 용도
                    
                    = 2차 캐시된 @Entity 가 동시성 상황에서 어떻게 사용되어야하는가? 에 대한 정의를 내리는것
                    
                    - **NONE** : 2차 캐시된 @Entity 에 대해서 아무것도 신경쓰지 않겠다
                    - **READ_READ** : 2차 캐시된 @Entity 에 대해 **읽기만 해야한다.**
                    - **NONSTRICT_READ_WRITE** : 2차 캐시된 @Entity 에 대해 **읽기/쓰기 다 가능, LOCK 없이**
                    - **READ_WRITE** : 2차 캐시된 @Entity 에 대해 **읽기/쓰기 다 가능, SOFT LOCK 으로만 보호**
                    - **TRANSACTIONAL** : **견고한 LOCK 적용 = @Transactional**
        
        ### @EnableCaching 은 꼭 JPA 의 2차 캐시로만 쓰이는건 아니다. 메소드 캐싱에도 활용 !
        
        스프링은 AOP 방식으로 편리하게 메소드에 캐시 서비스를 적용하는 기능을 제공 (**[참조 1](https://adjh54.tistory.com/165)**, **[참조 2](https://jiwondev.tistory.com/282)**, **[참조 3](https://mangkyu.tistory.com/179)**)
        
        - **캐시 서비스는 트랜잭션과 마찬가지로 AOP를 이용해 메소드 실행 과정에 투명하게 적용**
            - 캐시 관련 로직을 핵심 비즈니스 로직으로부터 분리할 뿐만 아니라, 손쉽게 캐시 기능 적용 가능
        - **스프링은 위에 설명했듯, 캐시 구현 기술에 종속되지 않도록 추상화된 서비스를 제공**
            - 그렇기 때문에 환경이 바뀌거나 적용할 캐시 기술을 변경하여도 애플리케이션 코드에 영향을 주지 않는다
        - **@Cachable 용례** : 특정 메소드의 결과값을 캐싱할 수 있다.
            - 캐시를 저장 & 조회하기 위한 ****@Cacheable****
                - 메소드의 파라미터 중 원하는것을 Key 값으로, 특정 조건을 만족했을때 캐시하도록 지정 가능
                    - 3가지 인자를 주로 사용 (Key 의 경우 없으면 메소드의 파라미터로 설정함)
                        - `**value`, `key`, `condition`**
                        - `cacheName`, `keyGenerator`, `cacheManager`, `cacheResolver`, `unless`, `sync`
                    
                    ```java
                    @Slf4j
                    @Service
                    public class NumberService {
                    
                        @Cacheable(
                    				value = "squareCache",
                    				key = "#number",
                    				condition = "#number > 10"
                    		)
                        public BigDecimal square(Long number) {
                            BigDecimal square = BigDecimal.valueOf(number).multiply(BigDecimal.valueOf(number));
                            log.info("square of {} is {}", number, square);
                            return square;
                        }
                    
                    }
                    ```
                    
            - 캐시 저장 만을 위한 ****@CachePut****
            - 캐시 제거 만을 위한 ****@CacheEvict****
        
        ## Spring Data JPA, Hibernate(JPA)
        
        ### Hibernate 전환된 SQL 쿼리 로그
        
        1. `spring.jpa.properties.hibernate.**show_sql**` 은 **System.Out** 에 Log 가 출력
            - `spring.jpa.properties.hibernate.**format_sql**` : **로그 포맷팅**
            - `spring.jpa.properties.hibernate.**highlight_sql**` : **하이라이팅**
            - `spring.jpa.properties.hibernate.**use_sql_comments**` : **주석 포함하여 보기**
        2. `logging.level.org.hibernate.**SQL** = DEBUG` 는 **Logger** 에 Log 를 출력 **(꼭 이걸 사용하도록!)**
            - **Logger (SLF4J)** → 어떤 Logger 구현체를 쓸지는 따로 설정이 필요 : **Log4J** or **Logback**
            - `logging.level.org.hibernate.**type.descriptor.sql** = DEBUG` : **바인딩 파라미터 보기**
            - `logging.level.org.hibernate.**type.descriptor.sql.BasicBinder** = DEBUG`
                - 참고로 여기 들어가는 `DEBUG` 에는 `TRACE` 도 들어갈 수 있다. Logger 에서 표기되는 레벨을 뜻함
        
        추가로, 위에 것들은 Hibernate 가 SQL 로 어떻게 전환되는지, JPA 를 보는것이고
        
        - **JdbcTemplate** 는 아래의 설정을 통해 로깅할 수 있다.
        
        ```java
        logging.level.org.springframework.jdbc.core.JdbcTemplate = DEBUG
        logging.level.org.springframework.jdbc.core.StatementCreatorUtils = TRACE
        ```
        
        - **코드 리펙토링** : Exception 및 @Controller 의 효율적 처리
        - **Hibernate 로깅** : SQL + Parameter Binding



  ### 1. **Spring Security**

  Spring 을 활용하여 개발한 웹 어플리케이션들은 일부 혹은 모든 사용자에게 서비스를 제공하기에 **보안 필수**

    - **Spring Security** : Spring 기본적으로 로그인, 세션에 관련된 모듈 및 설정 손쉽게 사용가능도록 제공
    - **Filter Chain** : 웬만한 모듈들은 Spring Security 가 제공하기에 거의 다 활용 가능 or 커스텀도 가능
        - **요청 URL 에 따라 다른 처리 가능**
        - **모든 요청에 따로 개발한 인증 모듈을 적용 가능**

  ### 2. Filter (Servlet) & Interceptor (Spring) 차이 - 이론 및 코드

  Spring Security 를 하는데, 왜 갑자기 필터와 인터셉터를 다루나요?

  이유는 Spring Security 에서 모든 보안처리는 **Filter 의 집합(SecurityFilterChain)**을 통해 동작됩니다.

    - **공통** : Spring Controller 에 요청이 전달되기전에 Middleware 처럼, URI 별 중앙처리가 필요한 경우
    - **차이** : Spring MVC Architecture 의 Front Controller 패턴을 기점으로 나뉜다.
        - **관리주체 차이**
            - **Filter** : **Tomcat** (**Servlet** Container) 서 관리
            - **Interceptor** : **Spring** (**Spring** Container) 서 관리
        - **호출시기 차이**
            - **Filter** : Front Controller **앞단** : **DispatcherServlet 앞쪽**에 위치
                - `**doFilter**()`
                    - 요청이 DispatcherServlet.service() 에 **진입하기 직전**(init() 후)에 **호출**
                    - 결과를 DispatcherServlet.service() 가 **반환하는 직후**(destroy() 전)에 **호출**
            - **Interceptor** : Front Controller **뒷단** : **Controller (Handler) 앞쪽**에 위치
                - `**preHandle**()` : 요청이 Controller 에 진입하기 **직전**에 **호출**
                - `**postHandle**()` : 결과를 Controller 가 반환하는 **직후**에 **호출**
                - `**afterCompletion**()` : Controller 성공/실패 결과에 따라 **View 를 생성한 직후**에 **호출**
                    - 에러가 발생해도, 무조건 수행되기 때문에 성공/실패에 따른 로그 저장 로직 등에 활용
        - **커버리지 차이**
            - **Filter** : Tomcat 은 WAS 이니 **정적리소스 반환** 등의 WS 처리도 수행, Filter 는 여기까지 적용
            - **Interceptor** : Spring Controller (Handler) 요청, 반환에 대해서만 Interceptor 적용

  ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/923d44c9-d91b-4fd0-bcd4-ea268a79b814/Untitled.png)

    - 더 상세한 그림 :  (이해하는데에 참조)

      ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/0cf68f07-23fb-4ca0-a62f-89763e718280/Untitled.png)


    ### 2.1. Filter ← Tomcat (Servlet Container)
    
    - Servlet (J2EE 7 표준) 스펙에 정의
    - Tomcat (WAS) 의 **Deployment Descriptor(web.xml)** 내 설정
        - **역사** : Filter 는 Servlet Container 에서 관리하기에, Spring Container 에서 인지할 수 없**었**다.
            1. **꽤나먼 과거 : Servlet Filter 내 Spring Bean 사용 불가**
                
                *web.xml 내 정의하는 Filter 들은 기본적으로 Servlet Container(WAS, Tomcat)에 의해서 control 됩니다. 이것들은 Spring 과는 아무런 관련이 없는 것들이죠. 따라서, Spring Framework 에서 사용하는 DI 즉, @Autowired 같은 것들을 사용할 수 없**었**습니다.*
                
                - **Servlet** : ServletContext 사용 ⇒ ServletFilter (Spring Bean 참조 **불가**)
                - **Spring** : ApplicationContext 사용 ⇒ Spring Bean 사용 **가능**
            2. **가까운 과거 : Spring Filter 사용 가능 및 내부에서 Spring Bean 사용 가능**
                
                *하지만 Filter 에서도 DI 를 통해 Spring Bean 사용이 필요한 상황이 발생(예를 들면, Filter 에서 특정 @Repository DAO 를 통해 해당 유저가 유효한지 아닌지를 DB 조회로 판단)하면서, Spring 개발자는 Filter 에서도 Spring Bean 이 주입될 수 있도록 프록시로 대안을 마련*
                
                - **Servlet** : ServletContext 사용 ⇒ ServletFilter (Spring Bean 참조 **불가**)
                - **Spring** : ApplicationContext 사용 ⇒ Spring Bean 사용 **가능**
                - **DelegatingFilterProxy** 의 등장 = Spring 에서 제공하는 ServletFilter (**[참조](https://findmypiece.tistory.com/62)**)
                    - ServletContext 내 DelegatingFilterProxy (Spring 제공 ServletFilter) 지정
                    - DelegatingFilterProxy 의 생성자 인자에 **Spring Bean** 명시
                        
                        = Filter 에 대한 상세 처리를 DelegatingFilterProxy 가 **해당 Spring Bean** 에 위임
                        
                        - **해당 Spring Bean = GenericFilterBean 상속한 Bean**
                        - **DelegatingFilterProxy**
                            
                            = **GenericFilterBean**
                            
                            = **Spring ApplicationContext 에서 관리되는 ServletFilter**
                            
                            - **기존 : Servlet ServletContext 에서 관리되는 ServletFilter**
            3. **현재 : Spring Boot 사용 시 프록시 설정 없이 알아서 Filter 등록 및 Spring Bean 사용 가능**
                
                : *Spring Boot 는 내장 톰캣을 가지고있어 Tomcat 에 대한 직접 설정이 가능하기때문에, 개발자가 따로 DelegatingFilterProxy 설정을 안해도 Spring Boot 가 자동으로 Spring Bean 으로 정의한 Filter 를 ServletContext(Serlvet Container) 에 등록해준다.*
                
                - Spring Bean Filter 등록 시 Servlet 관할의 **(Application)FilterChain** 에 바로 연결
        - **정리** : 결과적으로, Filter 는 Spring Bean 로 등록 가능하며, 내부에서 Spring Bean 활용도 가능
            - **DelegatingFilterProxy 등장 이전 :** 스프링 빈으로 등록 및 다른 빈 주입이 불가능
            - **DelegatingFilterProxy 등장 이후 :** 스프링 빈으로 등록 및 다른 빈 주입이 가능
            - **Spring Boot 등장 이후** : 직접 내장 WAS 설정 → DelegatingFilterProxy 설정없어도 자동
    - **필터 인터페이스 : 구현하여 사용할 수 있는 메서드가 무엇이 있는지 살펴보자**
        - ***참고** : 수업시간에 같이 살펴본 코드에서는 `GenericFilterBean` 추상 클래스가 있었는데, 명칭이 Bean 이라서 Spring Bean 이기에 Servlet Container 의 관리 주체가 아닌줄 알았으나, Servlet Container 로 관리됨과 동시에 **Spring Bean 주입을 받을 수 있도록 확장된 추상 클래스**로 확인된다. = Filter 의 생명 주기를 스프링 라이프 사이클과 함께 관리할 수 있게 된다.*
        
        ```java
        public interface Filter {
        
            public default void **init**(FilterConfig filterConfig) throws ServletException {}
            public void **doFilter**(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException;
            public default void **destroy**() {}
        }
        ```
        
        > 하나의 함수가 요청 진입시 & 결과 반환시, 모두 커버 가능하여 전역처리 로직에 적합
        > 
        - `**init**` : **필터 초기화 메소드**, 서블릿 컨테이너가 **해당 필터 생성 시**(`Servlet.**init**()`) 호출
        - `**destroy**` : **필터 종료 메소드**, 서블릿 컨테이너가 **해당 필터 종료 시**(`Servlet.**destroy**()`) 호출
        - `**doFilter**` : **클라이언트 요청이 올 때마다 호출**, Request 시 / Response 시 나누어 처리 가능
            - 앞선 `**init**` 과 **`destory`** 는 WAS 로드되었을때 한번만 호출, **`doFilter`** 는 매 요청마다
        - **코드 예시) 커스텀 Filter : `doFilter**()` **정의**
            
            ```java
            /* 코드 출처 : https://devhj.tistory.com/59 */
            
            @Slf4j  
            public class CustomRequestFilter implements Filter {  
            
                @Override  
                public void init(FilterConfig filterConfig) throws ServletException {}  
            
                @Override  
                public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain)  
                        throws IOException, ServletException {  
                    HttpServletRequest req = (HttpServletRequest) request;  
            
                    // GET 방식 요청 중 '/filterData' 경로에 대해서만 파라미터 변경  
                    if (req.getMethod().equals("GET") && req.getRequestURI().equals("/filterData")) {  
                        // 요청을 위한 커스텀 래퍼(wrapper) 생성  
                        CustomRequestWrapper requestWrapper = new CustomRequestWrapper(req){  
                            @Override  
                            public String getServerName() {  
                                return "test.com";  
                            }  
                        };  
            
                        // 원하는 대로 파라미터 수정  
                        requestWrapper.setParameter("name", req.getParameter("name"));  
                        requestWrapper.setParameter("age", req.getParameter("age"));  
                        requestWrapper.setParameter("user", "1");  
            
                        // 수정된 요청으로 계속 진행  
                        log.info("CustomRequestFilter Start");  
                        filterChain.doFilter(requestWrapper, response);  
                        log.info("CustomRequestFilter End");  
                    } else {  
                        // 다른 요청에 대해서는 기존 요청 그대로 전달  
                        filterChain.doFilter(request, response);  
                    }  
                }  
            
                @Override  
                public void destroy() {}  
            }
            ```
            
    
    ### 2.2. Interceptor ← Spring (Spring Container)
    
    - Spring Framework 스펙에 정의
    - **인터셉터 인터페이스 : 구현하여 사용할 수 있는 메서드가 무엇이 있는지 살펴보자**
        - ***참고** : HandlerInterceptorAdapter 은 Spring 5.3+ 버전에서 DEPRECATED 되었는데, 이유는 불필요한 함수 정의를 방지하기 위해 추상 클래스를 도입하였으나, Java 인터페이스 내 default method 를 만들 수 있게 됨으로써, 굳이 추상 클래스가 필요없어짐*
        
        ```java
        public interface HandlerInterceptor {
        
        	default boolean **preHandle**(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception { return true; }
        	default void **postHandle**(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {}
        	default void **afterCompletion**(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {}
        }
        ```
        
        > 컨트롤러 진입 혹은 결과 반환 시점에 디테일하게 처리해야하는 로직에 적합
        > 
        - **`preHandle` :** 요청이 Controller 에 진입하기 **직전**에 **호출** (false 반환 시 Controller 미진입)
        - **`postHandle` :** 결과를 Controller 가 반환하는 **직후**에 **호출**
        - **`afterCompletion` :** Controller 결과에 따른 **View 생성 혹은 에러 발생과 상관없이** **호출**
            - 에러가 발생했다 하더라도, 무조건 수행되기에 성공/실패 상관없이 로그 저장 로직 등에 활용
        - **코드 예시) 커스텀 Interceptor** ← ~~HandlerInterceptorAdapter~~ ← **HandlerInterceptor**
            
            ```java
            /* 코드 출처 : https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-12-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Interceptor-%EC%84%A4%EC%A0%95/ */
            
            @Slf4j
            @Component
            // public class LoggerInterceptor extends HandlerInterceptorAdapter { // DEPRECATED
            public class LoggerInterceptor implements HandlerInterceptor {
            
            	@Override
            	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            		log.info(" Class       \t:  " + handler.getClass());
            		log.info(" Request URI \t:  " + request.getRequestURI());
            		log.info(" Servlet URI \t:  " + request.getServletPath());
            		
            		Enumeration<String> paramNames = request.getParameterNames();
            		while (paramNames.hasMoreElements()) {
            			String key = (String) paramNames.nextElement();  
            			String value = request.getParameter(key);
            			log.info("# RequestParameter: " + key + "=" + value + "");
            		}
            
            		return super.preHandle(request, response, handler);
            	}
            
            	@Override
            	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {}
            	
            	@Override
            	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {}
            }
            ```
            
    
    ### 3. Filter (Servlet) & Interceptor (Spring) 차이 - 그림
    
    - **(그림 1)** Filter 는 Servlet Container 가 관리, Interceptor 는 Spring Container 가 관리하던 시절
        1. (Application)**FilterChain** : WAS(Tomcat, Servlet Container) 에서 관리하는 필터 체인
    
    !https://user-images.githubusercontent.com/1817625/155494265-f606005f-4383-4688-926e-99f0637d636f.png
    
    - **(그림 2)** Filter 도 Spring Container 가 관리가능하던 시절 (DelegatingFilterProxy)
        
        !https://user-images.githubusercontent.com/1817625/155494292-9789207b-d720-4ba1-80c8-e407bdadd046.png
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d456a2c2-ddf0-40c8-98a7-4c03e0b9665e/Untitled.png)
        
        1. (Application)**FilterChain** : WAS(Tomcat, Servlet Container) 에서 관리하는 필터 체인
            - Servlet 관할의 (Application)FilterChain 에서 Spring Bean Filter 쓰고싶다면
                - DelegatingFilterProxy 주입 = Spring Bean Filter (GenericFilterBean)
                    
                    : 근데 Spring Security 는 단일 Filter 가 아니라 다수 Filter 로 엮고싶음
                    
                    - **단일 GenericFilterBean** = **단일 FilterChainProxy**
                        - FilterChainProxy 가 DelegatingFilterProxy 한테 위임받음
                    - **단일 FilterChainProxy** 내부에 ⇒ **다수 SecurityFilterChain 리스트**
                        
                        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/03823bd3-f11c-410f-8609-ca78d780f007/Untitled.png)
                        
                        - Spring Security 에서 제공하는 **SecurityFilterChain 도식**
                        
                        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d71135b2-6a58-4c76-8aec-f206be6f3790/Untitled.png)
                        
                    - **단일 SecurityFilterChain** 내부에 ⇒ **다수 GenericFilterBean(Filter) 리스트**
                        - `matches()` 와 `getFilters()` 라는 2개의 메서드를 가진 인터페이스
                            - `**matches**` : 실제 요청에 따른 SecurityFilterChain 적용 여부
                            - `**getFilters**` : 매칭되었을 때 수행될 필터들의 목록
                        
                        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/faf7bd0f-46ca-444b-823a-3512474da3c6/Untitled.png)
                        
                    - **다수 GenericFilterBean(Filter) 리스트** 중 유명한것
                        
                        = **UsernamePasswordAuthenticationFilter**
                        
                        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/d1e4a997-9f5a-4e63-a3d5-2ebba8742007/Untitled.png)
                        
                        - 그 외 정말 다양한것들이 존재한다.
                            - UsernamePasswordAuthentication**Filter**
                            - SecurityContextPersistence**Filter**
                            - DefaultLoginPageGenerating**Filter**
                            - HeaderWriter**Filter**
                            - Authentication Filter
                            - Authorization Filter
                            - CORS Filter
                            - CSRF Filter 등등 …
                
                *: Spring Boot 시절에 들어와서는 DelegatingFilterProxy 따로 설정 안해도 알아서해줌*
                
        2. **SecurityFilterChain** : Spring Container 에서 관리하는 필터 체인
            - 다시 정리하자면
                - (Application)FilterChain = Servlet 꺼
                - SecurityFilterChain = Spring 꺼 (정확히는 Spring Security 꺼)
    
    ### 4. Spring Security - Custom Filter 추가 및 설정
    
    앞서 1, 2, 3 챕터에 이어서 Spring Security 는 Spring Container 에서 관리하는 Filter (Spring Bean) 으로 구성된 **SecurityFilterChain** 을 통해 원하는 보안 사항을 적용함을 알 수 있었고, 최종적으로는 Servlet Container 가 관리하는 **(Application)FilterChain** 에 적용되어 Servlet 레벨에서 인증/인가가 처리됨을 알 수 있었다.
    
    - HTTP Request 를 가로채는 웹 어플리케이션 아키텍처 첫번째 계층은 **(Application)FilterChain**
    - **커스텀하게 직접 만든 Filter** (Spring Bean) 을 적용하기 위해서는
        
        SecurityFilterChain 내에 있는 **특정 Filter** (Spring Bean) 의 **앞/뒤/동일 위치에 추가**해야한다.
        
        > SecurityFilterChain (Spring 의 Filter Chain)을 변경하여, 애플리케이션의 요구사항에 정확히 일치하도록 인증 및 권한을 사용자 지정
        > 
        - **addFilterBefore** : 지정된 필터 **앞**에 커스텀 필터 추가
        - **addFilterAfter** : 지정된 필터 **뒤**에 커스텀 필터 추가
        - **addFilterAt** : addFilterBefore 과 유사 (오버라이드하는것은 아님)
        - + 만약 같은 위치에 배치된다면, **@Order 정의를 통해** 우선순위를 할당할 수 있다.
        
        예) CustomFilter(Bean) 을 UsernamePasswordAuthenticationFilter(Bean) 앞에 위치
        
        ```java
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.addFilterBefore(new CustomFilter("/login-process"), UsernamePasswordAuthenticationFilter.class);
        }
        ```
        
        - **Advanced 예)** CustomFilter(Bean) 에 추가 설정 넣고 싶다면, 아래와 같이 좀 더 상세하게 설정
            
            ```java
            @Bean
            public CustomFilter customFilter(){
            		// 인증 처리를 담당하는 커스텀 필터
            		CustomFilter filter = new CustomFilter("/login-process");
                filter.setAuthenticationManager(new CustomAuthenticationManager());
            		// 인증 로직이 포함된 커스텀 인증 매니저를 추가한다. (AuthenticationManager를 상속받아서 구현)
                filter.setAuthenticationFailureHandler(new SecurityAuthenticationFailureHandler());
            		// 인증 실패 핸들러를 등록할 수 있다.
                filter.setAuthenticationSuccessHandler(new SecurityAuthenticationSuccessHandler());
            		// 인증 성공 핸들러를 등록할 수 있다.
            		return filter;
            }
            
            @Override
            protected void configure(HttpSecurity http) throws Exception {
                http.addFilterBefore(customFilter(), UsernamePasswordAuthenticationFilter.class);
            }
            ```
            
    
    ### 5. Spring Security Architecture - 인증 Authentication 영역 처리는 어떻게 되나
    
    - **수업시간에 진행했던 도식** : 다 학습한 후 본 도식을 통해 깔끔하게 머릿속을 정리하자 (복습하자)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/1a7bbf4b-486f-4ba5-a2b6-1fa038a3af88/Untitled.png)
    
    - (추가 도식) 그리고나서 아래 도식을 보면, 도식 내 각 요소에 대해 이해가 될것이다
        - 하지만, 위에 “수업시간에 진행했던 도식” 만으로도 이해가 다 되었다면,
        - 굳이 아래 도식으로 머리를 복잡하게 하지 않아도된다. (근데 찾으면 이 그림만 나올것)
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/965c992a-1f89-4044-8c99-05c05e519a7f/Untitled.png)
        
    
    계속 반복해서 귀에서 피가나겠지만, Spring Security 의 핵심은 SecurityFilterChain 이고, 그 내부에 Spring Security 가 제공하는 수많은 Filter 들이 존재한다. 이 Filter 중 **Authentication 인증** 부분만 떼어서 어떻게 동작하는지 보자. ⇒ **AuthenticationFilter 부터 시작하는 ~~신나는~~ 모험**
    
    - **Spring Security Architecture → Authentication 인증 관련 동작 원리**
        
        = **AuthenticationFilter** + **AuthenticationManager** + **AuthenticationProvider** 로 구성
        
        - 설명에 앞서 **AuthenticationToken** 은 무엇인가? 앞으로 계속 언급할 객체이기에 짚고가자
            - 인증에 사용될 클라이언트(요청자)가 보낸 정보 ← **Authentification 인터페이스 구현체**
                - 앞으로 살펴볼 예시 UsernamePassword**AuthentificationToken**
                    
                    에서는 Username / Password
                    
                    - **Username** → **Principal 역할**
                    - **Password** → **Credential 역할**
                - **Authentification 인터페이스 구조**
                    - **Principal** : 접근 주체의 아이디 혹은 User 객체를 저장합니다.
                    - **Credentials** : 접근 주체의 비밀번호를 저장합니다.
                    - **Authorities** : 인증된 접근 주체자의 권한 목록을 저장합니다.
                    - **Details** : 인증에 대한 부가 정보를 저장합니다.
                    - **Authenticated** : boolean 타입의 인증 여부를 저장합니다.
        - **Authentication 은 다음과 같은 순서대로 인증이 진행된다 : Filter → Manager → Provider**
            
            ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/0a690055-b221-47ef-a1c7-8a61da3acf28/Untitled.png)
            
        1. **AuthenticationFilter** : **AuthenticationToken** 생성 및 저장 주체
            - (1) **AuthenticationToken 생성** : 인증(검증)을 위한 객체 생성, 아직 미인증
            - (2) **AuthenticationToken 저장 : 1. SecurityContextHolder + 2. SecurityContext**
                
                ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/6b668f6a-87d0-4b5c-bf13-0ed7ee9e4a97/Untitled.png)
                
                1. **SecurityContextHolder :** 지정된 보관 전략(모드)에 따라 **SecurityContext** 보관
                    - **기본적으로 ThreadLocal 을 사용** (모드 MODE_THREADLOCAL 사용시)
                        
                        = **한 스레드 내에서 쉐어하는 저장소**
                        
                        = **한 스레드에서 Authentication 공유 가능**
                        
                        - **(참고)** SecurityContextHolder 에 설정 가능한 모드는 아래와 같다
                            1. **MODE_THREADLOCAL**: (Default) **Local Thread 에서만 공유**
                            2. **MODE_INHERITABLETHREADLOCAL**
                                - 메인 스레드에서 생성한 **하위 Thread 에까지 공유 가능**
                                - 예) PararellStream 내에서 사용할 경우
                            3. **MODE_GLOCAL**: **모든 Thread, 어플리케이션 전체에서 공유 가능**
                    - 스레드가 달라지면 제대로 된 인증 정보를 가져올 수 없다 (**[해당 오류겪은 블로그글](https://aaronryu.github.io/2021/03/14/thread-and-security-context-holder-mode/)**)
                        
                        *: **기본 ThreadLocal 을 사용시** (모드 MODE_THREADLOCAL 사용시)*
                        
                    - SecurityContext 는 **ThreadLocal** 뿐만아니라 **HttpSession** 에도 저장되어 있다
                        
                        *= 인증 완료 시 스레드 뿐만 아니라 세션에도 저장된다는 뜻 (세션으로 재사용 가능)*
                        
                2. **SecurityContext** : 인증된 **Authentication** 객체를 보관하는 역할
                    - 동일 스레드라면 언제든 필요할때마다 Authentication 객체를 꺼내올 수 있음
                    - SecurityContextHolder → SecurityContext → Authentication 순서로 접근
                    
                    ```java
                    SecurityContextHolder.getContext().getAuthentication().getName();
                    ```
                    
            - 많은 **AuthenticationFilter** 구현체들이 있는데, 그 중 몇개를 짚어보자면
                - **UsernamePassword**AuthenticationFilter
                - **Basic**AuthenticationFilter
                - **Anonymous**AuthenticationFilter 등등..
        2. **AuthenticationManager** : **AuthenticationToken** 인증(검증) 방법 할당
            
            ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/0b6c8c0f-17ca-4dec-9780-f635591b9131/Untitled.png)
            
            - 구현체 : **ProviderManager**
                - 수많은 AuthenticationProvider 중 적합한 AuthenticationProvider 찾아 Token 검증
                    - **방법** : AuthenticationProvider.**supports**(**Authentication** toTest) 호출
                    - 만약 적합한 AuthenticationProvider 못찾으면 **ProviderNotFoundException**
        3. **AuthenticationProvider** : **AuthenticationToken** 인증(검증) 처리 주체
            - 앞서 **AuthenticationFilter** 에서 만든 미인증 (인증을 위한) **AuthenticationToken** 인증
                - **방법** : OAuth, SAML, 혹은 Username & Password 검증을 위한 DAO 등을 사용
                    
                    *: Username & Password, SAML, OAuth 등 **다양한 Provider 정의 및 사용 가능***
                    
                    - 인풋 : 인증 전의 AuthenticationToken - 미인증
                    - **반환 : 인증 후의 AuthenticationToken - 인증여부 최종 결정**
                        - 인증 **완료** 시 AuthenticationToken 객체 내 `authenticated = **true**` 설정
                        - 인증 **실패** 시 AuthenticationToken 객체 내 `authenticated = **false**` 설정
    
    ### 6. Spring Security Configurations
    
    <aside>
    ⚠️ **잠깐 배워가요 코너**
    
    Spring 에서는 (1) Interface + (2) Interface**Adapter** 이런식의 네이밍을 많이 활용한다
    
    - (1) Interface 는 인터페이스(interface)
    - (2) Interface**Adapter** 는 추상 클래스(abstract class)
    - 그래서 아래서 배울 내용이지만 예를 들면
        - WebSecurityConfigurer = 인터페이스(interface)
        - WebSecurityConfigurer**Adapter** = 추상 클래스(abstract class)
    
    추상 클래스들은 웬만하면 DEPRECATED 되는것이, 인터페이스에 Default Method 가 등장해서
    
    </aside>
    
    Spring Security 를 사용하기 위해서 어떤 설정을 해야할까? Spring Security 를 사용한다는것은 웹 보안을 위한 SecurityFilterChain 가 활성화되고, Servlet Filter Chain 인 (Application)FilterChain 의 일부분에 SecurityFilterChain 가 체결된다는 뜻
    
    - 아래 그림을 보면 클라이언트에서 요청이 들어오면 (Application)FilterChain 을 지나가는 도중에 Spring Security 가 제공하는 (활성화된) SecurityFilterChain 을 거쳐가는것을 볼 수 있다. (**[참조](https://bitgadak.tistory.com/10)**)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cd689693-149b-44c4-a203-a4bc60086f78/27c08457-e8bf-4e76-97d7-4d75fa20ed21/Untitled.png)
    
    SecurityFilterChain 를 활성화하고, (Application)FilterChain 에 체결하기 위한 방법을 알아보자
    
    1. **@EnableWebSecurity 설정을 추가**
        
        > 아래 두개 중 하나의 방법으로 **@EnableWebSecurity** 어노테이션을 추가하면
        > 
        > - 웹 보안이 활성화되고 **SpringSecurityFilterChain** 이 (Application)**FilterChain** 에 포함
        >     - 위에서 배웠던 **SecurityFilterChain** 가 활성화가 된다는 뜻
        - **(1) Spring Security 5.4 버전까지**
            - **@EnableWebSecurity 어노테이션을 아래 클래스에 붙인다**
                - WebSecurityConfigurer (인터페이스) 를 구현한 **Security Config 클래스**, 혹은
                - WebSecurityConfigurerAdapter (추상 클래스) 를 상속받은 **Security Config 클래스**
        - **(2) Spring Security 5.7+ 버전 이후부터** (WebSecurityConfigurerAdapter DEPRECATED)
            
            *: 기존의 구현, 상속 방식이 아닌 **Component-based Security Configuration** 를 사용한다.*
            
            - **@EnableWebSecurity 어노테이션을 @Configuration 와 함께 설정 클래스에 붙인다**
    2. **SecurityFilterChain** 을 **(2) 적용할지 말지**, 한다면 **(3) 세부적으로 어떻게쓸지**, **(1) 어떤 저장소쓸지**
        - (1) **UserDetailsManager** userDetailsService() (5.7+ 버전 이후)
            - [**인증 방식** 정의] void configure(**AuthenticationManagerBuilder auth**) **대체재** (이전)
                - **인증 정보를 어디에 저장할것인가?**
                    - 예시 1) **“인메모리”**를 사용자 저장소로 활용
                        
                        ```java
                        @Override
                        protected void configure(AuthenticationManagerBuilder auth) throws Exceptions {
                        	  auth.inMemoryAuthentication()
                        	     .withUser("user").password("password").roles("USER").and()
                        	     .withUser("admin").password("password").roles("USER", "ADMIN");
                        }
                        ```
                        
                    - 예시 2) **“데이터베이스”**를 사용자 저장소로 활용
                        
                        ```java
                        @Override
                        protected void configure(AuthenticationManagerBuilder auth) throws Exceptions {
                        	  auth.jdbcAuthentication()
                        		    .dataSource(dataSource)
                        		    .userByUsernameQuery("select username, password, true " + .... )
                        		    .authoritiesByUsernameQuery("select username, 'ROLE_USER' from ...")
                        		    .passwordEncoder(new StandardPasswordEncoder("53cr3t");
                        }
                        ```
                        
                    - 예시 3) **“LDAP”** 기반 (Lightweight Directory Access Protocol, 분산 디렉토리)
                        
                        ```java
                        @Override
                        protected void configure(AuthenticationManagerBuilder auth) throws Exceptions {
                        	  auth.ldapAuthentication()
                        		    .userSearchBase("ou=people")
                        				.userSearchFilter("(uid={0}")
                        	      .groupSearchBase("ou=groups")
                        	      .groupSearchFilter("member={0}")
                        	      .passwrodCompare()
                        	      .passwordEncoder(new Md5PasswordEncoder())
                        	      .passwordAttribute("password")
                        }
                        ```
                        
                    - 예시 4) **사용자 정의 DAO 저장소 활용**
                        
                        ```java
                        // userDetailsService(사용자 정의 사용자 서비스 설정)
                        @Override
                        protected void configure(AuthenticationManagerBuilder auth) throws Exceptions {
                        	  auth.userDetailsService(new UserService(userRepository));
                        }
                        ```
                        
        - (2) **WebSecurityCustomizer** { return (**WebSecurity web**) -> **web** } (5.7+ 버전 이후)
            - [**보안 예외 처리** 정의] void configure(**WebSecurity web**) **대체재** (이전)
                - `**ignoring**()` : `**antMatchers**` 내 URI 에 대해 **SecurityFilterChain 모두 무시**
                    
                    = Security Features (Secure headers, CSRF protecting 등) 미사용
                    
                - **일반적으로 로그인 페이지, public 페이지 등 인증, 인가 서비스가 필요없는 URI 에 사용**
                    - 예시 1) Spring Security 규칙 모조리 무시할 URI 설정 `web.**igoring**()`
                        
                        ```java
                        @Override
                        public void configure(WebSecurity web) throws Exception {
                        		web.ignoring()
                        				.antMatchers("/resources/**")
                        				.antMatchers("/css/**")
                        				.antMatchers("/vendor/**")
                        				.antMatchers("/js/**")
                        				.antMatchers("/favicon*/**")
                        				.antMatchers("/img/**");
                        }
                        ```
                        
        - (3) **SecurityFilterChain** filterChain(**HttpSecurity http**) (5.7+ 버전 이후)
            - [**보안 세부 처리** 정의] void configure(**HttpSecurity http**) **대체재** (이전)
                - antMatchers 내 URI 에 대한 **SecurityFilterChain 세부 설정**
                    
                    = Security Features (Secure headers, CSRF protection 등) 사용
                    
                - **취약점에 대한 보안 처리가 필요할 경우 사용**
                    - 예시 1) 권한 세부설정 `http.**authorizeRequests**()`
                        
                        ```java
                        // 모든 사용자가 모든 경로(/**)에 대해서 요청을 할 수 있지만, “/member/**” 인증된 사용자만 접근이 가능하고 "/admin/**"경로는 ROLE_ADMIN 권한이 있는 사용자만 접근할 수 있습니다.
                        http.authorizeRequests()
                            .antMatchers("/member/**").authenticated()
                            .antMatchers("/admin/**").hasRole("ADMIN")
                            .antMatchers("/**").permitAll();
                        ```
                        
                    - 예시 2) FORM 로그인 `http.**formLogin**()`
                        
                        ```java
                        // form login에 대한 설정
                        http.formLogin()
                        		.loginPage("/login")
                        		.defaultSuccessUrl("/")
                        		.permitAll();
                        ```
                        
                    - 예시 3) 로그아웃 `http.**logout**()`
                        - `invalidateHttpSession(**true**)` : **로그아웃 시 인증 정보를 지우고 세션을 제거**
                        - `invalidateHttpSession(**false**)` 를 사용하는 경우는? (더 공부 필요, **[출처](https://stackoverflow.com/questions/55729411/how-to-handle-unsuccessful-logout-in-spring)**)
                        
                        ```java
                        // 로그아웃에 대한 설정
                        http.logout()
                        		.logoutRequestMatcher(new AntPathRequestMatcher("/logout"))
                        		.logoutSuccessUrl("/login")
                        		.invalidateHttpSession(true);
                        ```
                        
    - **(참고) Usecases 에 따른 HttpSecurity 설정**
        1. **리소스(URL)에 대한 접근 권한 설정**
            - 특정 URL의 접근을 허용하거나 특정 권한을 가진 사용자의 접근을 허용
        2. 인증 전체의 흐름에 필요한 **로그인 페이지 설정**
        3. 인증 완료 후 페이지 / 인증 실패시 이동할 페이지 설정 = **인증 성공 / 실패에 대한 커스텀 Handling**
            - **AuthenticationSuccessHandler** 인터페이스 → **onAuthenticationSuccess** 구현
                
                ```java
                public class SecurityAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
                
                    @Override
                    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication) throws IOException, ServletException {}
                
                    @Override
                    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {}
                }
                ```
                
            - **AuthenticationFailureHandler** 인터페이스 → **onAuthenticationFailure** 구현
                
                ```java
                public class SecurityAuthenticationFailureHandler implements AuthenticationFailureHandler {
                
                    @Override
                    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {}
                }
                ```
                
            - 그리고 **HttpSecurity** 설정을 통해 위에 정의한 **인증 성공 / 실패 클래스 주입**
                
                ```java
                @Configuration
                @EnableWebSecurity
                public class SecurityConfig extends WebSecurityConfigurerAdapter {
                
                    @Override
                    protected void configure(HttpSecurity http) throws Exception {
                        http.formLogin()
                            .loginPage("/login")
                						// /login/process 호출 시 인증처리 필터 실행 = UsernamePasswordAuthenticationFilter
                            .loginProcessingUrl("/login/process")
                						// 1. 성공 : **AuthenticationSuccessHandler.onAuthenticationSuccess**
                            .defaultSuccessUrl("/index")
                            .successHandler(new SecurityAuthenticationSuccessHandler())
                						// 2. 실패 : **AuthenticationFailureHandler.onAuthenticationFailure**
                            .failureUrl("/log-in/fail")
                            .failureHandler(new SecurityAuthenticationFailureHandler());
                    }
                }
                ```
                
        4. 인증 로직에 대한 **커스텀 필터 설정**
            - AbstractAuthenticationProcessingFilter 상속받아 구현 (**[상세 설명](https://velog.io/@on5949/SpringSecurity-AbstractAuthenticationProcessingFilter-%EC%99%84%EC%A0%84-%EC%A0%95%EB%B3%B5)**)
        5. 기타 모든 스프링 시큐리티의 설정을 여기에서 가능