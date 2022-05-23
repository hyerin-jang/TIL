# Github Actions
- CI/CD를 가능하게 해주는 툴
- 그 외 CI/CD tool: Jenkins, AWS Code Deploy, GCP의 Code Build

## Workflow
- CI/CD 같은 자동화된 프로세스를 만들기 위한 Yaml 파일
- 확장자: .yml 이나 .yaml
- workflow > job > step > action 순으로 명시를 해서 파일을 작성해야 함

### workflows
- 레파지토리에 정의한 자동화된 프로세스 절차
- workflows는 하나 이상의 Jobs으로 구성되고 이벤트 기반으로 트리거 됨
- schedule을 설정해 정해진 시간에 실행되도록 하거나 main 브런치에 코드가 push가 되거나 
어떤 브런치에 pull request가 만들어지면 실행되도록 설정할 수 있음
- 또는 webhook을 이용해 외부 이벤트에 대한 응답으로 실행할 수 있음

### Jobs
- 하나 이상의 steps로 구성되고 workflow에 있는 job 끼리는 병렬적으로 실행
- job 끼리는 순서대로 실행되도록 설정할 수 있음
- 한 job은 build 과정을 실행하고 다른 job은 test를 실행하도록 명시하고 test job은 build가 완료된 후 실행하도록 할 수 있음
- 이때 두 job 끼리는 다른 환경에서 실행되므로 깉은 스토로지를 사용하도록 설정해야 함

### Steps
- 하나의 step은 하나의 task
- 하나의 job에 여러 task를 넣을 수 있음
- step은 action이나 shell command로도 생성될 수 있음
- 하나의 job에 있는 step은 같은 runner에 의해 실행

### Actions
- workflow에서 가장 작은 개념으로 action은 독립된 실행할 명령

### Runners
- Github에 의해 호스팅 되고있는 서버
- runner를 통해서 job을 실행시킴
