# Linux Basic Day 2

2020.07.23.

mode 

`v` visual mode 진입. 마우스로 블록을 잡을 수 있음. 복사는 y, 삭제는 b(잘라내기 이므로 p 로 붙여넣기)

`V` visual line mode 라인 전체를 마우스로 블록잡을 때 쓸 수 있음. 

​	`ctr + v` 라인 관계없이 마우스로 블록을 잡을 수 있음. 



### 사용자 및 그룹 관리

/etc/defalult/useradd, /etc/logindefs 파일 설정으로 암호 관련 계정 설정 가능. 

defalt 값은 명령어, vi 편집기 통해서 수정 가능. 

새로 계정을 만들면 가장 마지막 + 1 gid, uid 값이 부여됨. 

홈디렉토리를 다른 것으로 하려면 홈 디렉토리를 미리 새로 만들어야 함. 

- 생성

`useradd [option] [argument] username` 계정 신설. gid는 기존 값만 부여 가능. 디폴트값은 uid와 동일함. 

`passwd user01` root는 다른 사용자에게 비번 부여 가능, 비번을 부여해야 로그인이 가능. 

`vi etc/group` 에서 보조그룹 구성원 확인 가능. 

- 변경

`usermod -u 1002 -G 10 10 -c ""  -md /home/user02 -s /bin/bash` G 기존 보조그룹 삭제 후 추가. a 옵션은 추가만

d 홈디렉토리 설정만 변경, md 실제 폴더까지 함께 변경, 

홈디렉토리 변경 `useradd -D

`groupadd -g 1002 user02` 그룹을 추가함. 

`usermod -g 1002 user02` 추가한 그룹을 적용함. 



`usermod -l testuser -md /home/user03 user03`  `-l` 옵션을 통해 유저네임 변경 가능



* 삭제

`userdel -r username` `-r` 옵션을 붙이지 않고 그냥 삭제하면 메일과 홈디렉토리가 남아있음. 

`-u uid값`도 삭제하지 않으면, uid값을 받은 유저가 기존 파일을 볼 수 있음.  (확인 필요)



##### 암호 설정

`chage` 암호 설정 변경 , 옵션은 책에서 확인



### 특수권한 확장권한

setuid 소유주의 권한으로 명령어를 실행시킴. -> 개념 더 보기 필요.

​	`sudo` 명령어 없이도 root 권한 끌어다 쓸 때 가능, 위험성 인지해두기. 

​	설정을 통해 특정 명령어도 루트 권한으로 끌어다 쓸 수 있게 됨.

setgid 소유그룹의 권한으로 명령어를 실행시킴 -> '실행'의 권한 변경하여 적용. 



디렉토리는 '실행'이 아니어서 효과가 다름 

해당 그룹의 소유그룹을 상속함. 일반 사용자의 성격이 없더라도 상속한 폴더와 파일을 볼 수 있다. 



sticky bit t가 붙어있으면,  누구나 만들 수 있지만 지우는 것은 관리자와 소유주만 가능

move 도 같음. 그러나 cp는 가능(원본 파일과 별도로 움직이므로.)



S 소유주의 실행권한이 없으나, setuid 권한이 붙어있기는 하므로, 일반 사용자는 볼 수 있음 -> 대문자일 때 의미 더 공부해두기



##### ACL

접근제어목록 `.` acl설정 안됨 , 

소유자, 소유그룹, 기타사용자 밖에 없으므로 구분을 다양하게 하기 위해 만들었음.

특정사용자, 소유그룹, 특정그룹만 영향을 받음. 

`getfacl` acl 설정 확인 명령어, flag로 확인 가능. 

acl entry도 확인 가능

`setfacl -m u:centos:7, g:user01:7 /ptest `mask 생김 특정사용자, 소유그룹, 특정그룹의 최대권한

소유그룹 권한도 마스크 권한으로 바뀜 소유그룹과 마스크권한이 동기화됨. 



ACL없앨 때는 마스크도 지웠는지 확인하거나 옵션`-b` 활용. cf. `-s`옵션

기본 ACL은 디렉토리에 상속이 되나, 기존 것에는 반영 안됨. 



----

### 파일 입출력에 대한 컨트롤 방법

#### `cut paste diff sort uniq aed awk`

`cut -c 2-4 data.txt`

`-d` 파일 안에 있는 구격에 맞게 공백을 기준으로 데이터를 가져올 수 있다. 

`cut -d " " -f1,3 data.txt`

단점 규칙이 일정해야 한다. 필드 기준으로 잘라서 볼 수 있다. 

장점 가볍다. 빠른 처리. 

`cut -d ":" -f7,1 /etc/passwd|head -5`



##### `diff`

`diff /etc/passwd aaa`

`echo "test" >> aaa`

`diff /etc/passwd aaa`

달라진 파일의 결과를 출력함. 

뭐랑 뭐를 비교한거지?

백업되어있는 파일과 비교할 때, 라인별로 행간 비교할 수 있음. 

위치와 변화된 내용이 모두 나온다는 장점이 있음. 



##### `grep`

`grep -i ROOT /etc/passwd`

i,n, v, l, c

`grep -li umask /etc/*  >2 /dev/null` i 옵션, 대소문자 구분 안함.

`grep -c root /etc/passwd` root가 몇 번 나오나. 많이 쓰이는 옵션.

`lastb` 로그인 실패 정보가 남아있음. 

`lastb | awk '{print $3}' | grep -c ^i p |sort | uniq -c`

어떤 아이피가 몇 번 실패했는지 알 수 있음. 

 `egrep`, `fgrep` 등이 있음. 



##### `sort`

`sort 파일명` 정렬

`sort -r filename` 내림차순 정렬

`sort -t " 구분자" -k colnumber filename`  컬럼 k를 기준으로 오름차순 정렬

`sort -r -t ":" -k3 /경로/파일 | head -10`숫자를 한 **글자** 단위로 처리. 99::가 999:보다 크다고 인식됨 , `-n` 옵션으로 해결.



##### `sed`

해당 옵션보다는 `grep`을 많이 쓰는 편

##### `awk`

프로그램이지만 명령어처럼 사용함.

`awk '조건{print $1, $2}' data.txt;`

1행과 2행 출력

일정하지 않은 간격의 규칙도 공백을 묶어서 계산함. 

`df -ThP | awk  '{print $1, "사용량 = "$6}'`



---

`awk '{print $3}' lastb.txt | grep ^ip- | sort | uniq | sed -e 's/ip-//g' -e's/-/./g'`

`for i in ``ls /proc | grep ^[0-9]|sort -n``

디렉토리는 읽기 권한을 가지려면 실행권한이 반드시 있어야 함. 555!

----



### 작업 스케쥴링

시간이 요일보다 앞에 와야함. today만 쓰면 내일 이 시간이 됨. 

1부터 월요일

아나크론 일정시간이 지나도 켜지면 못했던 작업을 수행함. 



















