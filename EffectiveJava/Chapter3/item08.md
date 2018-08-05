##8 equals를 재정의할 때는 일반 규약을 따르라
<br/>
### 8.1 equals 메서드를 재정의 하지 않아도 되는 경우
equals 메서드는 재정의 하기 쉬워 보이지만 실수할 여지가 많기 때문에 아래 같은 상황에서는 재정의를 하지 않아 문제 발생을 피할 수 있다.

#### 8.1.1 각각의 객체가 고유할 때
값 대신 활성 개체(active entity)를 나타내는 Thread 같은 클래스가 이 조건에 부합한다. 이런 클래스는 Object의 equals를 그대로 사용할 수 있다.

#### 8.1.2 논리적 동일성(logical equality) 검사 방법이 필요 없을 때
java.util.Random 클래스는 두 객체가 같은 나수열(sequence of random numbers)을 만드는지 검사하는 equals 메서드를 재정의 할 수도 있지었지만 클라이언트가 그런 기능을 원할것 같지 않아 구현하지 않았다.

#### 8.1.3 상위 클래스의 equals가 하위 클래스에서 사용하기에도 적당할 때
대부분의 AbstractSet, AbstractList, AbstractMap 하위 클래스는 equals 메서드를 재정의 하지 않고 그대로 사용한다.

#### 8.1.4 클래스가 private, package-private로 선언되었고, equals 메서드를 호출할 일이 없다.
이때는 equals 메서드를 사용하지 않기 때문에 재정의가 필요 없지만 실수로 사용하는 것을 방지하기 위해 방버 코드를 재정의 하는 것을 추천한다.
```java
@Override public Boolean equals(Object o)
{
	throw new AssetionError();
}
```
<br/>
### 8.2 equals 메서드 재정의가 바람직한 경우
#### 8.2.1 객체 동일성(object equality)가 아닌 논리적 동일성(logical equality)의 개념을 지원하는 클래스일 때
#### 8.2.2 상위 클래스의 equals가 하위 클래스가 필요한 조건을 충족 시키지 못할 때
Integer나 Date같은 대부분의 값 클래스가 이 경우에 해당한다.
equals 메서드를 재정의 하지 않으면 Map의 Key나 Set의 원소로 사용할 수 없고 사용할 경우 예측하기 어려운 문제가 발생할수 있다.
<br/>
### 8.3 equals 메서드를 재정의 할 필요 없는 값 클래스
개체 통제 기능을 사용해 값마다 최대 하나의 객체만 존재하도록 제한하는 클래스는 equals 메서드를 재정의할 필요가 없다.
열거 자료형(enum)도 이 범주에 든다. 이런 클래스에서는 갳체 동일성이 곧 논리적 동일성이다. 
<br/>
### 8.4 equals 메서드를 정의할 때 준수해야 하는 일반 규약(general contraction)
equals 메서드는 동치 관계(equivalence relation)을 구현한다. 다음과 같은 과계를 동치 관계라 한다.
#### 8.4.1 반사성(reflexive)
> null이 아닌 참조 x가 있을 때,  x.equals(x)는 true를 반환한다.

모든 객체는 자기 자신과 같아야 한다는 뜻으로 일부러 깨트리기도 어렵다. 굳이 요구 사항을 깨트린 객체가 있다면 방금 컬렉션에 넣은 객체를 contains로 찾았을 때 false 가 반환될 것이다.
#### 8.4.2 대칭성(symmetric)
> null 아닌 참조 x와 y가 있을 때, x.equals(y)는 y.equals(x)가 true일 때만 true를 반환한다.

두 객체에게 서로 같은지 물으면 같은 답이 나와야 한다는 것이다. 반사성에 비해 깨지기 쉬운데 아래 코드를 참고해보자.
```java
public final class CaseInsensitiveString
{
	private final String s;
	
	public CaseInsensitiveString(String s)
	{
		if( s == null)
		{
			throw new NullPointException();
		}
		
		this.s = s;
	}
	
	@Override public boolean equals(Object o)
	{
		if( o instanceof CaseInsensitiveString)
		{
			return s.equalsIgnoreCase(
			((CaseInsensitiveString) o).s);
		}
		
		if(o instanceof String)
		{
			return s.equalsIgnoreCase((String) o);
		}
	}
}
```
이 클래스의 equals는 의도는 좋았으나, 일반 문자열과의 equals비교에서 문제가 있다.
CaseInsensitiveString 클래스의 equals에서는 String 클래스에 대한 처리가 있지만 String 클래스의 equals에서는 CaseInsensitiveString 클래스에 대한 처리가 없기 때문에 대칭성이 깨진다.
이렇게 규약이 깨진 객체를 만나는 경우 어떻게 행동될지 예측할 수 없기 때문에 아래처럼 CaseInsensitiveString가 String을 처리하지 않도록 해야 한다.
```java
@Override public Boolean equals(Object o)
{
	return o instanceof CaseInsensitiveString &&
		((CaseInsensitiveString)o).s.equalsIgnoreCase(s);
}
```

#### 8.4.3 추이성(transitive)
> null 아닌 참조 x, y, z가 있을 때, x.equals(y)가 true이고 y.equals(z)가 true이면 x.equals(z)도 true이다.

추이성 역시 쉽게 깨질 수 있다.
상위 클래스에는 없는 값 컴포넌트(value component)를 하위 클래스에 추가하는 상황을 생각해보자.
```java
public class Point
{
	private final int x;
	private final int y;
	public Point (int x, int y)
	{
		this.x = x;
		this.y = y;
	}
	
	@Override public boolean equals(Object o)
	{
		if(! (o instanceof Point))
		{
			return false;
		}
		Point p = (Point) o;
		return p.x == x && p.y == y;
	}
}
```

```java
pu
```
#### 8.4.4 일관성(consistent)
> null이 아닌 참조 x와 y가 있을 때, equals를 통해 비교되는 정보에 아무 변화가 없다면, x.equals(y) 호출 결과는 호출 횟수에 상관 없이 항상 같아야 한다.
#### 8.4.5 null 아닌 참조 x에 대해서, x.equals(null)은 항상 false이다.
	

	
	
	
	
