이번 스터디에서는 우리가 흔히 사용하는 @Transactional 어노테이션에 관련해서 자세하게 기술한다. 우선 트랜잭션의 기본적인 개념부터 알아야 @Transactional을 더 잘 이해할 수 있으므로
데이터베이스상에서의 트랜잭션의 개념을 먼저 알아보자.

## 트랜잭션의 정의
***
데이터베이스 상에서 트랜잭션을 정의하는 바는 다음과 같다.

데이터베이스 트랜잭션은 데이터베이스 관리 시스템 또는 유사한 시스템에서 **상호작용의 단위**이다.
여기서 유사한 시스템이란 트랜잭션이 성공과 실패가 분명하고 상호 독립적이며,
일관되고 믿을 수 있는 시스템을 의미한다.

### 트랜잭션의 특징

트랜잭션의 특징은 크게 4가지로 나뉜다.

* **원자성 (Atomicity)**
* **일관성 (Consistency)**
* **독립성 (Isolation)**
* **지속성 (Durability)**

### 원자성

1. 트랜잭션의 연산은 데이터베이스에 모두 반영되던지 아니면 모두 반영되지 않아야한다.

2. 트랜잭션 내의 모든 명령은 반드시 완벽히 수행되어야 하며, 하나라도 오류가 발생하면 트랜잭션 전부가 취소되어야 한다.

### 일관성

1. 트랜잭션을 성공적으로 완료하면 일관성 있는 데이터베이스 상태로 변환한다.

2. 시스템이 가지고 있는 고정요소는 트랜잭션 수행 전과 트랜잭션 수행 완료 후의 상태가 같아야 한다.

### 독립성

1. 둘 이상의 트랜잭션이 동시에 실행되는 경우 다른 트랜잭션의 연산이 끼어들 수 없다.

2. 수행중인 트랜잭션은 완전히 완료될 때까지 다른 트랜잭션에서 수행 결과를 참조할 수 없다.

### 지속성

1. 성공적으로 완료된 트랜잭션의 결과는 영구적으로 반영되어야 한다.

### 트랜잭션의 연산 - 커밋(Commit)과 롤백(Rollback)

* **커밋** : 하나의 트랜잭션이 **성공적으로 끝났으며**, 데이터베이스가 일관성이 있는 상태로 유지될 때 트랜잭션이 마무리되었다는 것을 트랜잭션 관리자에게 알리기 위한 연산이다.
* **롤백** : 트랜잭션 처리가 **비정상적으로 종료된 경우**, 트랜잭션을 다시 시작하거나, 트랜잭션의 부분적으로 연산한 결과를 취소시킨다.

## 스프링에서 트랜잭션 적용 방법
***
스프링에서는 어노테이션 방식으로 트랜잭션 처리를 지원한다.
EJB가 제공했던 엔터프라이즈 서비스에서 가장 매력적인 것은 **선언적 트랜잭션**이다.

**@Transactional**을 선언하여 사용한다. 클래스 또는 메서드 위에 @Transactional을 붙이면,
트랜잭션 기능이 적용된 프록시 객체가 생성되며, 트랜잭션 성공 여부에 따라 **Commit** 또는 **Rollback** 작업이 이루어진다.

또 다른 방법으로는 **트랜잭션 스크립트** 방식인데,
하나의 트랜잭션 안에서 동작해야 하는 코드를 한 군데 모아서 만드는 방식이다.
이러한 방식 때문에 보통 트랜잭션마다 하나의 메서드로 구성된다.

메서드의 앞부분에서 DB를 연결하고 트랜잭션을 시작하는 코드가 필요하고, 이렇게 만들어진 트랜잭션 안에서 DB를 액세스하는 코드와 그 결과를 가지고 비즈니스 로직을 적용하는 코드가 뒤엉켜서 등장한다. 트랜잭션
스크립트 방식을 사용한다면 이런 코드들 때문에 어쩔 수 없는 중복 현상이 빈번하게 발생한다.

```java
public Result do(...){
        try{

            SomeTransaction tx=...;
            tx.begin();

            비즈니스 로직

            tx.commit();

        } catch(..) {
            tx.rollback();
            ...
        } finally{
            ...
            ...
        }
    }

```

## 트랜잭션 매니저의 종류
***
스프링 트랜잭션 추상화의 핵심 인터페이스는 **PlatformTransactionManager**다. 
모든 스프링의 트랜잭션 기능과 코드는 이 인터페이스를 통해서 로우레벨의 트랜잭션 서비스를 이용할 수 있다.
**PlatformTransactionManager** 인터페이스는 아래와 같다.

