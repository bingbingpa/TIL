## spring boot SQL 보기 옵션 정리

- sql 보기: spring.jpa.properties.hibernate.show_sql=true
- sql 포맷팅 해서 보기: spring.jpa.properties.hibernate.format_sql=true
- sql 주석 추가: spring.jpa.properties.hibernate.use_sql_comments=true
- ? 에 어떤 값이 들어갔는지 확인하기: logging.level.org.hibernate.type.descriptor.sql=trace
- 하이버네이트 sql 보기: logging.level.org.hibernate.SQL:debug
- 통계 정보 보기: spring.jpa.properties.hibernate.generate_statistics=true