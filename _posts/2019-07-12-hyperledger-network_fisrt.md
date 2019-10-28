---
layout: post
comments: true
title:  "[Hyperledger Fabric] AWS 와 docker-swarm 을 이용하여 Fabric 네트워크 구성하기 (1)"
date:   2019-07-12
author: Cory
categories: Hyperledger
permalink : /dev/hyperledger/network-setting-1
tags: blockchain hyperledger fabric
---

블록체인 개발자인데 그동안 블로그에 블록체인에 관련한 포스팅이 한개도 없었습니다. 그래서 이번에 __AWS 클라우드__ 환경에서 __docker-swarm__ 을 이용하여 __Hyperledger Fabric 네트워크 구성__ 하는 튜토리얼에 대해 긴 여정을 떠나볼까 합니다. 물론, 요즘에는 __Kubernetes__ 로 네트워크 구성하는 것이 유행이지만, 사실 어느 것이 메인이 될지는 아직 모르기 때문에 둘 다 알아야 한다고 생각합니다. 이번 시리즈에서는 조금 더 쉬운, docker-swarm 을 사용하는 예제를 먼저 다뤄보고 이후에 시간이 나면 Kubernetes 도 다뤄보도록 하겠습니다.

`시간이 날때 짬내서 조금씩 작성할 예정이라, 언제 완성될지는 모르겠지만 Fabric 을 구성하고자 하는 분들께 도움이 되었으면 합니다. ^^`

이번 시리즈에서는 Fabric 1.4 버전을 사용할 것입니다. 이 포스팅을 작성하는 현재, fabric은 2.0 개발이 한창이라.. 나중에는 2.0 으로 다시써야 할지도 모르겠군요? 