```java
package org.springframework.transaction;

import org.springframework.lang.Nullable;

public interface PlatformTransactionManager extends TransactionManager {

	TransactionStatus getTransaction(@Nullable TransactionDefinition definition)
			throws TransactionException;

	void commit(TransactionStatus status) throws TransactionException;
	void rollback(TransactionStatus status) throws TransactionException;

}
```
트랜잭션을 시작한다는 의미의 `getTransaction()`이 있다. `getTransaction()`은 트랜잭션 속성에 따라서 새로 시작하거나, 진행 중인 트랜잭션에 참여하거나, 진행 중인 트랜잭션을 무시하고 새로운
트랜잭션을 만드는 식으로 상황에 따라 다르게 동작한다.

`TransactionDefinition`은 트랜잭션의 속성을 나타내는 인터페이스다. `TransactionStatus`는 현재 참여하고 있는 트랜잭션의 ID와 구분정보를 담고 있다. 커밋 또는 롤백 시에 사용한다.

```java
package org.springframework.transaction;

import org.springframework.lang.Nullable;

public interface TransactionDefinition {

	int PROPAGATION_REQUIRED = 0;
	int PROPAGATION_SUPPORTS = 1;
	int PROPAGATION_MANDATORY = 2;
	int PROPAGATION_REQUIRES_NEW = 3;
	int PROPAGATION_NOT_SUPPORTED = 4;
	int PROPAGATION_NEVER = 5;
	int PROPAGATION_NESTED = 6;
	
	int ISOLATION_READ_UNCOMMITTED = 1;
	int ISOLATION_READ_COMMITTED = 2;
	int ISOLATION_REPEATABLE_READ = 4;
	int ISOLATION_SERIALIZABLE = 8;
	
    	int TIMEOUT_DEFAULT = -1;
    
	default int getPropagationBehavior() {
		return PROPAGATION_REQUIRED;
	}

	default int getIsolationLevel() {
		return ISOLATION_DEFAULT;
	}

	default int getTimeout() {
		return TIMEOUT_DEFAULT;
	}

	default boolean isReadOnly() {
		return false;
	}

	@Nullable
	default String getName() {
		return null;
	}

	static TransactionDefinition withDefaults() {
		return StaticTransactionDefinition.INSTANCE;
	}

}
```
선언적 트랜잭션 방식을 사용할 것이라면 `PlatformTransactionManager` 인터페이스의 사용 방법을 몰라도 상관없다. 인터페이스를 구현한 트랜잭션 서비스 클래스의 종류를 알고 있기만 하면 된다.

가끔 트랜잭션을 제어해서 테스트 하고 싶을 경우가 있을 수 있는데, 이 경우 `PlatformTransactionManager`를 직접 이용해야 할 수 있다.이제 스프링이 제공하는
`PlatformTransactionManager` 구현 클래스를 간단하게 알아보자.

## 트랜잭션 매니저의 종류
***
* **DataSourceTransactionManager** : Connection의 트랜잭션 API를 이용해서 트랜잭션을 관리해주는 트랜잭션 매니저
* **JpaTransactionManager** : JPA를 이용하는 DAO에는 JpaTransactionManager를 사용한다. DataSourceTransactionManager가 제공하는 DataSource 레벨의
트랜잭션 관리 기능을 동시에 제공한다.
* **JmsTransactionManager**, CciTransactionManager : 스프링에서는 DB 뿐만 아니라 트랜잭션이 지원되는 JMS와 CCI를 위해서도 트랜잭션 매니저를 제공한다.
* **JtaTransactionManager** : 하나 이상의 DB 또는 트랜잭션 리소스가 참여하는 글로벌 트랜잭션을 적용하려면 JTA를 이용해야 한다.

> **JMS** :  두 개 이상의 클라이언트 간에 메시지 통신을 위한 공통 API를 정의하는 자바 표준이다.
**CCI** : 스프링 프레임워크의 일반적인 리소스 관리와 트랜잭션 관리 기능을 사용하면서 일반적인 스프링 방식으로 CCI 커넥터에 접근하는 클래스를 제공한다.

## 트랜잭션 속성
***
모든 트랜잭션이 같은 방식으로 동작하는 것은 아니다. 작업 전체가 실패하거나 성공하는 하나의 작업으로 묶는다는 점에서는 다를 바 없겠지만, 세밀히 따져보면 몇 가지 차이점이 있다.

스프링은 트랜잭션의 경계를 설정할 때 여러개의 속성을 제공한다.

