# Docker를 이용해서 Oracle XE 11g 설치하기

이 설치 가이드를 따라 설치하면 시스템에 Oracle XE 11g를 설치합니다.

또한, DB 수업에서 필요한 SCOTT 사용자도 자동 설치하고, SCOTT 사용자의 테이블과 데이터도 함께 등록해둡니다.

 

## 1. Docker 설치

- Download from https://www.docker.com/products/docker-desktop
- 설치파일 인스톨 (맥의 경우. Docker.dmg 실행 후 Application으로 drag-and-drop)
- Git 도 설치되어 있어야 합니다!



## 2. Image 파일 만들기

- Github에서 docker image project를 clone합니다.

- ```sh
  # 작업을 할 디렉토리를 적절히 만들어 놓습니다. (예: c:\works\database)
  # 윈도의 경우 디렉토리를 '\'로 구분해야 할 수도 있습니다. 'c:\' 등을 가지고 드라이브를 지정해야 할 수도 있습니다.
  cd /works/db
  git clone git@github.com:dongseop/docker-oracle-xe-11g.git
  cd docker-oracle-xe-11g
  
  # docker build 명령으로 "mjudb/oracle-xe-11g"라는 이미지를 만듭시다.
  docker build -t mjudb/oracle-xe-11g --no-cache . 
  
  # 실행 결과를 잘 살펴봅니다. Successfully built가 되어야 합니다.
  # 빌드가 정상적으로 완료되면 시스템에 해당 이미지가 존재한다. 확인해보자.
  docker image ls
  ```



## 3. Image 실행

```sh
 docker run -d -p 49161:1521 -e ORACLE_ALLOW_REMOTE=true mjudb/oracle-xe-11g  --name oracle
```

- 뜻을 이해하자. 실행할 컨테이너의 이미지 이름 "mjudb/oracle-xe-11g"
- -d : deamon으로 실행, 즉 host컴퓨터의 background에서 서버로 실행됨
- -p : localhost의 49161 port를 oracle container의 1521 port (Oracle의 기본 포트)로 연결한다.
  - 본인 컴퓨터의 49161 포트가 이미 사용 중이면 다른 숫자로 포트를 지정하면 됨. (예: 49162, 49664...)
  - 연결된 port가 어디인지 정확히 기억하자!!!
- -e ORACLE_ALLOW_REMOTE=true 로 외부 접속을 허용한다는 의미 (환경 변수를 통해 옵션을 지정)
- --name : 앞으로 이 컨테이너의 이름은 "oracle"이라고 부르겠다는 뜻
- 컨테이너가 시작되고 나서 오라클 서버가 정상적으로 시작되는데는 약간의 시간이 걸림



## 4. SQL Developer 설치

- https://www.oracle.com/tools/downloads/sqldev-v192-downloads.html

- 본인의 OS에 맞는 버전을 다운로드
- 압축을 풀면 Mac의 경우 



## 5. SQL Developer를 이용하여 Oracle 접속

- 접속 (+ 버튼) 을 눌러 새 접속을 만든다. 관리자 접속용과 SCOTT 접속용을 둘 다 만들어두자.

- 사용자이름, 비밀번호, 호스트이름, 포트, SID 등은 다음 정보를 따름

- ```
  << 관리자용 >>
  hostname: localhost
  port: 49161        (위의 docker를 띄운 local port를 기억하자)
  sid: xe
  username: system
  password: oracle   (이 docker는 관리자 계정 비밀 번호를 기본으로 oracle로 설정해 둔 것임)

  << SCOTT >>
  hostname: localhost
  port: 49161      
  sid: xe
  username: scott
  password: tiger
  ```
  
- 테스트 후 문제가 없으면 둘 다 각각 저장.

- 만일 "Locale not recognized" 에러가 뜬다면??? (참고: http://taewan.kim/tip/sqldeveloper_error_unrecog_locale/)

  - 패키지 내용보기로 들어가서 (혹은 윈도는 디렉토리내부에서 )

  - Contents/Resources/sqldeveloper/sqldeveloper/bin/sqldeveloper.conf 파일을 찾아서 다음 두 줄을 끝에 추가.

  - ```sh
    # Contents/Resources/sqldeveloper/sqldeveloper/bin/sqldeveloper.conf 
    AddVMOption -Duser.language=ko
    AddVMOption -Duser.country=KR 
    ```



## 7. Docker 컨테이너 (Oracle 서버) 종료 방법

```sh
# 현재 실행중인 컨테이너 확인
docker ps

# 종료된 컨테이너까지 모두 확인
docker ps -a

# 컨테이너 종료 (시스템에 시간을 조금 주고 천천히 종료하도록 지시) 
# 사용하지 않을 때는 컨테이너를 종료해두면 시스템의 CPU/Memory 등을 아낄 수 있습니다.
docker stop --time=30 <<container 이름 혹은 Container ID>>

# 컨테이너 재시작. 정지해둔 컨테이너는 재시작하면 다시 사용할 수 있습니다.
docker start <<container 이름 혹은 Container ID>>

# 컨테이너 삭제. 삭제하면 모든 정보가 날아갑니다. 
# 나중에 다시 실행하고 싶으면 docker run을 이용해 다시 실행하면 됩니다. (3번 참조)
docker rm <<container 이름 혹은 Container ID>>

```



## 8. DataGrip

SQL Developer가 잘 동작하지 않거나 불편해서 다른 툴을 이용하고 싶다면, DataGrip을 사용할 수도 있습니다.

https://www.jetbrains.com/datagrip/

학생 인증을 받으면 (@mju.ac.kr 이메일로 인증 가능) 무료로 사용할 수 있습니다.

https://www.jetbrains.com/student/



### 참고

- https://whitepaek.tistory.com/6
- https://whitepaek.tistory.com/40



