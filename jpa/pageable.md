# Pageable
![](img/jpa-repository.png)
- JpaRepository의 부모 인페이스인 PagingAndSortingRepository 에서 paging과 sorting 기능 제공


![](img/page-request.png)
- PageRequest 객체는 Pageable 인터페이스를 상속받음
- sort, offset, page 정보를 넘길 수 있음

## pageable 객체 생성
````
PageRequest pageable = PageRequest.of(page, size);
````
## 파라미터로 페이징 조회처리
````
public interface UserRepository extends JpaRepository<User, Long> {
    Page<User> findByAddress(String address, Pageable pageable);
}
````
## jpql로 페이징 처리
````
@Query("select u from User u where u.address = :address order by u.createTime desc") 
public interface UserRepository extends JpaRepository<User, Long> {
    Page<User> findByAddress(@Param("address) String address, Pageable pageable);
}
````
- 두번째 파라미터로 Pageable을 넘겨줌

## Paging 반환타입
1. Page<T>
- 일반적인 게시판 페이지 형태
- offset과 total page 수고 페이징 서비스 제공
- count 쿼리를 포함하 페이징으로 count 쿼리가 자동으로 생성됨
2. Slice<t>
- 더보기 형태의 페이징
- 추가 count 쿼리 없이 다음 페이지 조회 가능

### Page<T> 인터페이스 주요 메소드
- getTotalPages(): 총 페이지 수
- getTotalElements(): 전체 개수
- getNumber(): 현재 페이지 번호
- getSize(): 페이지 당 데이터 개수
- hasnext(): 다음 페이지 존재 여부
- isFirst(): 시작페이지 여부

### PageImpl 클래스는 Page<T> 인터페이스의 구현체
- 생성자의 첫번째 파라미터는 조회한 데이터 리스트, 두번째 파라미터는 pageable 객체, 세번째 파라미터는 데이터 총 개수