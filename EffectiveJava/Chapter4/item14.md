## 14 public 클래스 안에는 public 필드를 두지말고 접근자 메서드를 사용하라
객체 지향에서 아래 같은 코드는 재앙과 같다.
```java
class Point{
	public int x;
	public int y;
}
```
각 필드의 접근자 메서드를 두고 접근자 메서드를 통해 접근하도록 수정해야 한다.
```java
class Point{
	private int x;
	private int y;
	
	...
	
	public int getX(){
		return x;
	}
	public void setX(int x){
		this.x = x;
	}
	
	...
}
```
클래스가 public이라면 메서드 또한 public으로 만들어 외부에서 접근할 수 있게 해야한다.
하지만 **pakage-private 클래스나 private 중첩 클래스**는 데이터 필드를 공개하더라고 잘못이라고 할 수 없다.
시각적으로 깔끔해 보이기도 하고 클라이언트의 코드가 클래스 표현에 종속되는 문제가 있긴 하지만 클라이언트 코드 또한 같은 패키지 내부에 있기 때문에 영향이 크지 않다.

**public 클래스의 변경 불가능 필드**는 외부로 공개해도 영향이 덜하지만 API를 수정하지 않고 내부 표현을 변경할 수 없고 필드에 접근할 때 추가 동작을 정의할 수 도 없어 권장되지는 않는다.