Git
========
버전관리란?
----------
- 버전관리 시스템 (VCS-Version Control System): 파일 변화를 시간에 따라 기록했다가 나중에 특정 시점의 버전을 다시 꺼내 올 수 있는 시스템

분산 버전 관리 시스템(DVCS)
--------------------------
- Git
- 저장소를전부 복제
- 리모트 저장소가 존재해 사담들은 동시에 다양한 그룹과 다양한 방법으로 협업할 수 있다.

Git
----
- 데이터를 스냅샷의 스트림처럼 취급
- 거의 모든 명령을 로컬에서 실행
- 데이터를 저장하기 전에 항상 체크섬을 구하고 그 체크검으로 데이터를 관리
  - SHA-1 해시를 사용하여 체크섬을 만듬. 만든 체크섬읜 40자 길이의 16진수 문자열 (ex. 24b9da6552252987aa493b52f8696cd6d3b00373)
  - Git은 파일을 이름으로 저장하지 않고 위와 같은 해당 파일의 해시로 저장

Git 세가지 상태
----------------
- 파일을 Committed, Modified, Staged 세가지 상태로 관리
  - Committed: 데이터가 로컬 데이버베이스에 안전하게 저장됨
  - Modified: 수정한 파일을 아직 로컬 데이터 베이스에 커밋하지 않음
  - Staged: 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태

Git 세가지 단계
-------------------
1. Git 디렉토리
- Git이 프로젝트의 메타데이터과 객체 데이터베이스를 저장하는 곳
- Git의 핵심
- 다른 컴퓨터에 있는 저장소를 clone 할 때 Git 디렉토리가 만들어짐

2. 워킹 디렉토리
- 프로젝트의 특정 버전을 checkout  gks rjt
- Git 디렉토리는 지금 작업하는 디스크에 있고, 그 디렉토리 안에 압축된 데이터베이스에서 파일을 가져와서 워킹 디렉토리를 만듦

3. Staging Area
- 단순한 파일이고 곧 커밋할 파일에 대한 정보를 저장
- Staging Area

Git으로 하는 일
---------------
1. 워킹 디렉토리에서 파일을 수정
2. Staging Area에 파일을 stage해서 커밋할 스냅샷을 만듦
3. Staging Area에 있는 파일들을 커밋해서 Git 디렉도리에 영구적인 스냅샷으로 저장

- Git 디렉토리에 있는 파일들은 Committed 상태
- 파일을 수정하고 Staging Area에 추가했다면 Staged
- Checkout 하고 나서 수정했지만, 아직 Staging Area에 추가하지 않았으면 Modified

fork 하는 법
-------------
1. 원본 repositary 추가
````
$ git remote add upstream 주소
````
2. 추가 됐는지 확인
````
$ git remote -v
````
3. upstream 최신 내용 불러오기
````
$ git fetch upstream
````
4. merge
````
$ git merge upstream/main
````
5. origin으로 push
````
$ git push origin main
````