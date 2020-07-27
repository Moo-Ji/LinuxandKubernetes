# Docker &Kuberrnetes

2020.07.27.

설규환 강사님

##### 클라우드 기본

클라우드 - 원하는 자원을 빌려와서 사용하는 것. 

cloud <-> on-Premises

유형 - IaaS(Infrastructure as a service), PaaS(platform), SaaS(software)

인프라를 구축하기 위해 네트워크와 스토리지 등을 물리적으로 갖추는 것이 전통적인 IT 방식. 

플랫폼 단에서는 미들웨어와 런타임도 서비스로 제공 받음.  데이터와 어플은 직접 관리



##### 장점

- 초기 비용이 저렴, 직접 하드웨어를 갖출 필요가 없음. 

  확장성이 뛰어남. 서비스 인원에 따라서 인프라 크기 조절 가능

- 퍼블릭 클라우드에서는 자동 백업을 제공하기도 함. 

  인프라를 분산하여 구축 가능. 안정성. 

##### 단점

* 온프레미스는 클라우드 서비스보다 높은 가용성과 기밀성 갖춤. 

SLA.

* 특수한 요구사항이 있는 서비스를 처리할 때 어려움. 



##### 구분

- 퍼블릭 클라우드

  AWS, Google Cloud, Azure 등. 

* 프라이빗 클라우드

  클라우드의 장점을 온프레미스와 결합.

  인프라를 구축할 때, 클라우드를 내부적으로 구축하는 형태. 



### 가상화

Virtualization

클라우드 서비스를 구성하기 위해 가상화 개념이 필요. 

* 자원을 가상으로 분할할 수 있는 기능. 

* 서버 가상화 - 가장 일반적인 가상화 기술. ex. 윈도우 리소스를 끌어와 리눅스 구축

* 컨테이너 가상화. 

  * 컨테이너 -  Container. 
    * 격리성. 보안을 위해 필요.  - cgroup, namespace

  * Hypervisor ex. vm ware, virtual box 등. 

    호스트가 소프트웨어 파티셔닝을 할 수 있게 만들어주는 소프트웨어. 

    * 하이퍼바이저의 가상화. 운영체제 대신, 하드웨어에 하이퍼바이저를 직접 올람. 
    * 호스트 OS가 없으므로 더 많은 자원을 사용할 수 있고, 직접 하드웨어에 접근 가능. 
    * 그러나 관리를 하기 위해서는 별도의 컴퓨터와 콘솔이 필요함. 

  * 컨테이너 가상화 호스트 OS 위에 컨테이너 관리 소프트웨어를 설치

    * 별도의 게스트, 호스트 OS가 존재하지 않음. 기반 OS 의 리소스를 끌어쓸 수 있음. 효율성이 목적 

  * p.7 리눅스 컨테이너



### 도커

컨테이너를 컨트롤할 수 있는 도구.

##### 장점

* 서버를 코드 형태로 정의 

  * IaC : Infrastructure as a code. 인프라 관리를 코드로 수행

    cf. 기존에는 물리적인 방식으로 컨트롤. 

  하나의 컨테이너는 하나의 역할을 함. 일관화된 환경을 유지할 수 있음. 

* 이식성
  * 환경을 잘 갖추지 못하면, 개발, 테스트, 서비스 환경에서 동작을 잘 하지 않을 가능성이 있음. 일관성을 유지 하는데 용이. 
* 상호운용성
  * 다양한 벤더에서 지원. 쿠버네티스도 도커를 통해 컨트롤. 

##### 구조

* 이미지

  이미지를 가져오는 것이 첫 번 째 단계. 

  파일 생성, 확장 후 새로운 내용을 레이어에 저장하게 됨. 

  COW copy on write

* 저장소

  이미지를 가져오는 장소. 도커허브. 

* 컨테이너

  데이터의 틀 - 이미지, 실제 동작 - 컨테이너. 이미지의 실행형태. 

  디스크에 있으면 이미지, 실행하면 컨테이너. 

  프로그램과 프로세스의 관계와 유사. 



##### 기능

* 이미지 생성, 공유, 컨테이너 생성. 
* 레지스트리 이미지를 받아오고 올릴 수 있는 곳. 



##### 실습

소프트웨어 설치 방법

1. 소스코드 컴파일 gcc 등의 컴파일러를 사용하여 컴파일. 

2. 사전에 정의된 설정에 따라 컴파일 

   makefile 이 존재. 설치 과정

   ```{linux}
   # configure
   # make
   # make install
   ```

3. 패키지 파일 형태로 된 파일을 설치

   CentOS : xxx.rpm

   Ubuntu : xxx.deb

4. yum을 사용한 레포지토리 설치

   기본 제공, epel-release : Extra Package for Enterprise Linux

   ```{linux}
   # yum install epel-release
   ```

5. Docker는 별도의 레포지토리를 추가하여 설치. 

   https://docs.docker.com/engine/install/centos/

   1) 필요한 기본 패키지 설치

   ```{linux}
   $ sudo yum install yum-utils device-mapper-persistent-data lvm2
   ```

   2) docker 설치를 위한 repository 추가

   ```{linux}
   $ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

   3) 레포지토리 추가 확인

```linux
$ yum repolist
```

​		4) 도커 엔진, 커멘드라인 도구, 컨테이너 실행 도구 설치

```{linux}
$ sudo yum -y install docker-ce docker-ce-cli containerd.io
```

​		5) 서비스 시작

```{linux}
$ sudo systemctl start docker.service
```

​		6) docker 그룹 지정

```{linux}
$ sudo usermod -aG docker $USER
```

로그아웃 해야 `id` 조회 시 그룹 지정 상태가 반영된 것을 확인할 수 있음. 



##### 컨테이너 실행

hello-world 컨테이너 실행해보기

```{linux}
$ docker run hello-world
```

##### 이미지 검색

```{linux}
$ docker search centos
```

이미지 가져오기 

```{linux}
$ docker pull centos
```

-> 로그인 후 진행해야 할 때 필요한 명령어. `$ docker login`

버전을 맞추기 위해서는 태그 추가. `centos:태그`

이미지 목록 조회

```{docker}
$ docker images
$ docker image ls
```

image : 사용법 / images , image ls 전체 사용 목록

이미지 삭제

```{linux}
$ docker image rm centos:lagest
$ docker image rm contos:7 my_sql # 여러 개 지우기
```

`-f` 옵션 사용 시 강제로 지울 수 있음. 

`docker rmi` = `docker image rm`

이미지 정보 조회

```{linux}
$ docker inspect
```

이미지 파일로 저장, 등록

```{linux}
$ docker load
$ docker save -o img.tar centos:latest httpd:latest
$ docker load -i img.tar
```



##### 컨테이너 실행 

컨테이너 목록 확인 

```{linux}
$ docker ps # 현재 실행중인 컨테이너 출력
$ docker ps -a # 현재 호스트의 모든 컨테이너 조회
```

컨테이너 생성 

```{linux}
$ docker create
```

이 단계에서는 `ps`로 조회가 안됨. 

`-i` 연결되어 있지 않아도 표준 입출력 유지

`-it` ssh에 연결하듯 명령어를 치면서 실행, 

`--name`을 붙여야 구분 쉬움

컨테이너 실행

```{linux}
$ docker start web1
```

ip 정보 조회

```{linux}
$ docker inspect web1 | grep -i ipaddress
```

컨테이너 접근 `curl`

컨테이너의 표준 입출력에 접근 

```{linux}
docker start -ia os1
#접속 위치 변경됨. [root@12ebe6f78cad /]# exit로 나갈 수 있음. 그럼 컨테이너가 종료됨. 
```











