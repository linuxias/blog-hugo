+++
title = "kubeadm으로 kubernetes 설치 및 초기화"
+++

kubernetes를 설치하는 방법은 다양하지만 그 중 kubeadm을 이용한 방법을 간단히 정리한다.

먼저 설치하기 전 제약사항으로 kubernetes는 cpu core 2개 이상, Memory 2G 이상에서만 설치가 가능하다.
만약 AWS Freetier로 EC2를 사용하는 분들은 EC2 Instance를 무엇을 사용하는지 확인 할 필요가 있다.
(Freetier로 제공되는 t2.micro는 kubernetes 설치가 불가능하며 t3.small 이상의 Instance가 필요하다.)

kubeadm을 설치하기 전 저장소를 추가한다. 아래 명령어는 관리자 명령으로 수행한다.
```bash
#curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
#cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```

kubeadm을 설치한다.

```bash
$ wget -qO- get.docker.com | sh
$ sudo apt install -y kubelet kubeadm kubectl kubernetes-cni
```

Kubernetes 클러스터를 초기화 한다. 아래 명령어는 Kubernetes의 Control 노드에서 수행한다.
`{YOUR_IP}`는 Control 노드의 IP를 입력한다.
```bash
sudo kubeadm init --apiserver-advertise-address {YOUR_IP}--pod-network-cidr=192.168.0.0/16
```
