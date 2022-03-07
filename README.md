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

## 연산
대체적으로 java의 연산과 비슷하게 작동한다.
1. 산술연산 : `+`, `-`, `*`, `/`, `%`

2. 비교연산 : `<`. `>`는 가급적 HTML 엔티티를 사용하는 것이 좋다.
   - `>` (gt)
   - `<` (lt)
   - `>=` (ge)
   - `<=` (le)
   - `!`(not)
   - `==` (eq)
   - `!=` (neq, ne)

3. 삼항연산자
    ```html
    <li>(10 % 2 == 0)? '짝수':'홀수' = <span th:text="(10 % 2 == 0 )? '짝수':'홀수'"></span></li>
    ```
   - 조건에 부합하면 왼쪽의 값을, 부합하지 않으면 오른쪽 값을 렌더링함

4. Elvis 연산자 (삼항연산자의 편의 버전)
   ```html
   <li>${data}?: '데이터가 없습니다' = <span th:text="${data}?: '데이터가 없습니다.'"></span></li>
   <li>${nullData}?: '데이터가 없습니다' = <span th:text="${nullData}?: '데이터가 없습니다.'"></span></li>
   ```
   - 값이 존재하면 값을 출력, 존재하지 않으면 `:` 뒤의 내용을 출력


5. No-Operation
   ```html
   <li>${data}?: _ = <span th:text="${data}?: _">데이터가 없습니다.</span></li>
   <li>${nullData}?: _ = <span th:text="${nullData}?: _">데이터가 없습니다.</span></li>
   ```
   - 값이 존재하면 값을 출력하고, 존재하지 않을 경우 `_`에 의해, 타임리프 연산이 무시되어 태그로 감싸진 본문의 내용이 그대로 출력됨.

---

## 속성 값 설정

### 속성 덮어쓰기
```html
<input type="text" name="mock" th:name="userA" />
```
- `th:*`으로, 같은 속성이 있을 경우 덮어씀.
  - 예) 위의 경우, name 속성으로 "mock"이 지정되어있는데 `th:name`을 통해 덮어씀

### 속성값 추가
```html
- th:attrappend = <input type="text" class="text" th:attrappend="class=' large'"/><br />
- th:attrprepend = <input type="text" class="text" th:attrprepend="class='large '"/><br />
- th:classappend = <input type="text" class="text" th:classappend="large" /><br />
```
- `th:attrappend` : 속성값 뒤에 덧붙임
- `th:attrprepend` : 속성값 앞에 덧붙임
- `th:classappend` : class 속성에 자연스럽게 추가함
  - `attrappend`, `attrprepend` 을 통해 추가시 띄어쓰기를 추가적으로 처리해야하는 번거로움이 있는데, `classappend`를 통해 자연스럽게 추가가 가능하다.

### checkbox 처리
```html
- th:checked="true" <input type="checkbox" name="active" th:checked="true" /><br />
- th:checked="false" <input type="checkbox" name="active" th:checked="false" /><br />
- checked="true" <input type="checkbox" name="active" checked="true" /><br />
- checked="false" <input type="checkbox" name="active" checked="false" /><br />
- checked="checked" <input type="checkbox" name="active" checked="checked" /><br />
- checked="unchecked" <input type="checkbox" name="active" checked="uncheced" /><br />
```
- checkbox는 checked 속성에 어떤 값을 지정하든 항상 체크되는 불편함이 존재
- `th:checked`를 지정시, true일 경우 checked 속성을 추가하고, false일 때 속성을 추가하지 않는 식으로 처리하기 떄문에 true, false값이 넘어왔을 때 자연스럽게 처리 가능하다.

---

## 반복
- 기본적으로 `th:each`를 사용
- 추가적으로 반복자의 상태를 알 수 있는 기능도 지원됨

### th:each
```html
    <tr th:each="user : ${users}">
        <td th:text="${user.username}">username</td>
        <td th:text="${user.age}">0</td>
    </tr>
```
- `th:each="요소 : ${반복대상}"`
- List뿐 아니라, 배열, `java.util.Iterable`, `java.util.Enumeration` 인터페이스를 구현한 모든 객체에 대해 적용 가능함.
  - Map도 사용가능하기 한데, 이 경우 요소에 담기는 값은 `Map.Entry`가 된다.

### 반복 상태
```html
    <tr th:each="user, userStat : ${users}">
        <td th:text="${userStat.count}">count</td>
        <td th:text="${user.username}">username</td>
        <td th:text="${user.age}">age</td>
        <td>
            index = <span th:text="${userStat.index}"></span>
            count = <span th:text="${userStat.count}"></span>
            size = <span th:text="${userStat.size}"></span>
            even = <span th:text="${userStat.even}"></span>
            odd = <span th:text="${userStat.odd}"></span>
            first? = <span th:text="${userStat.first}"></span>
            last? = <span th:text="${userStat.last}"></span>
            current = <span th:text="${userStat.current}"></span>
        </td>
    </tr>
```
- `th:each="user, userStat : ${users}"`
  - 두번째 변수로 지정한 변수는 반복 상태를 담는 객체의 변수가 됨.
  - 두번째 변수를 생략가능한데 이 경우에는 변수명(`user`)+`Stat`이 됨.
