# [JPA] 임베디드 타입이란?(@Embeddable)

Shopping mall Project를 진행하면서 알게된 **임베디드 타입**을 정리해보려 한다.  
임베디드 타입은 엔티티 안에서 **중복된 필드를 제거하고 재사용성을 높이기 위해** 별도의 클래스로 분리해서 쓰는 방식이다.

임베디드 타입을 쓰면 공통 필드를 새로운 클래스(@Embeddable)로 빼서 여러 엔티티에서 공유할 수 있고,  
유지보수 관점에서도 주소 정보가 바뀌면 그 클래스만 수정하면 되니까 관리가 훨씬 쉬워진다


## 임베디드 타입 사용 예시

###  User Entity
@Entity
public class User {
 
    @Id
    @GeneratedValue
    private Long id;
 
    private String username;
    private String city;
    private String street;
    private String zipcode;
}

여기서 city, street, zipcode는 사용자의 주소에 해당한다.
해당 필드들을 Address라는 클래스 타입으로 정의하고 임베디드 타입을 선언하여
4개의 필드를  User Entity를 각각 다 적을 필요 없이 클래스 필드 하나로 작성할 수 있다.

@Embeddable
public class Address {
 
    private String city;
    private String street;
    private String zipcode;
    
    public Address() {} //기본 생성자는 필수
    ...
}

@Entity
public class User {
 
    @Id
    @GeneratedValue
    private Long id;
 
    private String username;
    
    @Embedded
    private Address address;
    ...
}

새로 만든 Address 클래스에 @Embeddable을 붙이고,
해당 클래스를 쓰는 곳(User 엔티티)에는 @Embedded를 붙여주면 끝! 아주 편하고 향후 유지보수도 편함.
User 엔티티를 저장할 때 DB에 Address 필드(city, street, zipcode)가 같은 테이블 컬럼으로 함께 들어간다.

## 임베디드 타입  주의사항 
- 기본 생성자 필수!
- 불변적이어야한다. -> 생성자 초기화, setter를 통한 값 변경은 지양!

