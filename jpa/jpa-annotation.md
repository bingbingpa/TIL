# JPA annotation 정리

- @MappedSuperclass
  - 부모 클래스를 상속받는 자식 클래스에게 **매핑 정보만 제공**하고 싶을 때 사용한다.
  - 엔티티는 엔티티만 상속 받을 수 있기 때문에 엔티티가 아닌 클래스를 상속받기 위해서 @MappedSuperclass 를 사용한다.
- @AttributeOverride
  - 상속 받은 값중에 변경하고 싶은 값이 있을 경우에 사용한다.
  - ~~~ java
    @Entity
    @AttributeOverride(name = "createdAt", column = @Column(name = "publishedAt"))
    public class Book extends BaseEntity {
     ...
    }

    ~~~