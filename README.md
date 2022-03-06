---

# Thymeleaf Basic

김영한 님의 "스프링 MVC 2편 - 백엔드 웹 개발 활용 기술" 강의 코드를 따라치면서 간략하게나마 학습정리를 조금씩 하기 위한 Repository

---

### 프로젝트 환경

- jdk : 11
- IDE : intelliJ Ultimate
- 빌드 : gradle, Spring Boot
```groovy
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```
- 의존 라이브러리
    - lombok
    - spring-boot-starter-web
    - spring-boot-starter-thymeleaf

---

### welcome 페이지
`resources/static/index.html` : 강의 실습 코드들 실행 결과 `view`를 한 페이지에 모아둠

---

## 타임리프 선언
```html
<html xmlns:th="http://www.thymeleaf.org">
```
- html 선언시 속성으로 xmlns... 을 작성해야함.

---

## 텍스트 출력 - th:text, th:utext
```html
<h1>text vs utext</h1>
<ul>
    <li>th:text = <span th:text="${data}"></span></li>
    <li>th:text = <span th:utext="${data}"></span></li>
</ul>

<h1><span th:inline="none">[[...]] vs [(...)]</span></h1>

<ul>
    <li><span th:inline="none">[[...]] = </span>[[${data}]]</li>
    <li><span th:inline="none">[(...)] = </span>[(${data})]</li>
</ul>
```
![unescaped.png](img/unescaped.png)

1. 태그의 속성으로 지정하여 사용
   - `th:text` : escape 함
   - `th:utext` : escape 안 함(unescaped text)

2. 콘텐츠 영역 안에서 직접 사용
   - `[[...]]` : escape 함
   - `[(...)]` : escape 안 함(unescaped text)

3. escape?
   - HTML 엔티티 : `<`,`>`와 같은 HTML 문법에 적용되는 문자를 문자 그대로 표현하기 위한 문자.
     - 예) `<` -> `&lt;`, `>` -> `&gt;`
   - Escape : 문자열에 포함된 HTML에서 사용되는 문자를 HTML 엔티티로 변경하여 문자 그대로 표현하게 하는 것.


#### (cf) th:inline="none"
- 현재 태그 안의 내용에 대하여 타임리프 문법을 적용하지 말라는 옵션

---

## 변수 - SpringEL 표현식

- 변수 표현식 :`${...}`

### 객체의 프로퍼티 접근
```html
<h1>SpringEL 표현식</h1>
<ul>Object
    <li>${user.username} = <span th:text="${user.username}"></span></li>
    <li>${user['username']} = <span th:text="${user['username']}"></span></li>
    <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></li>
</ul>
<ul>List
    <li>${users[0].username} = <span th:text="${users[0].username}"></span></li>
    <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></span></li>
    <li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></span></li>
</ul>
<ul>Map
    <li>${userMap['userA'].username} = <span th:text="${userMap['userA'].username}"></span></li>
    <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']['username']}"></span></li>
    <li>${userMap['userA'].getUsername()} = <span th:text="${userMap['userA'].getUsername()}"></span></li>
</ul>
```
1. 객체 
   - `user` : user 객체
   - `user.username` : user의 username 속성에 접근 (`getUsername()` 사용)
   - `user['username']` : 위와 같은 표현식
   - `user.getUsername()` : user의 `getUsername()` 메서드를 직접 호출

2. List
   - `users[0]` : 리스트의 0번 인덱스에 위치한 객체
   - `users[0].username` : 리스트의 0번 인덱스에 위치한 객체의 username 프로퍼티 접근 (`users[0].getUsername()` 사용)
   - `users[0]['username']` : 위와 같음
   - `users[0].getUsername()` : 리스트에서 첫번째 값을 찾고 메서드 직접 호출
   
3. Map
   - `userMap['userA']` : 맵의 'userA' 키에 맵핑된 객체
   - `userMap['userA'].username` : 맵의 'userA' 키에  맵핑된 객체의 username 프로퍼티 접근 (`userMap['userA'].getUsername()` 사용)
   - `userMAp['userA']['username']` : 위와 같음
   - `userMap['userA'].getUsername()` : 맵의 'userA' 키에  맵핑된 객체를 찾고 메서드 직접 호출

