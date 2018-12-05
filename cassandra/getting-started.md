# Getting Started
이 섹션에서는 아파치 카산드라를 사용하는 방법에 대해서 설명하며, 카산드라를 처음 사용하는 경우 가장 먼저 읽어야 합니다.

## 카산드라 설치하기

### 미리 준비해둘 것
- Oracle JDK 8 또는 Open Jdk 8 이 설치되어 있어야합니다.
- `cqlsh`를 사용할 경우에 최신 버전 Python 2.7 버전이 필요합니다.

### 바이너리 파일로 설치
- 바이너리 파일 다운로드
- 아래의 명령어 실행
```
tar -xvf apache-cassandra-3.6-bin.tar.gz cassandras
```
- 위 명령어대로 실행 하면 `apache-cassandra-3.6`으로 압축이 풀리므로 다운로드 한 릴리스 번호로 `3.6`을 변경하세요.
  - `apache-cassandra-3.6\bin` 의 경로를 추가(선택사항).
  - 터미널에서 `bin/cassandra -f` 명령어를 호출하여 포어그라운드에서 카산드라를 시작해보세요. `ctrl-c`를 누르면 카산드라는 종료됩니다.
  - 백그라운드에서 수행시키기 위해서 터미널 에서 `bin/cassandra` 명령어를 호출하세요. 종료할 때는 `kill pid` 또는 `pkill -f CassandraDaemon` 명령어를 터미널에서 호출.
  - `pid`를 알아내려면 터미널에서 `pgrep -f CassandraDaemon` 명령어를 호출하세요.
  - 카산드라가 동작하고 있는지 확인하려면 터미널에서 `bin/nodetool status` 명령어를 호출하세요.
  - 설정 파일들은 `conf` 서브 디렉토리에 존재합니다.
  - 2.1 버전 이후부터 로그와 데이터 디렉토리들은 `logs`와 `data` 의 서브 디렉토리에 존재합니다. 이전 버전은 `/var/log/cassandra`, `/var/lib/cassandra` 에서 확인할 수 있습니다.
  - 이러한 이유 때문에 루트 권한으로 카산드라를 실행 시키기 위해서는 `conf/cassandra.yaml` 파일을 수정하세요.(현재 사용자가 소유한 디렉토리로 변경)
