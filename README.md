# E1-1 · 개발자용 작업실 꾸미기

## 1) 프로젝트 개요
<!-- 미션 목표 요약 -->

## 2) 실행 환경
- OS:
- Shell / 터미널:
- Docker:
- Git:

## 3) 수행 체크리스트
- [x] 터미널 기본 조작
- [x] 권한 변경 실습 (파일 1 · 디렉토리 1)
- [x] Docker 설치/점검
- [x] Docker 기본 운영 (images / ps / logs / stats)
- [x] hello-world · ubuntu 실습
- [ ] Dockerfile 커스텀 이미지
- [ ] 포트 매핑 접속
- [ ] 바인드 마운트 반영
- [ ] 볼륨 영속성
- [ ] Git 설정 + VS Code GitHub 연동

## 4) 수행 로그 및 검증
### 4-1 터미널 조작
환경: Windows PowerShell (VS Code 터미널). 작업 폴더 `D:\20_Codyssey\10_미션\E1-1_workstation\practice`

| 작업 | 명령 |
|---|---|
| 현재 위치 확인 | `pwd` |
| 목록 확인(숨김 포함) | `ls -Force` |
| 이동 | `cd` |
| 생성 | `mkdir`, `New-Item` |
| 복사 | `cp` |
| 이동/이름변경 | `mv` |
| 삭제 | `rm` |
| 내용 확인 | `cat` |
| 빈 파일 생성 | `New-Item <파일> -ItemType File` |

```powershell
PS D:\20_Codyssey\10_미션\E1-1_workstation> pwd

Path
----
D:\20_Codyssey\10_미션\E1-1_workstation

PS D:\20_Codyssey\10_미션\E1-1_workstation> mkdir practice

    디렉터리: D:\20_Codyssey\10_미션\E1-1_workstation

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----      2026-11-03   오후 1:44                practice

PS D:\20_Codyssey\10_미션\E1-1_workstation> cd practice
PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> pwd

Path
----
D:\20_Codyssey\10_미션\E1-1_workstation\practice

PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> New-Item hello.txt -ItemType File

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-11-03   오후 1:45              0 hello.txt

PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> echo "안녕 터미널" > memo.txt
PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> ls

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-11-03   오후 1:45              0 hello.txt
-a----      2026-11-03   오후 1:45             18 memo.txt

PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> cat memo.txt
안녕 터미널
PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> cp memo.txt memo_copy.txt
PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> mv memo_copy.txt renamed.txt
PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> ls

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-11-03   오후 1:45              0 hello.txt
-a----      2026-11-03   오후 1:45             18 memo.txt
-a----      2026-11-03   오후 1:45             18 renamed.txt

PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> rm renamed.txt
PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> ls

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-11-03   오후 1:45              0 hello.txt
-a----      2026-11-03   오후 1:45             18 memo.txt

PS D:\20_Codyssey\10_미션\E1-1_workstation\practice> cd ..
PS D:\20_Codyssey\10_미션\E1-1_workstation> pwd

Path
----
D:\20_Codyssey\10_미션\E1-1_workstation
```

**숨김 파일 포함 목록 (`ls -Force`)**
```powershell
PS D:\20_Codyssey\10_미션\E1-1_workstation> ls -Force

    디렉터리: D:\20_Codyssey\10_미션\E1-1_workstation

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--h-        2026-11-03  오후 1:36                .git
d----        2026-11-03  오후 1:46                practice
-a---        2026-11-03  오후 1:25            184 .gitignore
-a---        2026-11-03  오후 1:50           4551 README.md
```
`Mode`의 `h`가 숨김 속성이다. `.git`은 `-Force` 없이는 목록에 나타나지 않는다.

**관찰**
- `memo.txt`는 글자가 6자인데 18바이트다. PowerShell의 `>`는 파일을 UTF-16(BOM 포함)으로 저장하기 때문이다. 리눅스/Docker 안에서 같은 파일을 읽으면 글자가 깨질 수 있어, 이후에는 `Set-Content -Encoding utf8` 등으로 저장한다.
- `ls`, `cat`, `cp`, `mv`, `rm`은 PowerShell의 별칭(alias)이며 실제 리눅스 명령과 옵션이 다르다. (예: 숨김 파일은 리눅스 `ls -a`, PowerShell `ls -Force`)
### 4-2 권한 실습
Windows PowerShell에는 `chmod`가 없어서(권한 체계가 다름) 리눅스인 ubuntu 컨테이너 안에서 실습했다. 대상은 파일 1개(`file.txt`), 디렉토리 1개(`dir1`).

```bash
$ mkdir -p /tmp/perm && cd /tmp/perm && touch file.txt && mkdir dir1 && ls -ld file.txt dir1
drwxr-xr-x 2 root root 4096 Nov  3 05:17 dir1
-rw-r--r-- 1 root root    0 Nov  3 05:17 file.txt          # 변경 전 (기본값: 파일 644, 디렉토리 755)

$ chmod 600 file.txt && chmod 700 dir1 && ls -ld file.txt dir1
drwx------ 2 root root 4096 Nov  3 05:17 dir1
-rw------- 1 root root    0 Nov  3 05:17 file.txt          # 변경 후 (소유자만 접근)

$ chmod 777 file.txt && stat -c "%a %A %n" file.txt dir1
777 -rwxrwxrwx file.txt
700 drwx------ dir1
```

