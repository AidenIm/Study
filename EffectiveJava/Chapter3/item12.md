## 11 Comparable 구현을 고려하라
Comparable을 구현하는 클래스들은 Comparable 인터페이스를 사용하는 Collection 구현체에 정의된 정렬, 최대/최소 구하기 등의 기능을 쉽게 이용할 수 있다. 
### 11.1 Comparable 규약
본 메서드는 이 객체와 인자로 주어진 객체를 비교한다.
이 객체의 값이 인자로 **주어진 객체보다 작으면 음수, 같으면 0, 크면 양수를 반환**한다. 인자로 전달된 자료형이 이 객체와 비교 불가능한 자료형인 경우에는 ClassCastEception 예외를 던진다.

compareTo를 구현할 때는 모든 x와 y에 대해 
> sgn(expr) 함수는 expr 값이 음수면 -1, 양수면 1, 0이면 0을 리턴한다.

- `sng(x.compareTo(y)) = sng(y.compareTo(x))`
- `x.compareTo(y) > y.compareTo(z)` 이면 `x.compareTo(z) > 0`
- `x.compareTo(y) = 0` 이면 `sgn(x.compareTo(z)) = sgn(y.compareTo(z))`
- 강력히 추천하지만 절대적으로 요구되는 조건이 아닌것
`(x.compareTo(y) = 0) = (x.equals(y))`
이 조건을 만족하지 않는 Comparable 클래스는 equals와 부합하지 않음을 명시하는 것이 좋다.
   
### 11.2 compareTo와 equals
만약 규약의 마지막 조건을 만족하지 못하는 클래스를 만들었다고 해도 동작에 큰 문제는 없을 것이다. 
하지만 해당 객체를 **정렬된** 컬렉션에 저장할 경우 해당 컬렉션은 컬렉션 인터페이스(Collection, Set, Map)의 일반 규약을 위반하게 될 수 있다.
예를 들어 BigDecimal 클래스는 마지막 규약을 지키지 않는데 new BigDecimal("1.0")과 new BigDecimal("1.00")은 equals 에서 False를 반환하지만 CompareTo 에서는 True를 반환한다.
이 객체들을 HashSet에 넣으면 두 객체가 들어가지만 TreeSet에 넣으면 하나의 객체만 들어간다.

### 11.3 구현
```java
public final class CaseInsensitiveString
	implements Comparable<CaseInsensitiveString>{
	
	public int compareTo(CaseInsensitiveString cis){
		return String.CASE_INSESITIVE_ORDER.compare(s, cis.s);
	}
	...
}
```
일반적으로 Comparable은 자신의 타입을 지정해 구현해 자신과 동일한 객체만 비교하도록 한다.