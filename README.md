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
