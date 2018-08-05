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
public class ColorPoint extends Point
{
	private final Color color;
	
	public ColorPoint(int x, int y, Color color)
	{
		super(x, y);
		this.color = color;
	}
}
```
ColorPoint 클래스의 equals는 어떻게 구현해야 하나?
재정의 하지 않는다면 Point 클래스의 equals가 그래도 상속되므로 생각 정보가 비교되지 않을 것이다. equals의 규약을 어기는 것은 아니지만 바람직하지 않다. 
<br/>
만약 equals를 아래와 같이 정의 했다고 해보자.
```java
@Override public boolean equals(Object o)
{
	if(! (o instanceof ColorPoint)
	{
		return false;
	}
		
	return super.equals(o) && ((ColorPoint) o).color == color;
}
```
이 경우 Point, ColorPoint 사이의 대칭성이 위반된다.
```java
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2);

// p1.equals(p2) : false
// p2.equals(p1) : true
```
그럼 Point와 비교할 때는 생상정보를 무시하게 하면 어떨까?

```java
@Override public boolean equals(Object o)
{
	if(! (o instanceof Point)
	{
		return false;
	}
	
	if(! (o instanceof ColorPoint)
	{
		return o.equals(this);
	}
		
	return super.equals(o) && ((ColorPoint) o).color == color;
}
```
이 경우에는 대칭성이 보장되지만 추이성이 위반된다.
```java
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2);
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);

// p1.equals(p2) : true
// p2.equals(p3) : true
// p1.equals(p3) : false
```

그럼 이 문제의 해결책은 무엇일까?
이 문제는 객체 지향 언어에서 동치 관계(equivalence relation)를 구현할 때 발생하는 본질적인 문제로 객체 추상화의 혜택을 누리는 이상
**객체 생성 가능(instantiable) 클래스를 계승하여 새로운 값 컴포넌트를 추가하면서 equals 규약을 어기지 않을 방법은 없다.**
<br/>
equals 메서드를 구현할 때 instanceof 대신 getClass 메서드를 사용하면 기존 클래스를 확장하여 새로운 값 컴포넌트를 추가해도 
equals 규약을 준수할 수 있다는 말이 있긴 하지만 이렇게 구현하면 **리스코프 대체 원칙**을 위반하므로 올바르지 않다. 
> **리스코프 대체 원칙 (Liskov subsitution principle)**
> 어떤 자료형의 중요한 속성(property)는 하위 자료형애도 그대로 유지되어서 그 자료형을 위한 메서드는 하위 자료형에서도 잘 동작해야 한다.
```java
// 리스코프 대체 원칙 위반
// Point 객체와 equals를 활용하는 함수에서 올바르게 동작하지 않는다.
// ex. List<Point>.Contains()
@Override public boolean equals(Object o)
{
	if( o == null || o.getClass() == getClass())
	{
		return false;
	}
	
	Point p = (Point) o;
	return p.x == x && p.y == y;
}
```

이 문제 해결을 위해서는 Point 클래스를 상속하지 않고 ColorPonit의 priavet 필드로 만들어 사용하는 방법을 사용할 수 있다. 

```java
public class ColorPoint
{
	private final Point point;
	private final Color color;
	
	public ColorPoint(int x, int y, Color color)
	{
		if(color == null)
		{
			throw new NullPointerException();
		}
		Point = new Point(x, y);
		this.color = color;
	}
	
	public Point asPoint()
	{
		return point;
	}
	
	@Override public boolean equals(Object o)
	{
		if(!(o instanceof ColorPoint))
		{
			return false;
		}
		
		ColorPoint cp = (ColorPoint) o;
		return cp.point.equals(point) && cp.color.equals(color);
	}
}
```

상위 클래스가 abstract 라면 상위 객체를 직접 만들 수 없으므로 equals규약을 어기지 않고도 값 필드를 추가할 수 있다.

#### 8.4.4 일관성(consistent)
> null이 아닌 참조 x와 y가 있을 때, equals를 통해 비교되는 정보에 아무 변화가 없다면, x.equals(y) 호출 결과는 호출 횟수에 상관 없이 항상 같아야 한다.

변경 불가능한 객체들간의 동치 관계는 시간이 지나도 달라지지 않아야 한다. 
그리고 **신뢰성이 보장되지 않는 자원(unreliable resource)들을 비교하는 equals를 구현하는 것은 삼가라.**
예를 들어 java.net.URL의 equals 메서드는 URL에 대응하는 IP주소를 비교하는데 URL을 IP주소로 변경할 때 네트워크에 접속해야 하므로 언제나 같은 결과가 나온다고 보장할 수 없다. 
극히 드문 몇 가지 경우를 제외하면 equals 메서드는 메모리에 존재하는 객체들만을 사용해 결정정(deterministic) 게산을 수행하도록 구현돼야 한다.

#### 8.4.5 null 아닌 참조 x에 대해서, x.equals(null)은 항상 false이다.
아래와 같은 코드가 있다고 했을 때 인자로 넘어온 객체 o가 null 인 경우를 구분해야 할 것 같지만 instanceof에서 o가 null 인경우 false를 리턴하게 되기 때문에 별도의 처리를 하지 않아도 된다.
```java
@Override public boolean equals(Object o)
{
	if( !(o instanceof MyType))
	{
		return false;
	}
	
	//...
}
```

### 8.5 정리
#### 8.5.1 연산자를 사용하여 equals의 인자가 자기 자신인지 검사하라
#### 8.5.2 instanceof 연산자를 사용하여 인자의 자료형이 정확한지 검사하라.
#### 8.5.3 equals의 인자를 정확한 자료형으로 반환하라
#### 8.5.4 "중요" 필드 각각이 인자로 주어진 객체의 해당 필드와 일치하는지 검사한다.
#### 8.5.5 equals 메서드 구현을 끝냈다면, 대칭성, 추이성, 일관성의 세 속성이 만족되는지 검토하라.
#### 8.5.6 기타
- equals를 구현할 때는 hashCode도 재정의 하라
- 너무 머리 쓰지마라
- equals 메서드의 인자형을 object에서 다른 것으로 바꾸지 마라.

	
	
	
	