### propagation(트랜잭션 전파)
트랜잭션을 시작하거나, 기존 트랜잭션에 참여하는 방법을 결정하는 속성이다. 트랜잭션 경계의 시작 지점에서 트랜잭션 전파 속성을 참조해서 해당 범위의 트랜잭션을 어떤 식으로 진행시킬지 결정할 수 있다.

스프링이 지원하는 트랜잭션 전파 속성은 6개가 있으며, 모든 속성이 모든 종류의 트랜잭션 매니저와 데이터 액세스 기술에서 다 지원되진 않을 수 있으므로 주의하여 사용해야 한다.

### REQUIRED
Defualt 속성이며, 모든 트랜잭션 매니저가 지원한다. 보통 이 속성으로 많이 사용한다. 미리 시작된 트랜잭션이 있으면 참여하고, 없으면 트랜잭션을 생성한다. 간단한 트랜잭션 전파 방식이지만 사용해보면 매우
강력하고 유용한 속성이라는 사실을 알 수 있다.

### SUPPORTS
이미 시작된 트랜잭션이 있으면 참여하고 없으면 트랜잭션 없이 진행한다. 트랜잭션이 없긴 하지만 해당 경계 안에서 Connection이나 Hibernate Session 등을 공유할 수 있다.

### MANDATORY
REQUIRED와 비슷하며, 이미 시작된 트랜잭션이 있으면 참여한다. 하지만 트랜잭션이 없다면 생성하는 것이 아니라 예외를 발생시킨다. 혼자서 독립적으로 트랜잭션을 실행하면 안되는 경우에 사용한다.

### REQUIRES_NEW
항상 새로운 트랜잭션을 시작한다. 이미 진행 중인 트랜잭션이 있다면, 트랜잭션을 보류시킨다.

### NOT_SUPPORTED
트랜잭션을 사용하지 않게 한다. 이미 진행 중인 트랜잭션이 있으면 보류시킨다.

### NEVER
트랜잭션을 사용하지 않도록 강제한다. 이미 진행 중인 트랜잭션도 존재하면 안되며, 트랜잭션이 있다면 예외를 발생시킨다.

### NESTED
이미 진행 중인 트랜잭션이 있으면 중첩 트랜잭션을 시작한다. 중첩 트랜잭션은 말그대로 트랜잭션 안에 트랜잭션을 만드는 것이다.독립적인 트랜잭션을 만드는 REQUIRES_NEW와는 다르다.

중첩된 트랜잭션은 먼저 시작된 부모 트랜잭션의 커밋과 롤백에는 영향을 받지만, 자신의 커밋과 롤백은 부모 트랜잭션에게 영향을 주지 않는다.

> REQUIRES_NEW와 NESTED 차이
![](../../../../../../../var/folders/kr/3t9_nxz92lxgwr9c89_7bz9m0000gn/T/TemporaryItems/NSIRD_screencaptureui_TpJbFD/스크린샷 2023-01-15 08.42.41.png)

예를 들면, 로그를 찍는 기능은 부가 기능이다. 만약 부가 기능에 문제가 생겨서 트랜잭션을 롤백해야하는 상황이다. 로그를 찍는 기능에 문제가 생겼다고 해서, 핵심 기능에 영향을 끼치면 안된다.

따라서 부가 기능의 트랜잭션은 부모 트랜잭션 안에 영속되지만, 부모 트랜잭션에 영향을 줄 수 없다.

## isolation(트랜잭션 격리수준)
***
트랜잭션 격리수준은 동시에 여러 트랜잭션이 진행될 때에 트랜잭션의 작업 결과를 다른 트랜잭션에게 어떻게 노출할 것인지를 결정하는 기준이다. 스프링은 다음 다섯 가지 격리수준 속성을 지원한다.

### DEFAULT
사용하는 데이터 액세스 기술 또는 DB 드라이버의 디폴트 설정을 따른다. 보통 드라이버의 격리수준은 DB의 격리수준을 따르는게 일반적이다. 대부분의 DB는 READ_COMMITTED를 기본 격리수준으로 설정하고
있다.

### READ_UNCOMMITTED
가장 낮은 격리수준이다. 하나의 트랜잭션이 커밋되기 전에 그 변화가 다른 트랜잭션에 그대로 노출되는 문제가 있다. 격리수준과 속도는 반비례하기 때문에 속도는 가장 빠르다.