### 지역변수 선언
```html
<h1> 지역변수 - (th:with) </h1>
<div th:with="first=${users[0]}">
    <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>
</div>
```
- `th:with`를 사용하면 그 태그 영역에서 지역변수를 선언해서 사용할 수 있음.
- 단, 지역변수는 선언된 태그 안에서만 사용 가능

---

## 타임리프에서 기본적으로 제공하는 객체들

### 기본 객체
```html
<h1>식 기본 객체 (Expression Basic Objects)</h1>
<ul>
    <li>request = <span th:text="${#request}"></span></li>
    <li>response = <span th:text="${#response}"></span></li>
    <li>session = <span th:text="${#session}"></span></li>
    <li>servletContext = <span th:text="${#servletContext}"></span></li>
    <li>locale = <span th:text="${#locale}"></span></li>
```
- 요청 : `${#request}` (`HttpServletRequest` 객체가 그대로 제공)
- 응답 : `${#response}` (`HttpServletResponse` 객체가 그대로 제공)
- 세션 : `${#session}` (`HttpSession` 객체가 그대로 제공)
- ServletContext : `${#servletContext}`
- 지역 : `${#locale}`

### 편의 객체
```html
<h1>편의 객체</h1>
<ul>
    <li>Request Parameter = <span th:text="${param.paramData}"></span></li>
    <li>session = <span th:text="${session.sessionData}"></span></li>
    <li>spring bean = <span th:text="${@helloBean.hello('Spring!')}"></span></li>
</ul>
```
- 요청 파라미터 접근
  - 예) 파라미터가 param이고 값이 paramData일때 : `${param.paramData}`
- Http 세션 접근
  - 예) session에 sessionData로 맵핑한 값이 있을 때 : `${session.sessionData}`
- 스프링 빈 접근
  - 예) 빈으로 helloBean으로 등록한 빈이 있을 때 : `${@helloBean.hello('spring!')}`

---

## 유틸리티 객체와 날짜

### 타임리프 유틸리티 객체들
- `#message` : 메시지, 국제화 처리
- `#uris` : URI escape 지원
- `#dates` : `java.util.Date`(Deprecated) 서식 지원
- `#calendars` : `java.util.Calendar` 서식 지원
- `#temporals` : `java.time.*` 서식 지원
- `#numbers` : 숫자 서식 지원
- `#strings` : 문자 관련 편의 기능
- `#objects` : 객체 관련 기능
- `#bools` : boolean 관련 기능
- `#arrays` : 배열 관련 기능
- `#lists`, `#sets`, `#maps` : 컬렉션 관련 기능
- `#ids` :  아이디 처리 관련 기능

### 날짜, 시간
```html
<h1>LocalDateTime</h1>
<ul>
    <li>${localDateTime} (디폴트) = <span th:text="${localDateTime}"></span></li>
    <li>${#temporals.format(localDateTime,'yyyy-MM-dd HH:mm:ss')} = <span th:text="${#temporals.format(localDateTime,'yyyy-MM-dd HH:mm:ss')}"></span></li>
</ul>
<h1>LocalDateTime - Utils</h1>
<ul>
    <li>${#temporals.day(localDateTime)} = <span th:text="${#temporals.day(localDateTime)}"></span></li>
    <li>${#temporals.month(localDateTime)} = <span th:text="${#temporals.month(localDateTime)}"></span></li>
    <li>${#temporals.monthName(localDateTime)} = <span th:text="${#temporals.monthName(localDateTime)}"></span></li>
    <li>${#temporals.monthNameShort(localDateTime)} = <span th:text="${#temporals.monthNameShort(localDateTime)}"></span></li>
    <li>${#temporals.year(localDateTime)} = <span th:text="${#temporals.year(localDateTime)}"></span></li>
    <li>${#temporals.dayOfWeek(localDateTime)} = <span th:text="${#temporals.dayOfWeek(localDateTime)}"></span></li>
    <li>${#temporals.dayOfWeekName(localDateTime)} = <span th:text="${#temporals.dayOfWeekName(localDateTime)}"></span></li>
    <li>${#temporals.dayOfWeekNameShort(localDateTime)} = <span th:text="${#temporals.dayOfWeekNameShort(localDateTime)}"></span></li>
    <li>${#temporals.hour(localDateTime)} = <span th:text="${#temporals.hour(localDateTime)}"></span></li>
    <li>${#temporals.minute(localDateTime)} = <span th:text="${#temporals.minute(localDateTime)}"></span></li>
    <li>${#temporals.second(localDateTime)} = <span th:text="${#temporals.second(localDateTime)}"></span></li>
    <li>${#temporals.nanosecond(localDateTime)} = <span th:text="${#temporals.nanosecond(localDateTime)}"></span></li>
</ul>
```
- `#temporals` : java8 이후 추가된 `LocalDate`, `localTime`, `LocalDateTime`, `Instant` 지원
  - 추가 라이브러리 `thymeleaf-extras-java8time`이 필요한데, 스프링 부트가 알아서 자동으로 추가, 통합해줌

