# Memory

## 메모리 계층
- 레지스터: CPU 안에 있는 작은 메모리, 휘발성, 속도 가장 빠름, 기억 용량이 가장 적음
- 캐시: L1, L2 캐시 지칭. 휘발성, 속도 빠름 기억 용량 적음
- 주기억장치: RAM. 휘발성, 속도 보통, 기억 용량 보통
- 보조기억장치: HDD, SDD. 휘발성, 속도 낮음, 기억 용량 많음

### 캐시
- 데이터를 미리 복사해 놓는 임시 저장소이자 빠른 장치와 느린 장치에서 속도 차이에 따른 병목 현상을 줄이기 위한 메모리

#### 캐시히트와 캐치미스
- 캐치히트: 캐시에서 원하는 데이터 찾음
- 캐시미스: 해당 데이터가 캐시에 없어서 주 메모리로 가서 데이터를 찾아오는 것

#### 캐시매핑
- 캐시가 히트되기 위해 매핑하는 방법
- 직접 매핑: 메모리가 1-100 있고 캐시가 1-10이 있다면 1:1-10, 2:1-20 이런식으로 매핑하는 것.
처리가 빠르지만 충돌 발생이 잦음
- 연관 매핑: 순서를 일치시키지 않고 관련 있는 캐시와 메모리를 매핑. 충돌이 적지만 모든 블록을 탐색애햐 해서 속도가 느림
- 집합 연관 매핑: 집접 매핑 + 연관 매핑. 순서는 일치시키지만 집합을 둬서 저장하며 블록화되어 있기 때문에 검색이 좀 더 효율적

#### 웹브라우저의 캐시
- 쿠키
  - 만료 기한이 있는 키-값 저장소
  - 4KB까지 저장
  - 클라이언트 또는 서버에서 만료기한 등을 정할 수 있는데 보통 서버에서 만료기한을 정함


- 로컬스토리지
  - 만료기한이 없는 키-값 저장소
  - 10KB까지 저장
  - 웹브라우저를 닫아도 유지되고 도메인 단위로 저장, 생성
  - HTML5를 지원하지 않는 웹 브라우저에서는 사용할 수 없으며 클라이언트에서만 수정 가능


- 세션 스토리지
  - 만료기한이 없는 키-값 저장소
  - 탭 단위로 세션 스토리지를 생성하며 탭을 닫을 때 해당 데이터가 삭제
  - 5MB까지 저장
  - HTML5를 지원하지 않는 웹 브라우저에서는 사용할 수 없으며 클라이언트에서만 수정 가능

- 데이터베이스 시스템을 구축할 때도 메인 데이터베이스 위에 레디스 데이터베이스 계층을 캐싱계층으로 둬서 성능을 향상시키기도 함


