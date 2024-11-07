---
date: 2024-10-03 22:04:01
updatedAt: 2024-11-07 23:08:21
tags:
  - hugo_blog
  - Container
categories:
  - Container
title: 1- Container 정리
lastmod: 2024-11-07T14:08:47.005Z
---
# Container runtime

* 컨테이너가 호스트 시스템 내에서 작동할 수 있도록 하는 기본 소프트웨어
* 컨테이너 레지스트리에서 컨테이너 이미지를 가져오고, 수명주기를 관리하는 것부터 컨테이너를 실행하는 것까지의 모든 작업을 담당

> vs Container engine\
> Container engine은 이미지 배포, 오케스트레이션, 런타임 관리 등 수명주기를 훨씬 더 광범위하기 관리한느 시스템\
> Container runtime은 컨테이너 실행을 담당함

### 고수준

* 사용자와의 인터페이스 제공
* Process based
  * cri-o
    * Lightweight Container Runtime for Kubernetes
    * https://cri-o.io/
  * docker Engine
  * Podman
  * rtk(ended)
* Virtual Machine based
  * Kata
    * https://katacontainers.io/
  * Firecracker
    * https://firecracker-microvm.github.io/

### 저수준

* 컨테이너를 사용 가능하게 만들고,  실제로 unpacking, creating, starting, stopping함
* Runc
  * 리눅스에서 OCI spec을 따르는 container를 생성하고 실행하기 위한 CLI 도구
  * https://github.com/opencontainers/runc
* crun
  * https://github.com/containers/crun
* runhcs
  * https://github.com/Microsoft/hcsshim/tree/main/cmd/runhcs
* containerd
  * containerd는 고수준과 저수준 사이의 특징을 가지고있음
  * https://github.com/containerd/containerd

> 저수준의 container runtime은  고수준 container runtime에서 사용하므로 우리가 저수준 container runtime을 직접적으로 사용할 일은 없음

## Docker, Dockershim, k8s, Podman

* Docker도 containerd기반이라서 containerd+docker를 사용한다면 차이는 없음, 단, docker에서 cotainerd를 직접 사용하는 것이 아닌, dockershim을 사용하여 containerd를 관리

## Container에서 사용하는 기술

* cgroup v2
* namespaces
* chroot
* Linux capabilities
* Union Mount
  * Container image layer

## Open Container Initiative

* OCI에서 하는 프로젝트
  * runc
  * image-spec
  * distiribution-spec
  * runtime-spec
* https://opencontainers.org/

## CLI 명령도구

* crictl
  * k8s 컨테이너 관리 CLI명령어
  * https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md
* ctr
  * containerd 명령어

## LXC (Linux Container)

* 리눅스 컨테이너

## CRI(Container Runtime Interface)

* https://github.com/kubernetes/cri-api

## CNCF(CLOUD NATIVE COMPUTING FOUNDATION)

참고 문서

* https://www.cloudraft.io/blog/container-runtimes
* https://www.samsungsds.com/kr/insights/docker.html
* https://ikcoo.tistory.com/266#change-root-%EA%B2%A9%EB%A6%ACchroot%EB%9E%80?
* https://www.wiz.io/academy/container-runtimes
* https://velog.io/@whattsup\_kim/Linux-Kernel-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%A6%AC%EB%88%85%EC%8A%A4%EC%9D%98-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EA%B2%A9%EB%A6%AC-%EA%B8%B0%EB%8A%A5-cgroup-namespace-union-mount
