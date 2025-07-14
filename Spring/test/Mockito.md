
현재 쇼핑몰 시스템을 개발하는 과정에서 기능별 단위 테스트를 진행하던 중 Mockito를 정확히 이해하지 못한 채 사용하는 것 같아, 아래와 같이 한 번에 정리하였다.

# Mockito란?
**Mockito**는 Java에서 널리 사용되는 **단위 테스트용 Mocking 프레임워크**다.

## Mocking이란?
실제 구현 객체를 흉내 내는 **가짜 객체(mock)** 를 만들어 테스트를 수행하는 기법이다.  
데이터베이스, Redis, 외부 API, Service 등을 실제로 호출하지 않고 모방해 테스트에서 제외시킨다.

## 왜 Mockito를 써야 하는가?
1. **단위 테스트 분리** – 전체 사이클(Controller → Service → Repository)을 모두 검증하지 않고 특정 부분만 검증할 수 있다.  
2. **테스트 속도 향상** – 실제 DB나 네트워크를 거치지 않기 때문에 테스트가 빠르다.  
3. **독립성 확보** – Spring 같은 프레임워크에 의존하지 않아 항상 동일한 테스트 환경을 유지한다.  
4. **예외 시나리오 테스트** – 현실에서 만나기 어려운 예외 상황을 직접 설정하여 로직을 촘촘히 검증할 수 있다.

# Mockito의 핵심 구성요소
- `@ExtendWith(MockitoExtension.class)` : JUnit 5 환경에서 `@Mock`, `@Spy`, `@InjectMocks` 등이 자동으로 동작하도록 설정하는 어노테이션
- `mock()` : 실제 객체를 모방한 가짜 객체 생성
- `@Mock` : Mockito가 자동으로 해당 타입의 Mock 객체 생성
- `@InjectMocks` : `@Mock`으로 만든 객체들을 테스트 대상 객체에 의존성 주입
- `when().thenReturn()` : 특정 메서드 호출 시 원하는 값을 반환하도록 시나리오 정의
- `verify()` : 특정 메서드가 호출되었는지 검증
- `@Spy` : 실제 객체를 Wrapping 하여 필요한 부분만 Mocking

# Mockito 사용법

## 1. Mock 객체 생성
```java
List<String> mockedList = mock(List.class);
when(mockedList.get(0)).thenReturn("Hello");
```

## 2. verify()로 메서드 호출 검증
```java
mockedList.add("Hello");
verify(mockedList).add("Hello");
```

## 3. 예외 시나리오 설정
```java
when(mockedList.get(0)).thenThrow(new NullPointerException("Null 예외 발생"));
mockedList.get(0); // 호출 시 예외 발생
```

## 4. @Mock과 @InjectMocks 사용
```java
@InjectMocks
private UserService userService;

@Mock
private UserDataRepository userDataRepository;
```

## 5. 다양한 반환 값 설정
```java
when(mockedList.get(anyInt()))
    .thenReturn("first", "second", "third");

mockedList.get(0); // first
mockedList.get(0); // second
mockedList.get(0); // third
```

# Mock과 Spy의 차이

## Mock
- 완전한 가짜 객체 (기능이 없음)
- 메서드 호출 시 아무 동작도 하지 않음
- 기본값(0, null, false 등) 반환
- 반드시 `when().thenReturn()` 등으로 동작을 지정해야 함

```java
@Mock
List<String> mockList;

@Test
void mockTest() {
    mockList.add("A");
    System.out.println(mockList.size()); // 0
    when(mockList.size()).thenReturn(100);
    System.out.println(mockList.size()); // 100
}
```

## Spy
- 실제 객체를 감싼 가짜 객체
- 메서드 호출 시 실제 메서드가 동작함
- `doReturn().when()` 등을 통해 일부 동작만 오버라이드 가능

```java
@Spy
List<String> spyList = new ArrayList<>();

@Test
void spyTest() {
    spyList.add("A");
    System.out.println(spyList.size()); // 1
    doReturn(100).when(spyList).size();
    System.out.println(spyList.size()); // 100
}
```

## 정리

| 구분 | Mock | Spy |
|------|------|-----|
| 객체 생성 | 완전한 가짜 객체 | 진짜 객체를 감싸는 부분 가짜 |
| 메서드 기본 동작 | 아무것도 안 함 | 실제 메서드 실행 |
| 커스터마이징 | `when().thenReturn()` | `doReturn().when()` |
| 사용 목적 | 외부 의존성 제거 | 내부 일부 로직만 조작 |

---

> 이 문서는 Mockito의 기본 개념과 실용적인 사용법에 대한 내용을 단일 텍스트 파일로 정리한 것입니다.
