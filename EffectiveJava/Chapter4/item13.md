## 13 클래스와 멤버의 접근 권한은 최소화 하라
### 13.1 정보 은닉(캡슐화)
모듈 사이의 의존성을 낮춰서 프로파일링과 최적화에 용이하다.

### 13.2 각 클래스와 멤버는 가능한 한 접근 불가능하도록 만들어라
public 으로 선언된 클래스는 API의 일부로 호환성 보장을 위해 꾸준히 관리해줘야 하지만 pakage-private로 선언하면 API의 일부가 아니라 구현 세부사항에 속하게 되므로 자유롭게 변경할 수 있다.

### 13.3 접근권한
필드, 메서드, 중첩 클래스, 중첩 인터페이스 같은 멤버의 접근 권한은 아래 네 개 중 하나로 설정할 수 있다.

> **Private**
> 선언된 최상위 레벨 클래스 내부에서만 접근 가능하다.

> **package-private (기본 접근 권한)**
> 같은 패키지 내의 아무 클래스나 사용할 수 있다. 멤버를 선언할 때 아무런 접근 권한 수정자를 붙이지 않으면 이 권한이 주어진다.

> **protected**
> 선언된 클래스 및 그 하위 클래스만 사용할 수 있다. 선언된 클래스와 같은 패키지에 있는 클래스에서도 사용이 가능하다.

> **public**
> 어디에서나 사용이 가능하다.

### 13.4 객체 필드(instance field)는 절대로 public으로 선언하면 안 된다.
변경 가능 객체를 참조하는 final 필드는 참조 자체는 변경할 수 없지만 참조 대상 객체는 변경할 수 있으므로 문제가 발생할 수 있다.

예를들어 public static final배열 필드를 두거나 배열 필드를 반환하는 접근자(accessor)를 두면 클라이언트가 배열 내용을 변경할 수 있게되므로, 보안에 문제가 생긴다.
 
```java
public static final Thing[] VALUES = {...}
```

문제의 해결방법은 두 가지가 있는데 두 가지 방법중 클라이언트의 요구사항과 요구 성능에 더 부합하는 방법을 선택하면 된다. </br>첫 번째는 public으로 선언됐던 배열을 private로 변경하고 변경이 불가능한 public list를 만드는 것이다.

```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final List<Thing> VALUES = 
	Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```
두 번째는 배열은 private로 선언하고, 해당 배열을 복사해서 반환하는 public 메서드를 추가하는 것이다.
```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final Thing[] values(){
	return PRIVATE_VALUES.clone();
}
```
