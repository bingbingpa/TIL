# [우아콘2020] 수십억건에서 QUERYDSL 사용하기


## 1. 워밍업

- extends / implements 사용하지 않기: JpaQueryFactory 만 있으면 Querydsl 을 사용 할 수 있다.
- 동적쿼리는 BooleanExpression 을 사용하자. null 반환시 자동으로 조건절에서 제거 된다.

## 2. 성능개선 - Select

- Querydsl 의 exist 금지
  - 2500만건 기준으로 select exists 와 select count 조회 시간 비교
    - exists 가 약 2초 정도 더 빠르고, 찾고자 하는 대상이 일찍 조회 될수록 exists 가 더 빠르다. 
  - Querydsl 의 exists 는 실제로 count() > 0 으로 실행된다.
    - JPQL 은 from 없이는 쿼리를 생성할 수 없기 때문이다.
  - **limit 1로 조회 제한해서 exists 와 비슷한 성능을 낼 수 있다.**
- Cross Join 회피
  - cross join 은 나올 수 있는 모든 경우의 수를 대상으로 하기 때문에 성능이 좋을 수가 없다.
  - 묵시적 join 으로 cross join 이 발생 할 수 있다.(hibernate 이슈라서 spring data jpa 도 동일하게 발생한다.)
    - ~~~kotlin
      return queryFactory
              .selectFrom(customer)
              .where(customer.customerNo.gt(customer.shop.shopNo))
              .fetch()
      @Query("""
          SELECT c FROM Customer c
          WHERE c.customerNo > c.shop.shopNo
      """
      ~~~
  - **명시적 join 으로 inner join 이 발생하도록 하자.**
- Entity 보다는 Dto 를 우선
  - Entity 조회시 Hibernate 캐시, 불필요한 컬럼 조회 OneToOne N+1 쿼리등 단순 조회 기능에서는 성능 이슈 요소가 많다.
  - **Entity 조회: 실시간으로 Entity 변경이 필요한 경우**
  - **Dto 조회: 고강도 성능 개선 or 대량의 데이터 조회가 필요한 경우**
- 조회 칼럼 최소화하기: as 표현식으로 대체하면 select 에서 제외된다.
  - ~~~kotlin
    Expressions.asNumber(bookNo).as("bookNo")
    ~~~
- select 컬럼에 엔티티를 자제하자.
  - 예를 들어, AdBond 라는 클래스를 만들기 위해 customer 의 id가 필요하다고 해보자. 
  이때, adItem.customer 를 select 절에 포함한다면, customer 의 id 만을 필요로함에도 불구하고 customer 의 모든 정보를 조회하게 된다.
  - 연관된 Entity 의 save 를 위해서는 반대편 Entity 의 ID 만 있으면 된다.
  - 만약 distinct 까지 포함되어있다면 select 에 선언된 엔티티의 컬럼 전체가 distinct 의 대상이 된다. 
  즉, distinct 를 위한 임시 테이블 등을 만들기 위한 작업이 수행되기 때문에 많은 작업을 필요로 한다. 따라서 distinct 의 경우에는 꼭 필요한 컬럼만 조회해오자.
- group by 최적화
  - 인덱스를 타지 않게 되는 경우에는 FileSort 가 매번 발생한다. mySQL 에서는 order by null 을 사용하면 fileSort 가 제거되지만, 쿼리디엣셀에서는 order by null 문법을 지원하지 않는다.
  - **OrderByNull 을 직접 구현하자.**
  - null 을 그냥 넣게 되면 Querydsl 의 정렬을 담당하는 OrderSpecifier 에서 제대로 처리하지 못한다. Querydsl 에서는 공식적으로 null 에 대해 NullExpression.DEFAULT 클래스로 사용하길 권장하니 이를 활용한다.
    단, 페이징일 경우, order by null 을 사용하지 못하므로 페이징이 아닌 경우에만 사용해야 한다.
  - 조회 결과가 100건 이하라면 애플리케이션에서 정렬하는 것을 추천
- 커버링 인덱스
  - 쿼리를 충족시키는데 필요한 모든 컬럼을 갖고 있는 인덱스로 select / where / order by /group by 등에서 사용되는 모든 컬럼이 인덱스에 포함된 상태를 의미한다.
  - jpql 은 from 절의 서브쿼리를 지원하지 않는다.
  - 커버링 인덱스 조회는 나눠서 진행
    - cluster key(pk)를 커버링 인덱스로 빠르게 조회하고, 조회된 key 로 select 컬럼들을 후속 조회한다.

## 3. 성능개선 - Update/Insert

- 하이버네이트 캐시는 일괄 업데이트시 캐시 갱신이 안된다. 이럴 경우엔 업데이트 대상들에 대한 Cache Eviction 이 필요하다.
- DirtyChecking: 실시간 비즈니스 처리, 실시간 단건 처리 
- Querydsl.update: 대량의 데이터를 일괄로 Update 처리시
- **진짜 Entity 가 필요한게 아니라면 Querydsl 과 Dto 를 통해 딱 필요한 항목들만 조회하고 업데이트 하자!!!**
- JPA 로 Bulk Insert 는 자제하자.
  - rewriteBatchedStatements 으로 Insert 합치기 옵션을 넣어도, JPA 는 auto_increment 일때 Insert 합치기가 적용되지 않는다.
  - jdbcTemplate 을 사용하던지, Qclass 기반으로 Native SQL 을 사용하던지... 선택은 알아서~

## 4. 마무리

- 상황에 따라 ORM / 전통적 Query 방식을 골라 사용할 것 
- JPA / Querydsl 로 발생하는 쿼리 한번 더 확인하기