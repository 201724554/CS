* **Modeling**
  * UML class diagram 
    * Class
      * 데이터 객체를 표현
    * Relationships
      * 데이터 객체 간의 연관관계를 표현
      * 연관관계의 이름
      * 1:1, 1:N, N:M 관계인지 cardinality 정보
      * Association, Aggregation, Composition 표현
    * Constraints
      * 데이터 객체가 만족해야하는 조건
      * Key constraints - 모든 class는 최소 하나의 unique한 key를 가져야함
      * Referential integrity - 다른 class의 attribute를 foreign key로 참조하는 경우, 해당 attribute는 반드시 primary key이어야 함
  * Weak entity set
    * 자신의 attribute만으로는 식별할 수 없어서 다른 class의 primary key를 이용해 자신을 식별하는 class
* **Transaction**
  * db에서 실행되는 작업의 기본 단위
  * ACID
    * atomicity, consistency, isolation, durability
* **SQL**
  * DDL
    * Data Definition Language
    * 스키마를 정의하거나 조작하는 명령어
    * CREATE, DROP, ALTER, RENAME, COMMENT, TRUNCATE
  * DML
    * Data Manipulation Language
    * 데이터를 조작하는 명령어
    * SELECT, UPDATE, DELETE, INSERT...
  * DCL
    * Data Control Language
    * 데이터를 제어하는 명령어
    * COMMIT, ROLLBACK, GRANT, REVOKE
    * 일부 경우, COMMIT, ROLLBACK을 TCL(Transaction Control Language)로 정의해 구분하기도 함
  * JOIN
    * JOIN 기법
      * nested loop join
        * 이중 루프를 이용한 join
        * 레코드 관점에서는 join 순서가 상관없지만, 블록 관점에서는 순서에 따라 성능이 달라짐
        * 파일 A에 N(A)개 레코드에 B(A)개 블록, 파일 B는 N(B)개 레코드에 B(B)개 블록 가정
          * A join B의 경우, N(A) * B(B) + B(A)번의 disk access 발생
            * A의 각 레코드와 B의 모든 레코드를 대조함, 즉 처음 기준이 되는 A의 레코드를 가져오므로 N(A)번의 disk access
            * 이후 A의 각 레코드를 B의 모든 레코드를 대조하므로, A의 레코드 하나 당 B의 모든 레코드를 가져와야함 - N(A)*B(B)번 disk access
          * 반대의 경우, N(B) * B(A) + B(B)번 disk access
      * block nested loop join
        * 레코드 별로 블록과 대조하지 않고 블록 별로 블록과 대조함
        * A join B의 경우, A의 블록을 가져오고 B의 블록을 가져온 후, 각 블록의 레코드끼리 대조함
        * 따라서 B(A) * B(B) + B(A)번의 disk access
        * B join A는 B(A) * B(B) + B(B)번의 disk access
      * indexed nested loop join
        * 인덱스를 이용한 join
        * A join B의 경우, 과정은 다음과 같음
          * A의 블록을 가져옴
          * 가져온 블록에서 각 레코드 별로 join할 때 사용할 attribute의 index를 이용해 B에서 레코드를 가져옴
            * ex. A join B on city인 경우, A의 한 레코드의 city가 Seoul인 경우, city를 이용해 만들어진 B의 index를 이용해 빠르게 B에서 city = Seoul인 레코드를 가져올 수 있음
          * B(A) + N(A) * B에서 index를 이용해 레코드를 가져오는 비용
          * 이때 b+tree를 이용하고 블록의 크기가 4KB, 포인터 타입의 크기를 4Byte라고 가정하면 1024^2개의 데이터가 있을 때 depth가 1로 block nested join보다 disk access가 월등히 적음
    * JOIN 종류
      * **todo: inner join, outer join, right join, left join**
* **Index**
  * clustered index
    * primary key로 지정한 attribute를 기준으로 생성되는 index
    * 실제 물리적 레코드의 정렬에 영향을 미침
    * 테이블 당 한개씩 존재
    * 인덱스의 leaf node는 실제 레코드를 가르키고 있음(정확히는 파일의 slotted page의 레코드 정보)
    * 검색 성능엔 좋지만 삽입, 수정, 제거 시 오버헤드가 큼
  * non clustered index
    * 직접 생성하는 index
    * 실제 물리적 레코드의 정렬에 영항 x
    * 테이블 당 여러개 존재 가능
    * 인덱스의 leaf node는 실제 레코드를 가르키지 않고, 해당 레코드의 primary key를 가르켜 non clustered index를 타고 primary key를 찾아 clustered index를 타는 방식
    * clustered index에 비해 한번 더 인덱스를 타기 때문에 검색 성능은 상대적으로 좋지 않지만 삽입 등의 작업은 더 빠름
  * dense index
    * 모든 레코드를 포함하는 인덱스
    * 모든 레코드를 포함하기 때문에 탐색 성능이 좋음
    * 모든 레코드를 포함하기 때문에 인덱스 자체가 차지하는 용량이 큼
  * sparse index
    * 모든 레코드를 포함하지 않고, 몇몇 레코드만 포함
    * 탐색 성능 상대적으로 나쁨
    * 적은 용량 차지
  * 주로 B+tree, R-tree, Hash 등 자료구조를 이용
* **Isolation level**
  * read uncommitted
    * 제일 단순함
    * 다른 트랜잭션에서 커밋하지 않은 데이터까지 모두 읽음
    * dirty read
      * 트랜잭션 A에서 한 데이터를 업데이트 함
      * 근소한 차이로 트랜잭션 B에서 해당 데이터를 읽음
      * 이후 트랜잭션 A에서 문제가 생겨 해당 데이터를 롤백함
      * 하지만 트랜잭션 B는 읽은 데이터가 롤백된 것을 모르고 유효하지 않은 데이터로 트랜잭션을 마무리함
  * read committed
    * 다른 트랜잭션에서 commit 하지 않은 상태는 읽지 않음
    * dirty read는 발생하지 않음
    * non repeatable read 문제 발생
      * 트랜잭션 A에서 한 데이터를 읽음
      * 근소한 차이로 트랜잭션 B에서 해당 데이터를 업데이트하고 커밋함
      * 트랜잭션 A에서 다시 해당 데이터를 읽음
      * 커밋한 결과를 읽기 때문에 읽은 데이터가 이전의 결과와 다르게 됨
  * repeatable read
    * Multi Version Concurrency Control(MVCC) 이용
      * 여러개의 트랜잭션이 락을 걸지 않고 같은 데이터에 동시에 접근할 수 있게 하는 기술
      * **todo: search for details**
    * phantom read
      * **todo: search for details**
      * 사실상 MVCC를 이용해 거의 문제가 발생하지 않음
  * serializable
    * 한 번에 하나의 트랜잭션만 수행 가능
    * 성능 상 거의 사용되지 않음
    * deadlock 확률 높음
* **todo: nosql, S-lock, X-lock**