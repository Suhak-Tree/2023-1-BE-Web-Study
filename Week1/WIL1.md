백엔드 스터디 – 무엇이 어떻게 사용되는가?!	- 1주차

hpps://start.spring.io -> 스프링 부트로 스프링 만들기(스프링 부트 기반으로).
Gradle, Java, spring boot 2.3.1버전으로 실행.
groupld : hello (보통 기업 도메인명), artifactld : hello-spring (결과물)
Dependencies(중요) : Spring Web(web project), Thymeleaf(html을 만들어주는 템플릿 엔진. - generate – 다운로드 – study 폴더에 압축해제)
폴더를 IntelliJ로 open – build.gradle -> open.as.project.
idea -> IntelliJ가 사용하는 설정파일.

src -> main -> Java – resources
	test – test code들과 관련된 코드가 있음(요즘은 test 코드가 중요)

IntelliJ 버전은 Gradel을 통해서 실행하는 것이 기본이지만, 실행속도 빠르게 하기 위해 Java로 바로 실행하는 것으로 바꿀 것.

라이브러리 – Gradle은 의존 관계가 있는 라이브러리를 함께 다운로드 하기 때문에 spring web 라이브러리를 추가하면 tomcat(웹서버), webmvc 가 자동으로 땡겨 들어온다. thymeleaf(타임리프 템플릿 엔진(view))를 추가하면 spring-boot-starter(스프링 부트 + 스프링 코어+ 로깅 – logback, slf4j) 다 땡겨온다. 테스트 라이브러리는 따라서 자동으로 들어오며, 핵심이자 기본은 junit이고 그 외에 mockito, assertj, spring-test 가 있다. 

src -> main -> resources -> static -> 우클릭 -> 파일 -> index.html -> 서버 껐다 킴 -> localhost:8080 -> 홈화면이 뜸
spring.io -> project -> spring boot -> LEARN -> 2.3.1 -> reference doc
 model.addAttribute("data", "hello!!"); -> “data” 가 key의 역할을 하고, “hello!”가 값의 역할을 한다. 
return "hello"; - hello.html을 찾아서 “data”를 model key 값으로 치환하여 삽입.
동작 환경 : 웹 브라우저에서 요청을 하면 내장 톰캣 서버가 요청을 받고 스프링으로 던진다. 후에 hellocontroller가 hello를 리턴값으로 설정하고 resources/templates 폴더 밑의 hello를 찾는다. 찾으면 thymeleaf 템플릿 엔진 처리에서 랜더링을 하여 웹 브러우저로 리소스를 넘긴다.

.gradlew build (잘 안되면 .gradlew clean build로 실행) -> cd build/libs -> 18MB짜리 파일 만들어지는지 확인 -> java –jar hello-spring-0.0.1-SNAPSHTO.jar (이게 18MB짜리 파일. 서버에 배포할 때는 이 파일만 복사해서 서버에 넣고 java –jar로 실행하면 됨) -> 실행확인 

스프링 웹 개발 기초 – 크게 3가지 방식이 있음 : 정적 컨텐츠, MVC와 템플릿 엔진, API

정적 컨텐츠 : 예전의 welcome page처럼, 파일 그대로 web brouser에 바로 보여줌. spring boot에서 자동으로 제공하는 기능이며 데이터 구조 포맷으로 client한테 data를 전달한다. /static, /public, /resources, /NETA-INF/resources에서 찾아서 제공한다. 원하는 파일 넣으면 그대로 진행이 되지만 프로그래밍은 불가능하다.
동작 방식 – 웹브라우저에서 요청을 하면 내장톰캣서버에서 요청을 스프링으로 던진다. controller가 우선순위를 가져서 controller부터 확인을 하고 controller가 없으면 resources폴더에서 hello-static 이름이 들어간 파일을 찾고 그것을 웹 브라우저에 반환한다.

MVC(model, view, controller)와 템플릿엔진
controller와 model은 business logic과 관련이 있거나 내부적인 것을 처리하는데 집중한다.(서버 뒷단의 일 등) view는 화면을 그리는데 모든 역량을 집중한다. controller의 “name”은 외부로부터 받는 parameter이며, value = “name, required = false이다. 후의 model의 name은 spring으로 입력 시 spring으로 바뀌며 모델에 key값으로 담긴다. 모델에서 이 key 값 ‘spring’을 꺼내서 view의 ${name}에 전달한다. 파일을 서버없이 껍데기를 볼 수 있다. 기본적으로는 모델이 관련된 화면에 필요한 것을 담아서 넘겨준다. 
동작 방식 – 웹 브라우저에서 요청하면 내장 톰캣 서버에서 스프링으로 던지고 controller가 mapping이 되어있는 것을 확인한다. return 값이 hello-template인걸 확인하고 그 안에 들어있는 model의 key 값을 확인 후 viewResolver에 넘긴다. 여기서 hello-template이 들어간 파일을 찾아 템플렛 엔진에 넘기고 HTML로 변환 후 웹 브라우저에 반환한다.
viewResolver는 화멸 해결자 동작을 한다. view라는 템플릿을 찾아주고, 템플릿 엔진을 연결해준다.

API – 데이터를 바로 내림. @ResponseBody 가 필수로 필요(문자 반환). http에서 body 부의 데이터를 직접 넣어줌. public String helloString(@RequestParam("name") String name) { return "hello " + name; } name이 바로 내려가는 구조이다. @ResponseBody가 있기에 viewResolver를 사용하지 않고, http의 body에 문자 내용을 직접 반환한다.(thml body tag가 아님)
@responseBody를 사용하고 객체를 반환하면 객체가 JSON으로 변환되는 것이 기본이다.@ResponseBody 사용 원리 : 웹 브라우저에서 요청을 하면 내장 톰캣서버에서 확인을 하고 스프링으로 던진다. controller가 있는지 확인 후 요청을 넘기는데 @ResponseBody가 있기 때문에 응답에 그대로 데이터를 넘긴다. HttpMessageConverter가 동작하여 객체인 경우 JsonConverter에 문자인 경우 StringConverter에 넘긴다. converting 후에 내용을 웹 브라우저에 전송한다.
기본 문자처리: StringHttpMessageConverter, 기본 객체처리: MappingJackson2HttpMessageConverter, byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있다.

정리 : 정적 컨텐츠 – 그냥 파일을 그대로 내려준다.
MVC와 템플릿엔진 – 템플릿 엔진을 model, view, controller로 쪼개서 view를 템플릿 엔진으로 html을 좀 더 프로그래밍 한 것으로 랜더링 한다. 랜더링 된 html을 client에게 전달
API – 객체 반환 -> httpmessageconverter이용해서 JSON 스타일로 바꿔서 반환해줌. view 이런거 없이 그냥 그대로 바로 http response에 값을 넣어서 반환해줌