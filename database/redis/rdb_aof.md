## RDB(Redis Database)

* 특정 시점(Snapshot)의 메모리에 있는 데이터 전체를 바이너리 파일로 저장한다.
* AOF 방식 파일보다 사이즈가 작으므로, 로딩 속도가 AOF보다 빠르다.
* 저장 시점은 `redis.conf` 파일의 save 파라미터로 설정한다.

### redis.conf

* save 900 1 : 900초 동안 1번 이상 key 변경 발생 시 저장
* save 300 10 : 300초 동안 10번 이상 key 변경 발생 시 저장
* save 조건은 여러 개 지정 가능, 하나라도 만족하면 저장 수행

## AOF (Append On File)

* 입력, 수정, 삭제 명령이 실행될 때마다 `appendonly.aof` 파일에 기록된다.
* AOF는 계속 추가하면서 기록되기 때문에, 파일 사이즈가 계속 커져서 OS 파일 사이즈 제한, 레디스 서버 시작 로드 지연 등의 문제가 생길 수 있다. (예를 들어, set 명령으로 동일한 key의 value를 10번 변경하면 메모리에는 수행된 결과값만 남아있지만, AOF에는 10번 명령 모두 남아있다.)
* 이 문제를 해결하기 위해 `rewrite` 명령을 통해 이전 기록은 모두 지우고 최종 데이터만 기록할 수 있다.

#### references
* https://hoooon-s.tistory.com/25