### READ_COMMITED
실제로 가장 많이 사용되는 격리수준이다. 만약 이 격리수준을 사용하고 싶다면 DEFAULT로 설정해둬도 될 것이다. READ_UNCOMMITTED와 달리 다른 트랜잭션이 커밋하지 않은 정보는 읽을 수 없다.

하지만 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 있다. 때문에 처음 트랜잭션이 같은 로우를 다시 읽을 경우 다른 내용이 조회될 수 있다.

### REPEATABLE_READ
하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정하는 것을 막아준다. 하지만 새로운 로우를 추가하는 것은 제한하지 않는다. 예를 들어 데이터 1, 2, 3이 있을 때 이를 수정하는 것은 허용하지 않지만, 4, 5,
6을 추가하는 것은 허용하기에 이 또한 데이터 일관성을 해칠 수 있다.

그래도 READ_COMMITED보다는 격리 수준이 높은 만큼 덜 일어나는 현상이다.

### SERIALIZABLE
가장 강력한 트랜잭션 격리수준이다. 이름 그대로 트랜잭션을 순차적으로 진행시켜주기 때문에 여러 트랜잭션이 동시에 같은 테이블의 정보를 액세스하지 못한다. 가장 안전한 격리수준이지만 가장 성능이 떨어지기 때문에
극단적으로 안전한 작업이 필요한 경우가 아니라면 자주 사용되지 않는다.

필자가 생각하기에 이정도 격리수준을 사용할 시스템은 정산 시스템 정도가 되지 않을까 생각해본다.

## timeout(트랜잭션 제한시간)
***
timeout 속성을 사용하면 트랜잭션에 제한시간을 지정할 수 있다. 단위는 **초(second)** 단위로 지정한다. default는 트랜잭션 시스템의 제한시간을 따른다.

## readOnly(읽기전용 트랜잭션)
***
트랜잭션을 읽기전용으로 설정할 수 있다. 성능을 최적화하기 위해 사용할 수도 있고, 특정 트랜잭션 작업 안에서 쓰기 작업이 일어나는 것을 의도적으로 방지하기 위해 사용할 수도 있다.

> 트랜잭션을 읽기전용으로 설정하면 왜 성능이 최적화 될까?
변경감지를 위한 **스냅샷**을 유지하지 않아도 되고, 영속성 컨텍스트를 **Flush**하지 않아도 되기 때문이다.

aop/tx 스키마로 트랜잭션 선언 시 이름 패턴을 이용해 읽기전용 속성으로 만드는 경우가 많다. 보통 get 또는 find 같은 이름의 메서드를 읽기 전용으로 만들어서 사용하면 편하다.

선언적 트랜잭션의 경우 각 어노테이션마다 일일이 읽기전용을 지정해줘야 한다.

## rollbackFor(트랜잭션 롤백 예외)
***
선언적 트랜잭션에서는 런타임 예외가 발생하면 롤백한다. 반면에 예외가 전혀 발생하지 않거나 체크 예외가 발생하면 커밋한다. 체크 예외를 커밋 대상으로 삼은 이유는 체크 예외가 예외적인 상황에서 사용되기보다는 리턴
값을 대신해서 비즈니스적인 의미를 담은 결과를 돌려주는 용도로 많이 사용되기 때문이다.

```java
@Transactional(propagation = Propagation.NESTED, rollbackFor = NotFoundAuthIdException.class)
public void save(String message, String token) {
    String authority = jwtUtil.extractAuthorityFromToken(token);
    ...
}
```

## noRollbackFor(트랜잭션 커밋 예외)
***
rollbackFor 속성과는 반대로 기본적으로 롤백 대상인 런타임 예외를 트랜잭션 커밋 대상으로 지정한다. 사용 방법은 위와 동일하다.

## 트랜잭션 정리
***
이렇게 트랜잭션 속성은 총 6가지인데, 모든 트랜잭션 경계설정 속성에 사용할 수 있다. 하지만 모든 트랜잭션마다 일일이 트랜잭션 속성을 지정하는 건 귀찮고 불편하다.

세밀하게 튜닝해야 하는 시스템이 아니라면 메서드 이름 패턴을 이용해서 트랜잭션 속성을 한 번에 지정하는 **aop/tx** 스키마 방식이 편하다. 보통은 readOnly 속성 정도만 사용하고 나머지는 default로
지정하는 경우가 많다. 세밀한 속성은 **DB**나 **WAS**의 트랜잭션 매니저의 설정을 이용해도 되기 때문이다.

이번 포스팅에서는 데이터베이스 상에서 트랜잭션의 개념과, 스프링에서 트랜잭션의 원리 및 사용 방법에 대해 알아보았다.