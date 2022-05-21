# CI/CD
##CI (Continuous integration)
- 개발자를 위한 자동화 프로세스인 지속적인 통합(Continuous Integration)을 의미
- CI를 성공적으로 구현할 경우 애플리케이션에 대한 새로운 코드 변경 사항이 정기적으로 빌드 및 테스트되서 공유 레파지토리에 통합되므로 
여러 명의 개발자가 동시에 애프리케이션 개발과 관련된 코드 작업을 할 경우 충돌하는 문제를 해결할 수 있음
- code 작성 -> Build -> Test를 짧은 주기로 자동화 하는것
    

## CD (Continuous Delivery) 또는 (Continuous Deployment)
- Continuous Delivery와 Continuous Deployment는 유사
- 둘 다 애플리케이션에 적용한 변경 사항이 버그 테스트를 거쳐 리포지토리에 업로드 되는 것을 뜻함
- 실제 개발에선 개발환경과 운영환경을 구별해서 개발
- 개발 환경에서는 Delivery와 Deployment 모두 자동화를 함
- 운영환경에선 Delivery는 마지막에 개발자가 수동으로 해줘야 하는 부분을 남겨놓고 Deployment는 운영환경에서도 자동화 함