| 숫자 | 기호 | 의미 |
|---|---|---|
| 644 | `rw-r--r--` | 소유자 읽기·쓰기 / 그룹·기타 읽기 |
| 755 | `rwxr-xr-x` | 소유자 읽기·쓰기·실행 / 그룹·기타 읽기·실행 |
| 600 | `rw-------` | 소유자만 읽기·쓰기 |
| 700 | `rwx------` | 소유자만 읽기·쓰기·실행 |

- r=4, w=2, x=1을 자리별(소유자·그룹·기타)로 더한다. 7=4+2+1, 5=4+1, 6=4+2, 4=4.
- 디렉토리의 `x`는 "안으로 들어갈 수 있는 권한"이다.
- 절대 경로는 `/`부터 시작(`/tmp/perm/file.txt`), 상대 경로는 현재 위치 기준(`./file.txt`, `../dir1`)이다.

### 4-3 Docker 설치·점검
설치: 교안의 설치 스크립트로 Docker Desktop 설치 후 재시작.
```powershell
PS> docker --version
Docker version 29.9.2, build a82e528

PS> docker info          # 발췌
Server:
 Containers: 0
 Server Version: 29.9.2
 Storage Driver: overlayfs
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
```
`docker info`가 Server 항목까지 출력되므로 Docker 데몬이 동작 중이다. 커널이 `microsoft-standard-WSL2`인 것은 Docker Desktop이 WSL 2 위에서 리눅스 엔진을 돌리기 때문이다.

### 4-4 Docker 운영
```powershell
PS> docker pull alpine
Status: Downloaded newer image for alpine:latest

PS> docker images
IMAGE           ID             DISK USAGE   CONTENT SIZE
alpine:latest   be2656061b53         13MB         3.94MB

PS> docker run -d --name web-test nginx
bdf1a70e81a9...

PS> docker ps
CONTAINER ID   IMAGE     COMMAND                  STATUS          PORTS     NAMES
bdf1a70e81a9   nginx     "/docker-entrypoint.…"   Up 3 seconds    80/tcp    web-test
67692a7ecc82   ubuntu    "bash"                   Up About a minute          u1

PS> docker logs web-test          # 발췌
/docker-entrypoint.sh: Configuration complete; ready for start up
nginx/1.31.8
start worker processes

PS> docker stats --no-stream
NAME       CPU %     MEM USAGE / LIMIT     MEM %     PIDS
web-test   0.00%     19.59MiB / 15.38GiB   0.12%     23

PS> docker stop web-test
PS> docker ps                     # 실행 중인 것만
CONTAINER ID   IMAGE     COMMAND   STATUS              NAMES
67692a7ecc82   ubuntu    "bash"    Up About a minute   u1

PS> docker ps -a                  # 중지된 것까지
NAMES         STATUS
web-test      Exited (0) Less than a second ago
u1            Up About a minute
musing_rosalind   Exited (0) About a minute ago

PS> docker rm web-test
PS> docker ps -a                  # web-test 삭제 확인
```
- `docker ps`는 실행 중만, `docker ps -a`는 중지된 컨테이너까지 보여 준다.
- 이름 없이 실행한 컨테이너(`hello-world`)는 Docker가 `musing_rosalind` 같은 임의 이름을 붙인다.
- `docker stats`는 `--no-stream`이 없으면 계속 갱신된다.

### 4-5 컨테이너 실행 실습
```powershell
PS> docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.

PS> docker run -it --name u1 ubuntu bash
root@67692a7ecc82:/# 
```
ubuntu 컨테이너 내부 명령(`docker exec u1 ...`로 실행):
```bash
$ ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
$ echo hello
hello
$ pwd
/
```
**attach/exec 차이 관찰**
- `docker run -it ... bash`로 들어간 셸이 컨테이너의 **메인 프로세스(PID 1)** 다. 이 셸에서 `exit`하면 메인 프로세스가 끝나서 컨테이너도 중지된다.
- `docker exec`는 실행 중인 컨테이너에 **별도 프로세스**를 추가로 실행한다. 이 명령이 끝나도 컨테이너는 계속 실행 상태(`Up`)로 남는다. 실제로 셸이 열린 상태에서 `docker exec u1 ...`로 위 명령들을 실행해도 `u1`은 `Up`이었다.
- `docker attach`는 메인 프로세스의 입출력에 붙는 것이라, 거기서 `exit`하면 마찬가지로 컨테이너가 종료될 것이다. (이론 정리, 아직 직접 실행해 확인하지 않음)
### 4-6 커스텀 이미지 (Dockerfile)
### 4-7 포트 매핑
### 4-8 바인드 마운트
### 4-9 볼륨 영속성
### 4-10 Git · GitHub 연동

## 5) 트러블슈팅
### 문제 1
- 문제:
- 원인 가설:
- 확인:
- 해결/대안:

### 문제 2
- 문제:
- 원인 가설:
- 확인:
- 해결/대안:

## 6) 학습 정리
- 절대/상대 경로:
- 권한 r/w/x · 755/644:
- 포트 매핑이 필요한 이유:
- 볼륨:
- Git vs GitHub:
