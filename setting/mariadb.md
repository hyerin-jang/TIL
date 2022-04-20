mariadb
============

###1.home brew 설치
   1) 터미널에 아래 명령어 입력
   ```
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   ```
   2) brew update
   3) brew install mariadb

###2. mariadb 시작
   1) brew services list 로 mariadb 설치되어 있는지 확인
   2) brew services start mariadb
   3) sudo mysql -u root (루트계정으로 로그인)

###3. 계정 생성
    CREATE USER '[계정아이디]'@'[접속을 허용할 ip]' IDENTIFIED BY '[계정비밀번호]';
   
###4. 권한 부여
    GRANT ALL PRIVILEGES ON [데이터베이스 이름].[허용할 테이블] TO '[계정이름]'@'[허용ip]';
   - 모든 테이블에 접근 권할 주려면 테이블 명에 * 입력

###5. 생성된 계정 확인
    select host,user from mysql.user;
