## 11 clone을 재정의할 때는 신중하라

### 11.1 Clone의 일반적인 규약
객체의 본사본을 만들어서 반환한다. "복사"의 정확한 의미는 클래스마다 다르다.
일반적으로는 다음의 조건이 충족되어야 한다. 객체 x가 있다고 하자.

- x.clone() != x : **TRUE**
- x.clone().getClass() == x.getClass() : **FALSE**
- x.clone().equals(x) : **TRUE** (반드시 TRUE는 아니어도 됨)
- 객체를 복사하면 보통 같은 클래스의 새로운 객체가 만들어지는데, 내부 자료구조까지 복사해야 될 수도 있다. 
- 어떤 생성자도 호출되지 않는다. 

### 11.2 Clone 구현시 주의할점
#### 11.2.1 복제할 객체의 모든 필드가 기본 자료형 이거나 변경 불가능 객체 참조일 때
복제할 객체가 변경 가능 객체에 대한 참조를 가지고 있고 모든 객체가 clone을 구현하고 있다면, 단순히 상위 클래스의 Clone을 재귀적으로 호출해 객체를 복사할 수 있다.
```java
@Override public PhoneNumber clone(){
	try{
		return (PhoneNumber) super.clone();
	} catch ( CloneNotSupportException e){
		throw new AssertionError();
	}
}
```
> JDK 1.5 버전 부터는 공변 반환형(convariant return type)이 도입되어 반환 값 자료형은 재정의 되는 메서드 반환 값 자료형의 하위 클래스가 될 수 있다. 
> 클라이언트에서 매번 형변환(cast)을 하는 것보다 메서드 반환시 형변환을 해주는 것이 훨씬 낫다. **라이브러리가 할 수 있는 일을 클라이언트에게 미루지 말자.**

#### 11.2.2 복제할 객체에 변경 가능 객체 참조 필드가 있을때
복제할 객체에 변경 가능 참조 필드가 있다면 참조 객체의 내부까지 복사해 주어야 객체가 손상되는 일이 발생하지 않는다.
```java
@Override public Stack clone(){
	try{
		Stack result = (Stack) super.clone();
		result.elements = elements.clone();
		return result;
	} catch ( CloneNotSupportException e){
		throw new AssertionError();
	}
}
```
하지만 복사할 객체가 final로 선언되어 수정이 불가능하거나 스레스 세이프하게 동작해야하는 상황 등 **문제가 발생할만한 상황들이 너무 많이 있다.**

### 11.3 객체를 복사할 대안을 제공하거나, 복제 기능을 제공하지 않는 것이 낫다
 Cloneable/clone 대신 복사 생성자, 복사 팩터리 메서드를 제공하면 clone 에서 발생할 수 있는 여러가지 문제를 피할 수 있다.
```java
public Yum(Yum yum);
public static Yum newInterface(Yum yum);
```
### 11.3 상속 대상 객체에는 Cloneable을 사용하지 말자
Cloneable은 상속되는 객체에서 많은 문제가 발생할 수 있기 때문에 Cloneable을 상속하는 인터페이스를 만들거나 상속을 목적으로 하는 Cloneable을 상속받은 클래스를 만들면 안된다.