- 메서드
  - `index` : 0부터 시작했을 때 현재 순서(java에서의 인덱스)
  - `count` : 1부터 시작했을 때 현재 순서(index + 1)
  - `size` : 전체 size(요소의 수)
  - `even`, `odd` : 짝수인지 홀수인지 여부(boolean)
  - `first`, `last` : 첫번째 요소인지 마지막 요소인지 여부(boolean)
  - `current` : 현재 객체 반환

---

## 조건부 출력

### if/unless
```html
        <td>
            <span th:text="${user.age}">0</span>
            <span th:text="'미성년자'" th:if="${user.age lt 20}">0</span>
            <span th:text="'미성년자'" th:unless="${user.age ge 20}">0</span>
        </td>
```
- `th:if="조건` : 조건이 참일 경우 태그의 내용을 렌더링
- `th:unless="조건"` : 조건이 거짓일 경우 태그의 내용을 렌더링하지 않음. 태그 자체가 소멸함

### switch-case
```html
        <td th:switch="${user.age}">
            <span th:case="10">10살</span>
            <span th:case="20">20살</span>
            <span th:case="*">기타</span>
        </td>
```
- `th:switch="대상"` : 조건 판단의 대상
- `th:case="값"` : 조건값
  - `th:case="*"` : default(만족하는 값이 없을 때 사용하는 디폴트)

---

## 주석

### 순수 HTML 주석
```html
<!--
<span th:text="${data}">html data</span>
-->
```
1. 렌더링 전 HTML파일
   - 화면에 보이지 않음.
   - 소스코드 상에는 주석의 내용이 남아있음.
2. 렌더링 후
   - 화면에 보이지 않음.
   - 하지만 순수 HTML 주석에 불과해서 주석 내용이 클라이언트 측에 노출됨

### 타임리프 파서 주석
```html
<!--/* [[$(data)]] */-->
```
```html
<!--/*-->
<span th:text="${data}">html data</span>
<!--*/-->
```
1. 렌더링 전 HTML파일
   - 코드를 열어보면 주석이 남아있음
   - 타임리프 주석문법의 적용을 받지 않는다.
     - 위의 주석은 HTML문법의 적용을 받아 화면에 보이지 않음.
     - 아래의 주석은 HTML문법을 적용받아 맨위, 맨 아래 라인만 주석처리 되고 가운데 라인은 브라우저 상에 그대로 노출됨

2. 렌더링 후
   - 화면에 출력되지 않음
   - 렌더링을 거치면서 주석 자체가 제거됐다.
   - 클라이언트 측에는 주석 내용이 노출되지 않음

### 타임리프 프로토타입 주석 (잘 안씀)
```html
<!--/*/
<span th:text="${data}">html data</span>
/*/-->
```
1. 렌더링 전 HTML파일
   - 코드를 열어보면 주석이 남아있음
   - HTML 주석문법의 적용을 받아 화면에 보이지 않는다.

2. 렌더링 후
   - 주석 안의 내용이 렌더링되어 화면에 출력됨
   - 타임리프 문법을 적용받아 렌더링된 결과가 화면에 출력됨
   - 클라이언트 측에서는 원래 주석이였다는 사실을 모른다.

---

## th:block 태그
```html
<th:block th:each="user : ${users}">
    <div>
        사용자 이름1 <span th:text="${user.username}"></span>
        사용자 나이1 <span th:text="${user.age}"></span>
    </div>
    <div>
        요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>
    </div>
</th:block>
```
- 타임리프 문법은 일반적으로 HTML 태그의 속성에 삽입하여 사용한다.
- 하지만 여러 태그에 걸쳐서 타임리프 문법을 적용하고 싶은 애매한 상황이 있음.
  - 방법 1 : 상위 div에 th 태그를 걸고, 하위 태그들를 묶어서 사용하는 방법
    - 렌더링 결과 상위 `div` 태그가 남음
  - **방법 2 : 상위 th:block 으로 묶어서 사용한는 방법 (타임리프에서 제공하는 독자적 태그)**
    - 렌더링 결과 `th:block` 태그가 소멸함
- 사용은 가급적 안 하는게 좋고, 정말 사용하기 애매한 경우에 생각해볼만한 태그

---

## 자바스크립트 인라인 (자바스크립트 지원)

- 자바스크립트에서도 타임리프를 편하게 사용하고 싶을 때가 있음.
- 이를 위해 타임리프가 지원하는 문법이 `th:inline="javascript`다.

### javascript inline 선언
```html
<script th:inline="javascript"></script>
```

