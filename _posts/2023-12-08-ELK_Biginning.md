---
layout: post
title:  ELK 스택 공부 스타또~
date:   2023-12-8 17:25:29 +0900
categories: etc
--- 

## ELK 각각의 역할
1) logstash
	- data의 형태, DBMS 등과 상관없이 data를 elasticSearch에 수집
2) kibana
	- Data visualization tool
	- ElasticSearch data를 화면에 시각화

## 환경 구성
- 이 프로젝트를 진행하며 백엔드 개발자로서 알아야할 기본 소양인 Kubernetes, Docker를 공부하기 위해 이 두가지를 이용하여 ELK stack 실습환경을 구성하고자 했다.

## Docker vs k8s(kubernetes) vs VM
여러 문서들을 읽은 결과 내가 이해한 바로는
- Docker는 컨테이너를 만드는 기술이고 컨테이너가 여러개일 경우 쿠버네티스로 매니징한다.
- 쿠버네티스는 컨테이너들을 모니터링 하고 사용자가 많아지면 컨테이너를 늘리고 적어지면 줄이는 듯 auto Scaling(?)도 해주는 등 이점이 많다.
- VM은 호스트OS를 사용하는 것이 아닌 guestOS를 사용하고 Docker는 호스트OS를 공유한다. 대신 namespace라는 것을 이용하여 host kernel과는 분리하는 듯하다. https://velog.io/@kdaeyeop/도커-Docker-와-VM의-차이 참조
- 하지만 리눅스가 아닌 Mac 또는 windows에서 Docker Desktop으로 구동할 경우 Docker engine이 리눅스 guestOS를 자동으로 구성하고 그 위에 도커를 올리는 개념.

실습환경으로 도커 데스크탑을 쓰려 함... 자동으로 리눅스를 게스트OS로 만들어주는게 편리하고, minicube와 달리 여러 클러스터로 확장 가능함.
-> 기동 중인 도커를 쿠버네티스 노드로 인식.

- https://soojae.tistory.com/87
- https://luvstudy.tistory.com/197
- 열심히 서치한 끝에 위 글들을 발견하여 진행할 수 있었다. 이 글이 없었다면....

kubernetes로 elk stack을 배포하기 위해 helm 사용.
### Helm 이란?
https://www.youtube.com/watch?v=m7iZtjeIHJw
영상 참조.
- 배포할 app이 많아질수록, 그리고 컨테이너마다 같은 app임에도 dev, qa 등 네이밍을 다르게 해야돼서 yaml 을 관리함에 어려움을 겪게됨
- kubectl로 많은 yaml을 배포하던 것에서 helm 으로 명령어 입력을 확 줄이 수 있음.
- 이를 해결하기 위해 helm chart template이라는 것을 이용하여 yaml을 템플릿화하여 파라미터로 dev, qa만 주고 동적으로 yaml을 만들 수 있게 도와주는 것이 Helm이다.
- 이런 장점 외에도 app(오프소스)을 배포하기 쉽게 오픈소스사가 helm으로 가이드를 구성해놨기 때문에 app 배포(설치)가 쉬워짐.

- kubectl get nodes
```
(base) jihye@ijihyeui-MacBookAir ~ % kubectl get nodes

NAME             STATUS   ROLES           AGE   VERSION

docker-desktop   Ready    control-plane   21h   v1.28.2
```
여기서 control-plane는 마스터 노드들로 구성된다.

Kubernetes on docker desktop은 unconfigurable하며 single-node cluster만을 지원한다.
저 control-plane(이 환경에서는 master node에 배포하는 형태를 띈다.)에 elk stack을 배포하게 된다. 	
	- node : k8s 클러스터 내 single server(instance)
		-> node 내에 여러 컨테이너 존재 가능.
	- pod : k8s에서 배포의 기본 단위 (하나의 컨테이너일 수도 여러개의 컨테이너일 수도 있다.)
	- cluster : 노드들의 집합. single node로 구성될 수 있다. single node로 클러스터가 구성될 경우 control plane(master node)만을 가진다
	- objects : 일시적이거나 영구적인 entities
		ex) pods, services, volumes, namespces, deployments, replica sets, jobs 등
	- manifests : objects를 정의하는 파일. 
		ex) YAML or JSON file
	- replica set : 실행되는 pods 개수를 정함.
		ex) 5개의 pods를 항상 실해하도록 설정하면 이후 pod  1개가 죽을 경우 다시 pod 1개를 실행하여 5개를 유지하도록 함.
	- values.yaml : templates에 사용되는 변수들을 정의한 파일. 일반적으로 key : value 형태로 작성됨.
	- ingress(인그레스) : 클러스터 외부에서 내부로 접근하는 요청들을 어떻게 처리할지 정의해둔 규칙들의 모음
		- 트래픽 로드밸런싱
		- SSL 인증서 처리
		- 도메인 기반 가상 호스팅 제공
		- 외부에서 접속 가능한 URL 사용
		위 기능 등을 정의하고 실제 동작하기 위해서 인그레스 컨트롤러 필요 -> nginx(웹서버 오픈소스)
- 
참조
https://medium.com/womenintechnology/create-a-kubernetes-cluster-using-docker-desktop-72b493f3faa8

1. heml 설치를 위한 홈브루 설치 
	https://m-ur-phy.tistory.com/entry/맥북-Homebrew-홈브류-설치하기
2. docker-desktop 설치 후 k8s 설치.
3. 홈브루 이용하여 helm 설치
4. helm을 이용하여 ELK stack 배포