---

## URL

- 문법 : `@{...}`
- a 태그에서 : `<a th:href="@{...}"></a>`

### URL 변수 맵핑
1. 단순 URL
   - `@{/hello}` -> `/hello`

2. 변수 맵핑
   - URL 표현식 안에서 변수를 사용할 경우 앞에서 `{...}`을 통해 경로 변수를 선언
   - 뒤의 ()에서 변수에 맵핑될 값을 선언한다.
   - 앞에서 선언되지 않은 변수를 () 안에서 선언할 경우 쿼리 파라미터로 맵핑된다.

### URL 변수 맵핑 - 예시
```html
<li><a th:href="@{/hello}">basic url</a></li>
```
- 변수가 선언되지 않았으므로 단순 URL이 렌더링된다.
```html
<li><a th:href="@{/hello(param1=${param1}, param2=${param2})}">hello query param!</a></li>
```
- () 앞에서 param1, param2가 선언된 바 없다.
- param1, param2는 앞에서 선언되지 않았으므로 쿼리 파라미터로 맵핑된다.
```html
<li><a th:href="@{/hello/{param1}/{param2}(param1=${param1},param2=${param2})}">path variable</a></li>
```
- () 앞에서 param1, param2가 선언되었다.
- param1, param2는 경로변수로 선언되었으므로 그대로 맵핑된다.
```html
<li><a th:href="@{/hello/{param1}(param1=${param1}, param2=${param2})}">path variable + query parameter</a></li>
```
- () 앞에 param1만 선언되고 param2는 선언되지 않았다.
- param1은 앞에서 경로변수로 선언됐으므로 그대로 맵핑된다.
- param2는 앞에서 경로변수로 선언되지 않았으므로 쿼리파라미터로 취급된다.

---

## 리터럴

- 소스 코드 상에서 고정된 값
- 문자 : 'hello', ...
- 숫자 : 10, ...
- bool : true, false, ...
- null : null

### 문자 리터럴 취급
```html
<h1>리터럴</h1>
<ul>
    <!-- 주의! 띄어쓰기로 된 리터럴을 아래 주석과 같이 그대로 "" 사이에 풀어쓰면 예외가 발생함 !-->
    <!--    <li>"hello world!" = <span th:text="hello world!"></span></li>-->

    <li>'hello' + 'world!' = <span th:text="'hello' + ' world!'"></span></li>
    <li>'hello world!' = <span th:text="'hello world!'"></span></li>
    <li>'hello ' + ${data} = <span th:text="'hello' + ${data}"></span></li>
    <li>리터럴 대체 |hello ${data}| = <span th:text="|hello ${data}|"></span></li>
</ul>
```
1. 원칙적으로 문자 리터럴은 작은 따옴표(`'`)으로 감싸야하며, 결합은 + 을 통해 결합해야한다.
   - 예) th:text="'hello'"

2. 공백 없이 쭉 이어질 경우 작은 따옴표(`'`)를 생략할 수 있다.
   - 허용 : `A-Z`, `a-z`, `0-9`, `[]`, `.` `-`, `_`
     - 예) th:text="hello"
   - 예외 : 공백이 있는 상태로 풀어 사용할 경우 예외가 발생한다!!!
     - 예) th:text="hello world!"

3. 리터럴 대체 : 바(`|`)로 감싸면 리터럴로 대체된다.
   - 예) th:text="|hello world!|"
   - 예) th:text="|hello ${world}|"

---