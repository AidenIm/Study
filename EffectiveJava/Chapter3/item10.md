##10 toString은 항상 재정의 하라
<br/>
java.lang.Object 클래스가 제공하는 toString 메서드는 **클래스이름@16진수해시코드**의 정보를 제공한다.
이 정보는 그다지 유용하지 못하기 때문에 toString을 재정의하는 것이 바람직 하다.

> equals와 hashCode의 일반 규약을 지키는 것보다는 덜 중요하지만
> toString을 잘 만들어 놓으면 클래스를 좀 더 쾌적하게 사용할 수 있다.

### 10.1 가능하다면 toString객체 내의 중요 정보를 전부 담아 반환해야 한다
객체가 아주 크거나 문자열로 반환하기 까다로운 상태 정보를 담고 있는 경우에는 아래 처럼 요약 정보를 반환하도록 하는 것이 좋다.
> Manhattan white pages (148745 listings)

### 10.2 toString 문자열에 형식 명시하기
전화번호나 행렬같은 정보를 표현하는 클래스의 경우 toString에 형식을 같이 명시해서 사람이 읽기 쉽게 표현할 수 있다. </br>하지만 이 클래스가 널리 사용될 경우 **클래스 형식에 종속적인 코드들이 생성**되어 toString을 바꾸고 개선할 수 없게 된다.

### 10.3 toString이 어떤 의도인지 분명하게 남겨두기
toString이 반환하는 문자열의 형식을 명시하건 그렇지 않건 간에, 어떤 의도인지는 문서에 분명히 남겨야 한다.
```java
/*
	전화번호를 문자열로 변환해서 반환한다.
	문자열은 (XXX) YYY-ZZZZ 형식으로 14개 문자로 구성된다.
	...
	
	지역번호를 닫는 괄호와 국번 사이에는 공백이 온다는 것에 주의하자.
*/
@Override public String toString(){
	return String.format("(%03d) %03d-%04d");
}
```
형식을 명시하지 않기로 했다면 주석은 아래와 같이 달아야 할 것이다.
```java
/*
	전화번호를 문자열로 변환해서 반환한다.
	전해진 문자열 형식은 없으며, 바뀔 수 있다.
*/
@Override public String toString(){
	return ...
}
```
#### 10.4 toString이 반환하는 정보들은 전부 프로그래밍을 통해 가져올 수 있어야 한다
예를 들어 전화번호를 나타내는 클래스는 지역 번호, 국번, 회선 번호 등의 정보를 가져올 수 있는 접근자 메서드를 포함해야 한다. 그렇지 않으면 프로그래머들은 toString을 파싱하려 할 것이고 성능이나 메인터넌스에 취약한 코드를 생성하게 된다.