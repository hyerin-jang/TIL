# Kafka

## 구성
### Event
- kafka에서 producer와 consumer가 데이터를 주고받는 단위
### Producer
- kafka에 이벤트를 게시(post)하는 클라이언트 어플리케이션
### Consumer
- toplic을 구독하고 이로부터 얻어낸 이벤트를 처리하는 클라이언트 어플리케이션
### Topic
- 이벤트가 쓰이는 곳
- producer는 topic에 이벤트를 게시
- consumer는 topic으로부터 이벤트를 가져와 처리
- topic은 파일시스템의 폴더와 유사, 이벤트는 폴더안 파일과 유사
- topic에 저장된 이벤트는 필요한 만큼 다시 읽을 수 있음
#### Partition
- topic은 여러 bocker에 분산되어 저장되며 이렇게 분산된 topic을 partition 이라 함
- 어떤 이벤트가 partition에 저장될지는 이벤트의 key에 의해 정해지며 같은 키를 가지는 이벤트는 항상 같은 partition에 저장
- kafka는 topic의 partition에 저장된 consumer가 항상 정확히 동일한 순서로 partition의 이벤트를 읽을 것을 보장

## 주요 개념
### Producer와 Consumer의 분리
- kafka의 producer와 consumer는 완전 별개로 동작
- producer는 broker의 topic에 메세지를 게시하기만 하면 되며 consumer는 broker의 특정 topic에서 메세지를 가져와 처리하기만 하면 됨
- 이 덕분에 kafka는 높은 확장성 제공
- producer 또는 consumer를 필요에 의해 스케일 인/아웃 하기에 용이한 구조

### Push와 Pull 모델
- kafka의 consumer는 pull 모델을 기반으로 메세지 처리를 진행
- broker가 consumer에게 메세지를 전달하는 것이 아닌 consumer가 필요로 할 때 broker로 부터 메세지를 가져와 처리하는 형태
#### 장점
- 다양한 소비의 처리 형태와 속도를 고려하지 않아도 됨
  - push 모델의 경우, broker가 데이터 전송 속도를 제어하기 때문에 다양한 메세지 스트림의 소비자를 다루기 어렵지만 pull 모델은 consumer가 처리 가능한 때에 메세지를 가져와 처리하기 때문에 다양한 소비자를 다루기 쉬움
- 불필요한 지연없이 일괄처리를 통해 성능향상 도모
  - push 모델의 경우 요청을 즉시 보내거나 더 많은 메세지를 한번에 처리하도록 하기 위해 buffering을 할 수 있음
  - 이런 경우, consumer가 현재 메세지를 처리할 수 있음에도 대기를 해야 함
  - 그렇다고 전송 지연시간을 최소로 변경하면 한번에 하나의 메세지만 보내도록 하는 것과 같으므로 비효율적
  - 