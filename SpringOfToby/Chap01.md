# 토비의 스프링

## Chap1

### 개방 폐쇄의 원칙(OCP)

클래스나 모듈은 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 한다

> 개방 폐쇄의 원칙은 객체지향 설계 원칙 SOLID의 하나로 SOLID는 아래 5가지 원칙의 첫머리를 따서 만들었다.
>
> - SRP(The Single Responsibility Priciple) : 단일 책임 원칙
> - OCP(The Open Closed Principle) : 개방 폐쇄 원칙
> - LSP(The Liskov Substitution Principle) : 리스코프 치환 원칙
> - ISP(The Interface Segregation Principle) : 인터페이스 분리 원칙
> - DIP(The Dependenty Inversion Principle) : 의존관계 역전 원칙

#### 높은 응집도와 낮은 결합도

##### 높은 응집도

- 하나의 모듈, 클래스가 하나의 책임 또는 관심사에만 집중되어 있다

##### 낮은 결합도

- 책임과 관심사가 다른 오브젝트 또는 모듈과 느슨하게 연결된 형태를 유지하는 것이 바람직하다.


### 제어의 역전(IoC)

프로그램의 제어 흐름 구조가 뒤바뀌는 것이라고 설명할 수 있다. 프로그램의 시작을 담당하는 main()과 같은 엔트리 포인트를 제외하면 다른 모든 오브젝트는 위임받은 권한을 받는 오브젝트에 의해 결정되고 만들어진다.

템플릿 메소드 패턴이나 프레임워크에서 제어의 역전을 확인할 수 있다.
 - 템플릿 메소드 패턴에서 상위 클래스의 함수를 하위 클래스에 작성할 때 우리는 이 함수가 언제 어떻게 호출될지 알 수 없다.
 - 프레임워크 또한 사용자가 작성한 어플리캐이션 코드가 프레임워크에 의해 수동적으로 동작한다. 

> 프레임워크와 라이브러이가 혼용되어 사용되는 경우가 빈번한데 IoC가 적용되지 않았다면 프레임워크라고 할 수 없다. 
>
> 라이브러리는 사용자가 작성한 코드 내에서 라이브러리의 특정 기능을 사용에 직접 제어하게 되어 IoC가 적용되지 않는다.



### 디자인 패턴

#### 전략 패턴

OCP에 가장 잘 들어맞는 패턴으로 자신의 Context에서 필요에 따라 변경이 필요한 로직을 인터페이스를 통해 통째로 외부로 분리시켜 구현하는 패턴

#### 템플릿 메소드 패턴

변하지 않는 기능은 슈퍼클래스에서 만들어두고 확장할 기능은 훅(hook) 메소드만들어 서브클래스에서 구현하도록 한다.

> hook 메소드 : 슈퍼클래스에서는 기본 기능을 지정하거나 비워두고 서브클래스에서 추상 메소드 구현이나 오버라이드를 통해 기능을 확장하도록 하는 메소드

#### 팩토리 메소드 패턴

슈퍼클래스에서 서브클래스가 구현할 메소드를 호출해 오브젝트를 얻은 후 해당 오브젝트를 사용하는 패턴이다. 서브클래스가 반환하는 오브젝트는 보통 인터페이스 타입으로 어떤 클래스가 반환될지는 알 수 없다.