---
layout: post
title:  "싱글톤 패턴"
date:   2021-11-17 13:33:03 +0900
categories: jekyll update
---
- 목표 : 싱글톤 패턴에 대해서 공부하여 자료를 만들고 스터디 그룹에서 발표한다.

1. Singleton 의 사전적 의미
	- (단독) 개체, 독신자(결혼을 안 했거나 애인이 없는 사람) , (쌍둥이가 아닌) 외둥이[단일아]
2. 싱글톤 패턴 정의.
	- 클래스의 인스턴스를 하나만 생성하고, 어디서든 그 인스턴스를 참조할 수 있도록 하는 패턴.
	- 여러 컴퓨터에서 프린터 한 대를 공유하는 경우, 한 대의 컴퓨터에서 프린트하고 있을 때 다른 컴퓨터가 프린트 명령을 내려도 현재 프린트하는 작업을 마치고 그다음 프린트를 해야지 두 작업이 섞여 나오면 문제가 될 것이다. 즉 여러 클라이언트(컴퓨터)가 동일 객체(공유 프린터)를 사용하지만 한 개의 객체(프린트 명령을 받은 출력물)가 유일하도록 상위 객체가 보장하지 못한다면 singleton 패턴을 적용해야 한다. 이처럼 동일한 자원이나 데이터를 처리하는 객체가 불필요하게 여러 개 만들어질 필요가 없는 경우에 주로 사용한다.


3. 싱글턴 패턴을 쓰는 이유?
	- 고정된 메모리 영역을 가지고 하나의 인스턴스만 사용하기 때문에 메모리 낭비 방지
	- 싱글턴 클래스의 인스턴스는 전역이기 때문에 다른 클래스의 인스턴스들이 데이터를 공유하기 쉬움
	- DBCP(DataBase Connection Pool) 처럼 공통된 객체를 여러 개 생성해야 하는 상황에 많이 사용

4. 싱글턴 구현
	- Eager initializaion
		```java
			public class EagerSington {
				private static EagerSington instance = new EagerSington();

				private EagerSington() {
				}

				public static EagerSington getInstance(){
					return instance;
				}
			}
		```
		- 클라이언트에서 사용하지 않더라도 인스턴스가 항상 생성됨
		- 예외 처리를 할 수 있는 방법이 없음
	- Static block initializaion
		```java
			public class StaticBlockSingleton {
				private static StaticBlockSingleton instace;

				static {
					try {
						instace = new StaticBlockSingleton();
					} catch (Exception e) {
						throw new RuntimeException("싱글톤 객체 생성 오류");
					}
				}
				public static StaticBlockSingleton getInstance(){
					return instace;
				}
			}
		```
		- Eager initializaion과 유사, 인스턴스가  static block 내에서 생성됨.
		- 예외 처리 가능
		- 처음  시작할 때 초기화 되어 메모리 낭비 유발.
	- Lazy initializaion
		```java
			import java.util.Objects;

			public class LazyInitializaitonSingletion {
				private static LazyInitializaitonSingletion instace;

				private LazyInitializaitonSingletion() {}

				public static LazyInitializaitonSingletion getInstance() {
					if(Objects.isNull(instace)) {
						instace = new LazyInitializaitonSingletion();
					}
					return instace;
				}
			}
		```
		- getInctace() 호출 이외에는 인스턴스를 생성하지 않음.
		- Eager initializaion의 단점을 보완
		- Thread safety 하지 않음.
	- Tread safe initializaion
		```java
			public class ThreadSafeSington {
				private static ThreadSafeSington instance;

				private ThreadSafeSington() {}

				public static synchronized ThreadSafeSington getInstance(){
					if(instance == null) {
						instance = new ThreadSafeSington();
					}
					return instance;
				}
			}
		```
		- synchronized를 이용해서 하나의 스레드만 접근 가능하도록 설정
		- 성능 저하를 야기하는 비효율적인 방법(느리다.)
	- Double-Checked Locking
		```java
			private static DoubleCheckedLockingSingleton getInstance() {
				if (Objects.isNull(instace)) {
					synchronized (DoubleCheckedLockingSingleton.class) {
						if (Objects.isNull(instace)) {
							instace = new DoubleCheckedLockingSingleton();
						}
					}
				}
				return instace;
			}
		```
		- Null 체크를 synchronized 블록 밖에서 한번, 안에서 한번 총 두 번 실행
		- 밖에서 하는 체크는 인스턴스가 있는 경우 빠르게 리턴하기 위해서 안쪽에서 하는 체크는 인스턴스가 생성되지 않은 경우 하나의 인스턴스만 생성하기 위해.
	- Bill Pugh Solution
		```java
			public class BillPughSingleton {
				private BillPughSingleton() {
				}

				private static class SingletonHelper {
					private static final BillPughSingleton instace = new BillPughSingleton();
				}

				public static BillPughSingleton getInstance() {
					return SingletonHelper.instace;
				}
			}
		```
		- Double Checked에 비해 구현이 간단.
		- Lazy Loding이 가능
		- Thread safety

- Thread safety : 멀티 스레드 프로그래밍에서 일반적으로 어떤 함수나 변수, 혹은 객체가 여러 스레드로부터 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없음을 뜻한다. 보다 엄밀하게는 하나의 함수가 한 스레드로부터 호출되어 실행 중일 때, 다른 스레드가 그 함수를 호출하여 동시에 함께 실행되더라도 각 스레드에서의 함수의 수행 결과가 올바로 나오는 것으로 정의한다.