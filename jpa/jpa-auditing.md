# JPA Auditing

- audit 는 *감시하다, 감사하다* 라는 뜻으로 Spring Data JPA 에서 시간에 대해서 자동으로 값을 넣어주는 기능이다.
- ~~~ java
  @MappedSuperclass
  @EntityListeners(AuditingEntityListener.class)
  public abstract class BaseTimeEntity {
  
      // Entity가 생성되어 저장될 때 시간이 자동 저장
      @CreatedDate
      private LocalDateTime createdDate;
  
      // 조회한 Entity 값을 변경할 때 시간이 자동 저장
      @LastModifiedDate
      private LocalDateTime modifiedDate;
  
  }
  ~~~
- @EntityListeners(AuditingEntityListener.class): 해당 클래스에 Auditing 기능을 포함
- @CreatedDate: Entity 가 생성되어 저장될 때 시간이 자동 저장
- @LastModifiedDate: 조회한 Entity 의 값을 변경할 때 시간이 자동 저장
- @EnableJpaAuditing: JPA Auditing 을 활성화 하는 어노테이션으로 부트의 실행 클래스 또는 별도의 Config 클래스에 추가한다.