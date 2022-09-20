# Git Stash
- 아직 마무리하지 않은 작업을 스택에 잠시 저장할 수 있도록 하는 명령어
- 이를 통해 아직 완료하지 않은 일을 commit하지 않고 나중에 다시 꺼내와 마무리할 수 있음

- git stash 명령을 사용하면 워킹 디렉토리에서 수정한 파일들만 저장
- stash란 아래에 해당하는 파일들을 보관해두는 장소


- Modified이면서 Tracked 상태인 파일
  - Tracked 상태인 파일을 수정한 경우
  - Tracked: 과거에 이미 commit하여 스냅샷에 넣어진 관리 대상 상태의 파일
- Staging Area에 있는 파일(Staged 상태의 파일)
  - git add 명령을 실행한 경우
  - Staged 상태로 만들려면 git add 명령을 실행해야 함
  - git add는 파일을 새로 추적할 때도 사용하고 수정한 파일을 Staged 상태로 만들 때도 사용

## 하던 작업 임시 저장
````
$ git stash
$ git stash save
````

## stash 목록 확인
````
$ git stash list
````

## stash 적용 (했던 작업 다시 가져오기)
````
// 가장 최근 stash 가져와 적용
$ git stash apply

// stash 이름에 해당하는 stash 적용
$ git stash apply [stash 이름]
````
- 위 명령어로는 staged 상태였던 파일을 자동으로 다시 staged 상태로 만들어주지 않음
- index 옵션을 주어야 staged 상태까지 복원
````
$ git stash apply --index
````

## stash 제게
````
// 가장 최근의 stash를 제거
$ git stash drop

// stash 이름에 해당하는 stash 제거
$ git stash drop [stash 이름]

// stash 적용과 동시에 스택에서 stash 제거
$ git stash pop
````

## stash 되돌리기
````
// 가장 최근의 stash를 사용하여 패치를 만들고 그것을 거꾸로 적용
$ git stash show -p | git apply -R

// stash 이름에 해당하는 stash를 이용하여 거꾸로 적용
$ git stash show -p [stash 이름] | git apply -R
````