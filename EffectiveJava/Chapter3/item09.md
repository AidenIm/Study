## 9 equals를 재정의할 때는 반드시 hashCode도 재정의 하라
<br/>
Object 클래스 명세에서 복사해 온 일반 규약은 아래와 같다.

- 응용프로그램 실행 중에 같은 객체의 hashCode를 여러 번 호출하는 경우, equals가 사용하는 정보들이 변경되지 않았다면, 언제나 동일한 정수(integer)가 반환되어야 한다. 다만 프로그램이 종료되었다가 다시 실행되어도 같은 값이 나올 필요는 없다.
- equals(Object) 메서드가 같다고 판정한 두 객체의 hashCode 값은 같아야 한다.
- equals(Object) 메서드가 다르다고 판정한 두 객체의 hashCode값은 꼭 다를 필요는 없다. 그러나 서로 다른 hashCode 값이 나오면 해시 테이블(hashtable)의 성능이 향상될 수 있다는 점은 이해하고 있어야 한다.

### 9.1 hashCode를 재정의 하지 않으면 두 번째 규약이 위반된다.
equals 메서드가 논리적으로 같다고 판단한 객체라도 Object의 hashCode 입장에서 보면 그다지 공통점이 없는 두 객체일 뿐이다.

### 9.2 hashCode 제정의 가이드
아래 절차를 통해 이상적인 해시 함수에 *가까운*함수를 만들 수 있다.

#### 주의
> -  중복 필드 (redundant field)는 제외해도 된다.
> - equals 계산에 쓰이지 않는 필드는 반드시 제외해야 한다. (두번째 제약 위반)
> - 성능 개선을 위해 객체의 중요 부분을 해시 코드 계산 과정에서 생략하면 안 된다.
> - 해시 생성 로직을 문서화 하면 클라이언트가 해시 생성 로직에 의존적인 코드를 작성 할 수 있어 좋지 않다. 해시 생성 로직은 문서화 하지 말고 꾸준히 개선할 수 있도록 숨기는 것이 좋다.

#### 1. 17과 같은 0이 아닌 상수를 result라는 이름의 int 변수에 저장한다.
#### 2. 객체 안에 있는 모든 중요 필드 f(equals 메서드가 사용하는 필드)에 대해서 아래의 절차를 시행한다.
**A.** 해당 필드에 대한 int 해시 코드 c를 계산한다.
	
> |Type|Convert|
> |---|---|
> | boolean|f ? 1 :0|
> |byte, char, short, int| (int) f|
> |long|(int)(f^(f >> 32))|
> |float|Float.floatToIntBits(f)|
> |double|t = Double.doubleToLongBits(f) and (int)(t^(t >> 32))|
> |Reference|equals가 재귀적으로 호출되는 경우 hashCode도 재귀적으로 호출해 계산한다. </br>만약 좀 더 복잡한 비교가 필요하다면 대표 형태(canonical representation)의 hashCode를 호출한다. </br>필드 값이 null인 경우 0을 반환한다.|
> |Array|각 배열의 원소를 필드 처럼 계산한다.</br>만약 배열내의 모든 원소가 중요하다면 JDK 1.5부터 적용되는 Array.hashCode를 사용할 수 있다.|

**B.** 위의 절차 A에서 계산된 해시 코드 c를 아래처럼 결합한다.

> `result = 31 * result + c`
> * 31의 곱셈은 시프트와 뺄셈의 조합으로 바꿀 수 있어 더 좋은 성능을 기대할 수 있다. (컴파일러에서 최적화 해준다.)

#### 3. result를 반환한다.
#### 4. 동치관계에 있는 해시 코드의 값이 똑같이 계산되는지 점검하고 문제가 있다면 수정하라.

### 9.3 hashCode 계산 비용이 높은 변경 불가능 클래스는 hashCode를 캐싱할 수 있다
```java
// 초기화 지연 기법을 사용해 해시 코드 캐싱
private volatile int hashCode;

@override public int hashCode(){
	int result = hashCode;
	if(result == 0){
		// generate hashCode
	}
}
```
		