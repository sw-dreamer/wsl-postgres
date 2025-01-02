# Docker에서 PostgreSQL 설치 및 볼륨 마운트 설정

이 가이드는 Docker에서 PostgreSQL을 설치하고, 데이터를 지속적으로 저장하기 위해 **Docker 볼륨을 마운트**하는 방법을 설명합니다. 컨테이너가 재시작되거나 삭제되더라도 데이터는 안전하게 유지됩니다.

## 1. Docker Volume 생성 (선택 사항)
먼저, 데이터를 저장할 **Docker 볼륨**을 생성합니다. 이 볼륨은 PostgreSQL의 데이터를 안전하게 저장하는 데 사용됩니다.

```bash
docker volume create pgdata
```
이 명령어는 pgdata라는 이름의 Docker 볼륨을 생성합니다. 볼륨이 생성되면 PostgreSQL 컨테이너에서 데이터를 이 볼륨에 저장하게 됩니다

## 2. PostgreSQL 컨테이너 실행 (볼륨 마운트 포함)
이제 PostgreSQL Docker 컨테이너를 실행합니다. -v 옵션을 사용하여 Docker 볼륨을 컨테이너 내 PostgreSQL 데이터 디렉토리 /var/lib/postgresql/data)에 마운트합니다.

```bash
docker run --name my-postgres \
  -e POSTGRES_PASSWORD=mysecretpassword \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  -d postgres:14
```
### 명령어 설명
  - --name my-postgres: 컨테이너 이름을 my-postgres로 지정합니다.
  - -e POSTGRES_PASSWORD=mysecretpassword: PostgreSQL의 기본 사용자(postgres)의 비밀번호를 mysecretpassword로 설정합니다.
  - -p 5432:5432: 호스트 시스템의 5432 포트를 컨테이너의 5432 포트에 매핑합니다. 이를 통해 외부에서 PostgreSQL에 접속할 수 있습니다.
  - -v pgdata:/var/lib/postgresql/data: pgdata라는 Docker 볼륨을 PostgreSQL의 데이터 디렉토리인 /var/lib/postgresql/data에 마운트합니다. 이 설정을 통해 PostgreSQL 데이터는 컨테이너가 삭제되더라도 볼륨에 지속적으로 저장됩니다.
  - -d: 컨테이너를 백그라운드 모드로 실행합니다.
  - postgres:14 : 사용할 PostgreSQL Docker 이미지를 지정합니다.

## 3. PostgreSQL 컨테이너 접속

컨테이너가 실행 중이라면, PostgreSQL 데이터베이스에 접속할 수 있습니다. 아래 명령어로 postgres 사용자로 PostgreSQL에 접속합니다.

```bash
docker exec -it my-postgres psql -U postgres
```
### 명령어 설명:
  - docker exec -it my-postgres: my-postgres라는 이름의 컨테이너에 명령을 실행합니다.
  - psql -U postgres: postgres 사용자로 PostgreSQL에 접속합니다.

## 4. 볼륨 확인
컨테이너와 연결된 Docker 볼륨이 정상적으로 생성되었는지 확인하려면, 아래 명령어로 현재 시스템에서 사용 중인 볼륨을 나열할 수 있습니다.

```bash
docker volume ls
```
이 명령어는 현재 Docker 시스템에서 사용 중인 모든 볼륨을 나열합니다. pgdata라는 이름의 볼륨이 목록에 표시되면 정상적으로 생성된 것입니다.