### 텍스트 렌더링 지원
```javascript
    var username = [[${user.username}]];
    var age = [[${user.age}]];
```
```javascript
// 렌더링 - 인라인 적용 전
    var username = userA;
    var age = 10;
// 렌더링 - 인라인 적용 후
    var username = "userA";
    var age = 10;
```
- 타입에 맞게, 적절하게 렌더링됨.
- 이스케이프 문자가 섞여있을 경우 자바스크립트에 맞게 이스케이프 처리도 해줌

### 자바스크립트 내츄럴 템플릿
```javascript
    var username2 = /*[[${user.username}]]*/ "test username";
```
```javascript
// 렌더링 - 인라인 사용 전
    var username2 = /*userA*/ "test username";
// 렌더링 - 인라인 사용 후
    var username2 = "userA";
```
- 주석을 이용하여, 내츄럴 템플릿(순수 자바스크립트를 그대로 유지하면서, 뷰 템플릿을 사용) 기능을 제공함.
- 렌더링 결과, 주석 안의 결과로 치환되고 뒤의 내용은 지워진다.

### 객체 - JSON
```javascript
    var user = [[${user}]];
```
```javascript
// 렌더링 - 인라인 사용 전
    var user = BasicController.User(username=userA, age=10);
// 렌더링 - 인라인 사용 후
    var user = {"username":"userA","age":10};
```
- 인라인 사용 전에는, 객체의 `toString()`이 호출된 값
- 인라인 사용 후에는, JSON으로 변환해줌.

### 자바스크립트 인라인 each
```javascript
    [# th:each="user, stat : ${users}"]
    var user[[${stat.count}]] = [[${user}]];
    [/]
```
```javascript
    var user1 = {"username":"userA","age":10};
    var user2 = {"username":"userB","age":20};
    var user3 = {"username":"userC","age":30};
```
- 자바스크립트 내부적으로 `th:each`를 사용할 수 있음.

---

## 템플릿 fragment

`templates/teamplate/footer.html`
```html
<footer th:fragment="copy">
    푸터 자리 입니다.
</footer>

<footer th:fragment="copyParam (param1, param2)">
    <p>파라미터 자리 입니다.</p>
    <p th:text="${param1}"></p>
    <p th:text="${param2}"></p>
</footer>
```
- `th:fragment="프래그먼트명 (파라미터1, 파라미터2, ...)"`으로 선언
- 반복적으로 여러 곳에서 중복적으로 사용되는 HTML 태그를 fragment로 지정하여, 여러곳에서 호출하여 사용할 수 있게 함.
- 관련있는 fragment를 같은 html 파일에 메서드처럼 모아두고 호출하여 사용

```html
<h1>부분 포함</h1>
<h2>부분 포함 insert</h2>
<div th:insert="~{template/fragment/footer :: copy}"></div>

<h2>부분 포함 replace</h2>
<div th:replace="~{template/fragment/footer :: copy}"></div>

<h2>부분 포함 단순 표현식</h2>
<div th:replace="template/fragment/footer :: copy"></div>

<h1>파라미터 사용</h1>
<div th:replace="~{template/fragment/footer :: copyParam ('데이터1', '데이터2')}"></div>
```
- `th:...="~{source path :: fragment명 (인자1, 인자2, ...) }"`으로 호출
- `th:insert` : 선언된 태그(주로 div)를 상위 태그로 하여, 하위에서 fragment를 호출함
- `th:replace` : 선언된 태그(주로 div)를 대체하여 fragment를 호출함. (기존 태그는 소멸함)

---

## 템플릿 레이아웃
- `layout`을 정의해두고, 각각의 html의 개별적인 코드조각들만 `layout`에 넘겨서 사용하는 방법
- 각각의 페이지별로 공통되는 부분은 layout에서 관리하고, 다른 부분만 넘겨서 처리함으로서 유지보수가 더 편해짐

```html
<!DOCTYPE html>
<html th:fragment="layout (title, content)" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:replace="${title}">레이아웃 타이틀</title>
</head>
<body>
<h1>레이아웃 H1</h1>
<div th:replace="${content}">
    <p>레이아웃 컨텐츠</p>
</div>
<footer>
    레이아웃 푸터
</footer>
</body>
</html>
```
- 레이아웃 파일에서 header 라든지 html 규모의 태그 단위로 fragment를 정의
```html
<!DOCTYPE html>
<html th:replace="template/layoutExtend/layoutFile :: layout(~{::title}, ~{::section})"
      xmlns:th="http://www.thymeleaf.org">
<head>
    <title>메인 페이지 타이틀</title>
</head>
<body>
<section>
    <p>메인 페이지 컨텐츠</p>
    <p>메인 페이지 포함 내용</p>
</section>
</body>
</html>
```
- `th:replace` : 대체
- 레이아웃을 사용하는 쪽에서는 fragment 호출 인자로 필요한 태그들을 지정하여 넘김
  - `~{::태그명}` : 태그명에 해당하는 것들을 싹 넘김
- 사용측의 html 영역이 레이아웃으로 싹 대체되고, 일부 지정해준 인자부분만 레이아웃측에 넘겨서, 대체 변경

---