또한 어느 정도 Hyperledger Fabric 에 대한 이해가 바탕이 되어야 합니다. 혹시 개념이 궁금하신 분들은 저희 회사 지식공유 프로젝트인 __dappCampus__ 에서 진행한 강의 [Hyperledger Fabric 개념](https://www.youtube.com/playlist?list=PLlYCl1UOH8dima_f8QOIeY1ieuOAYKo_G) 를 참고하시면 될 것 같습니다.

이번 포스팅에서는 Fabric 노드 세팅을 위한 환경설정을 다루고 다음 포스팅부터 본격적으로 crypto 설정, orderer 설정, peer 설정 등에 대해서 다루도록 하겠습니다.

## 네트워크 구성도

AWS EC2 인스턴스 5개로 만들 예정인데요. 각각의 인스턴스 구성은 다음과 같이 설정할 것입니다.

| EC2 instance | 들어가는 구성요소 |
|:--------|:--------|
| instance 1 | Org1의 ca, Org1의 peer0, couchDB, orderer0 |
| instance 2 | Org1의 peer1, couchDB, orderer1 |
| instance 3 | Org2의 ca, Org2의 peer0, couchDB, orderer2 |
| instance 4 | Org2의 peer1, couchDB |
| instance 5 | docker-swarm, fabric cli |

그림을 보면서 설명하겠습니다.

<img src="/assets/hyperledger/network-1/hyperledger-network1_structure.png" alt="hyperledger-network-structure">

그림에서 같은 색상은 같은 instance 를 의미합니다. 그리고 이 네트워크는 `3개의 orderer`, `1개의 channel`, `2개의 organization`, org 에는 `각각 2개의 peer`, 마지막으로 DB 는 `couchDB` 를 사용합니다. 오케스트레이션 도구로 `docker-swarm` 을 이용했으며, `cli` 로 peer 에게 명령을 내릴 예정입니다.

체인코드에 대한 포스팅이 아니기 때문에 체인코드의 경우 이미 존재하고 있는 [Fabric Example 의 Marbles](https://github.com/hyperledger/fabric/blob/release-1.4/examples/chaincode/go/marbles02/marbles_chaincode.go) 체인코드를 쓸 예정입니다.

이제 본격적으로 한 단계씩 진행해 보도록 하겠습니다. (참고로, 세팅을 잘하시는 분들은 간단하게 보고 알아서 세팅하시면 됩니다.)

## AWS 설정하기

이번 섹션은 사실 Fabric 에 관한 내용이라기 보다 aws 에서 docker swarm, aws network 등의 사용을 위한 세팅을 하는 과정입니다.

먼저 docker-swarm 을 사용하기 위해서는 보안그룹 설정에 가셔서 아래처럼 포트를 열어주어야 합니다. 일단 저는 실습을 위해서 `0.0.0.0/0` 으로 모두 열었지만, 실 서비스에서는 프라이빗하게 쓰시면 될 것 같습니다. 또한 UDP 포트도 모두 열 필요는 없습니다.

<img src="/assets/hyperledger/network-1/hyperledger-network2_for_docker_port.png" alt="for_docker_port">

포트와 관련한 것은 docker-swarm 과 관련된 내용이므로, 자세히 궁금하신 분들은 구글 서치를 추천드립니다. 저는 간단하게 설명만 하고 넘어가도록 하겠습니다.
* TCP port 2377: manager 노드에서 listen 하고 있는 포트(cluster management 통신에 사용)
* TCP/UDP port 4789: Overlay Network 트래픽용 포트
* TCP/UDP port 7946: 컨테이너 네트워크 검색을 위한 포트(Node 간 통신에 사용)

보안그룹을 생성하셨으면 이제 Fabric instance 를 각각 생성하면 됩니다. 실습을 위한 instance 의 경우 `t3.micro` 정도는 되어야 chaincode 1개를 설치할 때 __out of memory__ 오류가 안뜨더군요

아래 처럼 EC2 인스턴스 5개를 `Ubuntu Server 16.04 LTS` 로  구성했습니다.

<img src="/assets/hyperledger/network-1/hyperledger-network3_for_aws_instance.png" alt="for_aws_instance">

이제 AWS 에서의 인스턴스 구성이 완료되었습니다!! Fabric 을 위한 환경설정을 시작하도록 하겠습니다.

## Fabric 구성을 위한 환경 구성하기

Fabric 을 설치하기 위해서는 여러가지 기본 세팅이 필요합니다.([Hyperledger Prerequisites](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html) 참고) 저희는 local 에서 genesisblock, cryptoconfig 등을 생성하고 이를 각각의 instance 에 넣어줄 예정입니다. 그러니 local 에서 go, node, python 등을 설치해 주시면 됩니다.

이런 기본 설치 방법의 경우에는 따로 설명을 드리지는 않겠습니다.

다만 주의할 점은 hyperledger fabric 1.4 를 사용하기 위해서는 반드시 `go version 1.11.x`, `nodejs version 8.9.x` 등 알맞은 버전을 사용해야만 합니다. 버전이 더 높거나 낮은 경우 알 수 없는 많은 오류를 내니 혹시 오류가 났을 때, 내가 설치한 버전이 맞는지 반드시 확인해보시기 바랍니다.

## Docker & Docker Swarm 세팅

Fabric 의 경우 docker 로 관리되고 있습니다. 따라서 각각의 instance 에는 docker 만 설치해주면 됩니다. 모든 인스턴스에서 아래의 명령어를 활용해 docker 를 설치합니다.

```ruby
# 패키지 업데이트
$ sudo apt-get update

# Docker 를 설치하기 위해 필요한 패키지
$ sudo apt-get install curl apt-transport-https ca-certificates software-properties-common

# keyserver 에서 GPG key 를 추가합니다.
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 

# Docker Repository 설정
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" 

# 새로 추가된 패키지 업데이트
$ sudo apt-get update

# Docker CE 설치
$ sudo apt-get install docker-ce
```

그럼 이제 다음 명령어로 docker 가 잘 설치되어 있는지 확인해 봅시다.

```ruby
$ docker --version
```

`version` 이 17.06.2 보다 높게 설정되어 있어야 에러가 안납니다.

그런데 저는 docker 를 사용할 때 `sudo` 명령어를 사용하지 않기 위해 아래 명령어로 docker 실행 그룹에 ubuntu 계정을 포함시켰습니다. 명령어를 친 후 `exit` 으로 터미널을 나가고 다시 접속해주시면 됩니다.

```ruby
$ sudo usermod -aG docker $(whoami)
```

혹시 docker-compose 가 설치되어 있지 않다면 이도 설치해 줍니다.

```ruby
# git 에서 버전에 맞게 설치
$ sudo curl -L https://github.com/docker/compose/releases/download/1.24.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 권한 부여
$ sudo chmod +x /usr/local/bin/docker-compose
```

똑같이 설치가 되었는지 확인해 봅시다

```ruby
$ docker-compose --version
```

docker-compose 의 경우 `version` 이 1.14.0 보다 높아야 합니다.

네 이렇게 하면 docker 의 설치가 끝났습니다.

다만 저희는 이를 docker-swarm 을 이용해서 관리할 예정이므로 docker-swarm 을 사용하기 위한 세팅을 해줄 필요가 있습니다. `instance5` 에 접속하여 `docker-swarm` 을 세팅해 줍니다.

먼저 docker-swarm 을 초기화 합니다. (ip 의 경우 private ip 에 맞게 설정해 주시기 바랍니다.)

```ruby
$ docker swarm init --advertise-addr {instance5 의 private ip}
```

위 명령어를 실행했을 경우 아래와 같은 결과를 얻게 되는데 

```
Swarm initialized: current node (oe3pm1c43cu1okgop6rz260yk) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-25a8wklcajxut1r4ipefbrmo614aa08drnvzdndxtzvyjzyem9-5bybpq4bwe1j0o7l1y9260pal 172.16.0.19:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

저기 있는 `docker swarm join --token {token 정보} {instance5 IP:2377}` 의 명령어를 `instance1~4` 에 그대로 입력해 줍니다. (참고로 포트 뒤를 잘 보시면 뒤에 2377 포트를 열고 있다는 것을 알 수 있습니다.)

```ruby
$ docker swarm join --token {token 정보} {instance5 IP:2377}
```

그럼 `This node joined a swarm as a worker.` 라는 말과 함께 docker swarm 의 노드로써 참여하게 됩니다.

이제 다시 `instance5` 로 와서 잘 연결되어 있는지 확인해 봅시다.

```bash
# 어떤 노드가 연결되어 있는지 확인
$ docker node ls
```

그럼 아래와 같이 노드가 연결되어 있으면 완료되었습니다.

<img src="/assets/hyperledger/network-1/hyperledger-network4_for_docker_swarm_complete.png" alt="for_docker_swarm_complete">

※ 혹시 토큰 정보를 잃어버린 경우 `docker swarm join-token {manager | worker}` 를 이용해서 찾으시면 됩니다.

※ 토큰 정보는 중요한 정보입니다. 지금은 실습을 이해하기 위해 공개했지만, 외부에 공개하면 안되는 정보입니다. 만약 노출되었다면 `docker swarm join-token  --rotate {manager | worker}` 를 이용해서 토큰을 변경할 수 있습니다.

사실 아직 끝이 아닙니다.... docker-swarm 을 사용하면 docker 로 띄워진 peer 들끼리 통신, orderer 와의 통신 등 docker-daemon 간의 network 를 설정해 주어야 합니다. 따라서 통신을 위한 `overlay network` 를 만들어 주도록 합니다. (instance5 에서 실행하도록 합니다.)

```ruby
$ docker network create --attachable --driver overlay fabric-samples
```

## Fabric Binary / Docker 파일 설치하기

cryptogen 등의 명령어를 사용하기 위해서 sample binary 파일을 설치해야 합니다. ([참고](https://hyperledger-fabric.readthedocs.io/en/release-1.4/install.html)) 여기 참고에 있는 파일을 받는 순서는 read doc 에는 다음과 같이 나와있습니다. 저희는 local 에서 이 작업을 진행할 예정이므로 local 에서 진행하도록 합니다.

1. If needed, clone the hyperledger/fabric-samples repository
2. Checkout the appropriate version tag
3. Install the Hyperledger Fabric platform-specific binaries and config files for the version specified into the /bin and /config directories of fabric-samples
4. Download the Hyperledger Fabric docker images for the version specified

2번까지 진행하고 `fabric-samples` 디렉토리에 가서 아래 명령어를 치시면 __binary 파일__ 과 __hyperledger docker__ 최신 파일을 받으실 수 있습니다.

```ruby
$ curl -sSL http://bit.ly/2ysbOFE | bash -s
```

만약 현재 fabric 버전이 다르다면 지금 저희는 1.4.1, couchdb 는 0.4.15 로 진행하고 있으므로 아래 명령어로 받으시면 됩니다.

각각의 버전은 `<fabric_version>`, `<fabric-ca_version>`, `<thirdparty_version>` 을 의미합니다.

```ruby
$ curl -sSL http://bit.ly/2ysbOFE | bash -s -- 1.4.1 1.4.1 0.4.15
```

이제 잘 받아졌는지 확이해보겠습니다. `fabric-samples/bin/cryptogen` 를 실행시켜 아래와 같이 잘 뜨는지 확인해보세요

<img src="/assets/hyperledger/network-1/hyperledger-network5_for_binary_complete.png" alt="for_binary_complete">

## 마무리

네!! 드디어 Fabric 네트워크를 구성하기 위한 AWS 와 docker/docker-swarm 세팅을 끝냈습니다. 고생하셨습니다.

사실 이번 강좌에서는 Fabric 보다는 docker 와 docker-swarm 세팅하는 데에 힘을 쓰게 되었군요.

다음 포스팅 부터는 본격적으로 Fabric 노드를 구성해 봅시다.